---
title: Použití dynamické konfigurace v aplikaci Spring Boot
titleSuffix: Azure App Configuration
description: Přečtěte si, jak dynamicky aktualizovat konfigurační data pro aplikace spring boot
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 3/5/2020
ms.author: lcozzens
ms.openlocfilehash: 37c832e3b6d1430da0b45558c9632f0486a7233b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79216761"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Kurz: Použití dynamické konfigurace v aplikaci Java Spring

Klientská knihovna Application Configuration Spring Boot podporuje aktualizaci sady nastavení konfigurace na vyžádání, aniž by došlo k restartování aplikace. Klientská knihovna ukládá každé nastavení do mezipaměti, aby se zabránilo příliš mnoha voláním do úložiště konfigurace. Operace aktualizace neaktualizuje hodnotu, dokud nevyprší platnost hodnoty uložené v mezipaměti, a to ani v případě, že se hodnota v úložišti konfigurace změnila. Výchozí doba vypršení platnosti pro každý požadavek je 30 sekund. V případě potřeby jej lze přepsat.

Aktualizované nastavení můžete zkontrolovat na `AppConfigurationRefresh`vyžádání `refreshConfigurations()` voláním metody 's.

Alternativně můžete použít `spring-cloud-azure-appconfiguration-config-web` balíček, který trvá `spring-web` závislost na zpracování automatické aktualizace.

## <a name="use-automated-refresh"></a>Použití automatické aktualizace

Chcete-li používat automatickou aktualizaci, začněte s aplikací Spring Boot, která používá konfiguraci aplikace, například aplikaci, kterou vytvoříte, pomocí [rychlého startu jarního spuštění pro konfiguraci aplikace](quickstart-java-spring-app.md).

Potom otevřete soubor *pom.xml* v textovém `<dependency>` editoru a přidejte pro `spring-cloud-azure-appconfiguration-config-web`.

**Jarní mrak 1.1.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

**Jarní mrak 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

Uložte soubor a pak vytvořte a spusťte aplikaci obvyklým způsobem.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili aplikaci Spring Boot dynamicky aktualizovat nastavení konfigurace z konfigurace aplikace. Chcete-li se dozvědět, jak používat spravovanou identitu Azure ke zjednodušení přístupu ke konfiguraci aplikací, pokračujte dalším kurzem.

> [!div class="nextstepaction"]
> [Integrace spravované identity](./howto-integrate-azure-managed-service-identity.md)
