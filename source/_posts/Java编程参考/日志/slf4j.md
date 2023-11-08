---
title: slf4j
date: 2023-11-09 00:59:34
categories: [Java编程参考]
tags: [Java编程参考-日志]
---

主要是引入 `slf4j-api` 和 `slf4j-simple` 包（可以替换为其他日志框架，比如`slf4j-log4j12`）

```xml
    <dependencies>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>2.0.9</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-simple</artifactId>
            <version>2.0.9</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-api</artifactId>
            <version>5.10.1</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.30</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
```

```java
@Slf4j
public class SOptional {

    @Test
    public void or() {
        log.info("ok");
    }

}
```

