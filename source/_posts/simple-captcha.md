---
title: JAVA生成简单的图形验证码（上）-- 生成验证码图片
date: 2017-06-21 19:21:36
tags:
    - 验证码
    - captcha
    - java
categories:
    - java & javascript
---

JAVA生成简单的图形验证码（上）-- 生成验证码图片

没啥可说的直接上代码了

说明：CaptchaConfig.java这个类是非必须的，其中的配置可以在核心类中写成固定的值

### CaptchaConfig.java ( 图形的配置)
```java
import org.apache.commons.lang3.StringUtils;

import java.util.Properties;

public class CaptchaConfig {

    private int width = 120;    // 图片的宽度
    private int height = 36;    // 图片的高度

    private int length = 4;     // 验证码长度
    private int fontSize = 20;  // 验证码的字体大小

    public static CaptchaConfig getDefault() {
        return DEFAULT;
    }

    // ....
    // Getter/Setter省略

    private static final String CAPTCHA_CONFIG_PATH = "/captcha.properties";

    private static final CaptchaConfig DEFAULT = new CaptchaConfig();

    /**
     * 读取验证码的配置（可要可不要）
     */
    static {
        Properties properties = new Properties();
        try {
            properties.load(CaptchaConfig.class.getResourceAsStream(CAPTCHA_CONFIG_PATH));
            String h = properties.getProperty("height");
            String w = properties.getProperty("width");
            String l = properties.getProperty("length");
            String fs = properties.getProperty("font_size");
            if (StringUtils.isNotBlank(h)) {
                DEFAULT.height = Integer.parseInt(h);
            }
            if (StringUtils.isNotBlank(w)) {
                DEFAULT.width = Integer.parseInt(w);
            }
            if (StringUtils.isNotBlank(l)) {
                DEFAULT.length = Integer.parseInt(l);
            }
            if (StringUtils.isNotBlank(fs)) {
                DEFAULT.fontSize = Integer.parseInt(fs);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

### ImageCaptcha.java(生成验证码的核心类)

```java
import javax.imageio.ImageIO;
import java.awt.*;
import java.awt.image.BufferedImage;
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.io.OutputStream;
import java.util.Random;

public class ImageCaptcha {

    /**
     * 显示的字符（为了识别方便，去掉了0和o)
     */
    private static final char[] chars = {
            'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'p', 'q', 'r', 's'
            , 't', 'u', 'v', 'w', 'x', 'y', 'z', '1', '2', '3', '4', '5', '6', '7', '8', '9'
    };

    private char[] value;

    private CaptchaConfig config;

    private Random random = new Random();

    private int startX, startY;     // 字符起始的位置

    private BufferedImage image;    // 生成的验证码图片对象

    private boolean created = false;    // 标识验证码是否已经创建

    public ImageCaptcha(CaptchaConfig config) {
        this.config = config;
        value = new char[config.getLength()];
        startX = config.getWidth() / (config.getLength() + 2);    // 每个字的间隔一样
        startY = config.getHeight() - (config.getHeight() - config.getFontSize()) / 2;   // 垂直居中
    }

    /**
     * 创建验证码
     */
    public void create() {
        // 创建图片对象
        image = new BufferedImage(config.getWidth(), config.getHeight(), BufferedImage.TYPE_INT_RGB);
        // 创建画笔
        Graphics2D graphics2D = image.createGraphics();
        // 设置背景颜色
        graphics2D.setColor(Color.lightGray);
        Font font = new Font(Font.SANS_SERIF, Font.PLAIN, config.getFontSize());
        // 设置字体
        graphics2D.setFont(font);
        // 填充矩形
        graphics2D.fillRect(0, 0, config.getWidth(), config.getHeight());
        // 绘制干扰线
        graphics2D.setColor(Color.black);
        for (int i = 0; i < 16; i++) {
            int offsetX = random.nextInt(12), offsetY = random.nextInt(12);   // 干扰线偏移量
            int x = random.nextInt(config.getWidth());
            int y = random.nextInt(config.getHeight());
            graphics2D.drawLine(x, y, x + offsetX, y + offsetY);
        }
        // 绘制验证码
        int r, g, b;        // RGB色值，随机的颜色
        String temp;
        for (int i = 0; i < config.getLength(); i++) {
            char c = randomChar();
            value[i] = c;
            temp = String.valueOf(c);
            r = random.nextInt(255);
            g = random.nextInt(255);
            b = random.nextInt(255);
            graphics2D.setColor(new Color(r, g, b));
            graphics2D.drawString(temp, (i + 1) * startX, startY);
        }
        created = true;
    }

    /**
     * 得到图片的字节流
     */
    public byte[] getImageData() throws IOException {
        checkState();
        ByteArrayOutputStream out = null;
        try {
            out = new ByteArrayOutputStream();
            writeTo(out);
            return out.toByteArray();
        } finally {
            if (out != null) {
                out.flush();
                out.close();
            }
        }
    }

    /**
     * 将图片输出到输出流上
     *
     * @param outputStream 输出流对象
     * @throws IOException 写入图片出错时
     */
    public void writeTo(OutputStream outputStream) throws IOException {
        if (outputStream == null) {
            throw new IllegalArgumentException("输出对象不能为空");
        }
        checkState();
        ImageIO.write(image, "jpeg", outputStream);
    }

    /**
     * 生成随机的字符
     *
     * @return chars中的字符
     */
    private char randomChar() {
        return chars[random.nextInt(33)];
    }

    public BufferedImage getImage() {
        checkState();
        return image;
    }

    public String getCaptchaString() {
        checkState();
        return new String(value);
    }

    /**
     * 检查验证码是否已经创建
     */
    private void checkState() {
        if (!created) {
            throw new IllegalStateException("验证码未创建,请先调用create方法");
        }
    }
}
```

在初始化startY时,还是没搞明白坐标轴是设置的，希望好心人士告知！！！

### 测试

```java
import org.junit.Test;

import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;

public class ImageCaptchaTest {

    @Test
    public void generateCaptchaTest() throws IOException {
        CaptchaConfig captchaConfig = CaptchaConfig.getDefault();
        ImageCaptcha captcha = new ImageCaptcha(captchaConfig);
        captcha.create();
        File file = new File("F:/java/captchaTest.jpg");
        if (file.exists()) {
            file.delete();
        }
        file.createNewFile();
        FileOutputStream outputStream = new FileOutputStream(file);
        System.out.println(captcha.getCaptchaString());
        captcha.writeTo(outputStream);
    }
}
```

### 参考文章

[java web项目生成验证码的解决方案](http://blog.csdn.net/chenghui0317/article/details/12526439)