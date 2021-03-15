---
layout: post
title: '자바 AES256 암호화 관련 에러'
description: 'AES256 암호화 사용시 java.security.InvalidKeyException: Illegal key size 해결 방법'
date: '2018-04-05 23:04'
categories:
  - Java
tags:
  - Programming
  - Java
  - 암호화
---

# Java의 제한적인 키 길이 정책
Java에서 `Cipher` 클래스를 이용해 AES256 암호화를 사용하면 `java.security.InvalidKeyException: Illegal key size` 라는 예외가 발생합니다. Java에서 기본적으로 128bit(16byte)로 키 길이를 제한해뒀고, 기본 키 길이를 초과하는 경우 이 예외가 발생합니다.

Java는 기본적으로 제한된 JCE(Java Cryptography Extension) 정책을 제공합니다.
Java 기본 정책은 `<JAVA_HOME>/jre/lib/security/local_policy.jar` 안의 `default_local.policy` 파일에서 확인할 수 있습니다. 아래는 해당 정책 파일의 내용입니다.

``` Java
grant {
    permission javax.crypto.CryptoPermission "DES", 64;
    permission javax.crypto.CryptoPermission "DESede", *;
    permission javax.crypto.CryptoPermission "RC2", 128,
                                     "javax.crypto.spec.RC2ParameterSpec", 128;
    permission javax.crypto.CryptoPermission "RC4", 128;
    permission javax.crypto.CryptoPermission "RC5", 128,
          "javax.crypto.spec.RC5ParameterSpec", *, 12, *;
    permission javax.crypto.CryptoPermission "RSA", *;
    permission javax.crypto.CryptoPermission *, 128;
};
```

위에 언급된 암호화 방식을 제외하고는 최대 128bit의 키 길이만 가능합니다. 길이에 제한을 둔 이유는 나라별로 수입 정책이 달라 사용 가능한 키 길이가 다르기 때문입니다.

# Unlimited Strength
Oracle에서는 길이 제한을 해제하고 싶은 사용자를 위해 JCE Unlimited Strength 정책 파일을 번들로 제공합니다.

## 방법
현재 사용 중인 Java버전에 맞춰 Unlimited Strength 정책 파일을 다운받습니다.

* [Java 6](https://www.oracle.com/kr/java/technologies/javase-jce6-downloads.html)
* [Java 7](https://www.oracle.com/kr/java/technologies/javase-jce7-downloads.html)
* [Java 8](https://www.oracle.com/kr/java/technologies/javase-jce8-downloads.html)

다운 받은 파일안의 `local_policy.jar`, `US_export_policy.jar` 파일을 `<JAVA_HOME>/jre/lib/security/` 폴더로 옮겨 기존 정책을 덮어씌웁니다. 그러면 JCE로 사용 가능한 모든 암호화의 키 길이 제한이 해제됩니다. <br/>
추가로 [8u151 Release Notes](https://www.oracle.com/java/technologies/javase/8u151-relnotes.html)에서는 해당 버전부터 별도의 다운로드없이 Unlimited Strength 정책을 설정할 수 있게 추가 번들이 같이 제공됩니다.  
해당 버전에서 정책 파일은 `<JAVA_HOME>/jre/lib/security/policy` 경로에 `limited`와 `unlimited` 폴더로 구성되며, unlimited 설정은 `<JAVA_HOME>/jre/lib/security/java.security` 파일을 열어 아래와 같은 부분을 찾아 주석 처리(`#`)를 지워주면 제한 해제된 정책을 사용할 수 있습니다.

``` Java
crypto.policy=unlimited
```

## 변경된 기본 정책
2018년 1월 업데이트된 [Java8u161 Release Notes](https://www.oracle.com/java/technologies/javase/8u161-relnotes.html)에 따르면 Java8u161 버전부터는 JCE 기본 정책이 Unlimited이며, 길이를 제한하고 싶다면 위에서 언급한 `java.security`파일에서 `crypto.policy`를 주석 처리하면 됩니다.
Unlimited를 기본으로 사용하는 Java 버전은 [JDK-8170157 : Enable unlimited cryptographic policy by default in Oracle JDK builds](https://bugs.java.com/view_bug.do?bug_id=JDK-8170157)에서 확인할 수 있습니다.

# 참고
* [Wikipedia - 미국 암호 수출](https://en.wikipedia.org/wiki/Export_of_cryptography_from_the_United_States)
