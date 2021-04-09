---
title: Protokolování chyb a výjimek v MSAL pro jazyk Java
titleSuffix: Microsoft identity platform
description: Naučte se protokolovat chyby a výjimky v MSAL pro Java.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: d3fa13a6751b2d8acf1fc99aecbf174f1823fb0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954914"
---
# <a name="logging-in-msal-for-java"></a>Protokolování v MSAL pro Javu

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-java-logging"></a>MSAL pro protokolování Java

MSAL for Java umožňuje používat knihovnu protokolování, kterou už používáte s vaší aplikací, pokud je kompatibilní s SLF4J. MSAL for Java používá [jednoduché protokolování průčelí pro Java](http://www.slf4j.org/) (SLF4J) jako jednoduchou fasádu nebo abstrakci pro různá protokolovací rozhraní, jako je [Java. util. Logging](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html), [Logback](http://logback.qos.ch/) a [log4j](https://logging.apache.org/log4j/2.x/). SLF4J umožňuje uživateli připojit se k požadovanému rozhraní protokolování v době nasazení.

Například pro použití Logback jako protokolovacího rozhraní v aplikaci přidejte závislost Logback do souboru Maven pom pro vaši aplikaci:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Pak přidejte konfigurační soubor Logback:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

SLF4J se automaticky váže k Logback v době nasazení. Protokoly MSAL se zapíší do konzoly.

Pokyny k vytvoření vazby na další protokolovací rozhraní naleznete v [příručce SLF4J](http://www.slf4j.org/manual.html).

### <a name="personal-and-organization-information"></a>Osobní údaje a informace o organizaci

Ve výchozím nastavení protokolování MSAL nezachycuje ani neprotokoluje žádná osobní nebo organizační data. V následujícím příkladu je protokolování osobních nebo organizačních dat ve výchozím nastavení vypnuté:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

Zapněte možnost protokolování osobních a organizačních dat podle nastavení `logPii()` v Tvůrci klientských aplikací. Pokud zapnete protokolování osobních nebo organizačních dat, vaše aplikace musí převzít zodpovědnost za bezpečné zpracování vysoce citlivých dat a dodržování zákonných požadavků.

V následujícím příkladu je povoleno protokolování osobních nebo organizačních dat:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="next-steps"></a>Další kroky

Další ukázky kódu najdete v tématu [ukázky kódu Microsoft Identity Platform](sample-v2-code.md).