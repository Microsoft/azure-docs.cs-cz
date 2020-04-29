---
title: Použití dynamické konfigurace v aplikaci pružinového spuštění
titleSuffix: Azure App Configuration
description: Naučte se dynamicky aktualizovat konfigurační data pro aplikace pro spouštění pružiny.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 3/5/2020
ms.author: lcozzens
ms.openlocfilehash: 37c832e3b6d1430da0b45558c9632f0486a7233b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "79216761"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Kurz: použití dynamické konfigurace v aplikaci Java pružiny

Klientská knihovna konfigurace aplikace pro spouštění nástroje podporuje aktualizaci sady nastavení konfigurace na vyžádání, aniž by způsobila restartování aplikace. Klientská knihovna ukládá do mezipaměti každé nastavení, aby nedocházelo k příliš velkému počtu volání do úložiště konfigurace. Operace aktualizace neaktualizuje hodnotu, dokud nevyprší platnost hodnoty uložené v mezipaměti, a to i v případě, že se hodnota v úložišti konfigurací změnila. Výchozí doba vypršení platnosti každé žádosti je 30 sekund. V případě potřeby ho můžete přepsat.

Můžete vyhledat aktualizované nastavení na vyžádání `AppConfigurationRefresh`voláním `refreshConfigurations()` metody.

Alternativně můžete použít `spring-cloud-azure-appconfiguration-config-web` balíček, který využívá závislost na `spring-web` zpracování automatizované aktualizace.

## <a name="use-automated-refresh"></a>Použít automatizovanou aktualizaci

Pokud chcete používat automatizovanou aktualizaci, začněte s aplikací pro spouštění pružiny, která používá konfiguraci aplikace, jako je třeba aplikace, kterou vytvoříte pomocí [rychlého startu při spuštění pro konfiguraci aplikací](quickstart-java-spring-app.md).

Pak otevřete soubor *pom. XML* v textovém editoru a přidejte `<dependency>` pro. `spring-cloud-azure-appconfiguration-config-web`

**Jarní Cloud 1.1. x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

**Jarní Cloud 1.2. x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

Uložte soubor a pak Sestavte a spusťte aplikaci obvyklým způsobem.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili aplikaci pro spouštění pružiny, která dynamicky aktualizuje nastavení konfigurace z konfigurace aplikace. Další informace o tom, jak používat spravovanou identitu Azure ke zjednodušení přístupu ke konfiguraci aplikace, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Spravovaná integrace identit](./howto-integrate-azure-managed-service-identity.md)
