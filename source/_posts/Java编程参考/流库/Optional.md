---
title: Optional
date: 2023-11-09 01:14:42
categories: [Java编程参考]
tags: [Java编程参考-流库]
---

```java
package org.jxch.study.stream;


import lombok.extern.slf4j.Slf4j;
import org.junit.jupiter.api.Test;

import java.util.Optional;
import java.util.function.Function;

/**
 * ofNullable, empty, of
 * orElse, orElseGet, orElseThrow
 * ifPresent, map
 * isPresent, get
 * flatMap
 *
 * @author 姜希成
 * @since 8.0
 */
@Slf4j
public class OptionalTest {

    @Test
    public void ofO() {
        // [main] INFO org.jxch.study.stream.SOptional - Optional.empty
        log.info("{}", Optional.ofNullable(null));
        // [main] INFO org.jxch.study.stream.SOptional - Optional.empty
        log.info("{}", Optional.empty());
        // [main] INFO org.jxch.study.stream.SOptional - Optional[str]
        log.info("{}", Optional.of("str"));
    }

    @Test
    public void orO() {
        //[main] INFO org.jxch.study.stream.SOptional - is null
        log.info("{}", Optional.ofNullable(null).orElse("is null"));
        //[main] INFO org.jxch.study.stream.SOptional - get string
        log.info("{}", Optional.ofNullable(null).orElseGet(() -> "get string"));
        // IllegalStateException
        log.info("{}", Optional.ofNullable(null).orElseThrow(IllegalStateException::new));
    }

    @Test
    public void ifO() {
        // ifPresent 无返回值
        // [main] INFO org.jxch.study.stream.SOptional - str
        Optional.ofNullable("str").ifPresent((str) -> log.info(str));
    }

    @Test
    public void mapO() {
        // [main] INFO org.jxch.study.stream.SOptional - Optional[true]
        log.info("{}", Optional.ofNullable("str").map(s -> s.equals("str")));
        // [main] INFO org.jxch.study.stream.SOptional - Optional.empty
        log.info("{}", Optional.ofNullable(null).map(s -> s.equals("str")));
    }

    @Test
    public void getO() {
        //[main] INFO org.jxch.study.stream.SOptional - false
        log.info("{}", Optional.ofNullable(null).isPresent());
        // NoSuchElementException
        log.info("{}", Optional.ofNullable(null).get());
    }

    @Test
    public void flatMapO() {
        Function<Double, Optional<Double>> calc =
                (value) -> Optional.of(value)
                        .flatMap(v -> v.equals(0.0) ? Optional.empty() : Optional.of(1 / v))
                        .flatMap(v -> Optional.of(v + 1));
        // [main] INFO org.jxch.study.stream.SOptional - Optional.empty
        log.info("{}", calc.apply(0.0));
        // [main] INFO org.jxch.study.stream.SOptional - Optional[1.25]
        log.info("{}", calc.apply(4.0));
    }

}

```

