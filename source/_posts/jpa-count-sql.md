---

title: Spring Jpa执行分页count sql的时机
date: 2018-07-18 17:15:55
tags:
    - spring
    - spring-data-jpa
    - jpa
categories:
    - java
    - spring

---

在使用JPA的时候发现有的时候jpa并没有去执行total的查询语句，但还是返回了`totalElements`这个字段, 处于好奇，于是查了一波源码.
<!-- more -->
```java
// org.springframework.data.jpa.repository.query.JpaQueryExecution#PagedExecution
/**
 * Executes the {@link AbstractStringBasedJpaQuery} to return a
 * {@link org.springframework.data.domain.Page} of entities.
 */
static class PagedExecution extends JpaQueryExecution {

	private final Parameters<?, ?> parameters;

	public PagedExecution(Parameters<?, ?> parameters) {

		this.parameters = parameters;
	}

	@Override
	@SuppressWarnings("unchecked")
	protected Object doExecute(final AbstractJpaQuery repositoryQuery, final Object[] values) {
		ParameterAccessor accessor = new ParametersParameterAccessor(parameters, values);
		Query query = repositoryQuery.createQuery(values);

		return PageableExecutionUtils.getPage(query.getResultList(), accessor.getPageable(),
				() -> count(repositoryQuery, values));
	}

	private long count(AbstractJpaQuery repositoryQuery, Object[] values) {

		List<?> totals = repositoryQuery.createCountQuery(values).getResultList();
		return (totals.size() == 1
		    ? CONVERSION_SERVICE.convert(totals.get(0), Long.class)
		    : totals.size());
	}
}
```

上面`PagedExecution`这个类是用于执行分页查询的Jpa执行器，`count`方法用于查询总条数，注意到这一行：
```java
PageableExecutionUtils.getPage(query.getResultList(), accessor.getPageable(),
                () -> count(repositoryQuery, values))
```
第一个参数是查询的结果，第二个是传入的分页参数，第三个参数是**获取总记录条数的方法**，不是直接去查询总条数，我们来看看`PageableExecutionUtils.getPage`这个方法:

```java
// org.springframework.data.repository.support.PageableExecutionUtils
/**
 * Constructs a {@link Page} based on the given {@code content}, {@link Pageable} and {@link Supplier} applying
 * optimizations. The construction of {@link Page} omits a count query if the total can be determined based on the
 * result size and {@link Pageable}.
 *
 * @param content must not be {@literal null}.
 * @param pageable must not be {@literal null}.
 * @param totalSupplier must not be {@literal null}.
 * @return the {@link Page}.
 */
public static <T> Page<T> getPage(List<T> content, Pageable pageable, LongSupplier totalSupplier) {

	Assert.notNull(content, "Content must not be null!");
	Assert.notNull(pageable, "Pageable must not be null!");
	Assert.notNull(totalSupplier, "TotalSupplier must not be null!");

	if (pageable.isUnpaged() || pageable.getOffset() == 0) {

		if (pageable.isUnpaged() || pageable.getPageSize() > content.size()) {
			return new PageImpl<>(content, pageable, content.size());
		}

		return new PageImpl<>(content, pageable, totalSupplier.getAsLong());
	}

	if (content.size() != 0 && pageable.getPageSize() > content.size()) {
		return new PageImpl<>(content, pageable, pageable.getOffset() + content.size());
	}

	return new PageImpl<>(content, pageable, totalSupplier.getAsLong());
}
```

注意看第三个参数`totalSupplier`(戳此处了解[Supplier](https://docs.oracle.com/javase/8/docs/api/java/util/function/Supplier.html))的使用时机，就可以知道，以下两种情况下将不会使用`totalSupplier`：

1. 没有分页信息
2. 当前查询结果的记录条数小于pageSize(页大小)