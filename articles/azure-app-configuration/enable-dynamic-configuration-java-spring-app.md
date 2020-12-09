---
title: Použití dynamické konfigurace v aplikaci pružinového spuštění
titleSuffix: Azure App Configuration
description: Naučte se dynamicky aktualizovat konfigurační data pro aplikace pro spouštění pružiny.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 08/06/2020
ms.custom: devx-track-java
ms.author: alkemper
ms.openlocfilehash: c32e928bd4a83b4884c99e3ec3a9c647f5433e87
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929153"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Kurz: použití dynamické konfigurace v aplikaci Java pružiny

Klientská knihovna konfigurace aplikace pro spouštění nástroje podporuje aktualizaci sady nastavení konfigurace na vyžádání, aniž by způsobila restartování aplikace. Klientská knihovna ukládá do mezipaměti každé nastavení, aby nedocházelo k příliš velkému počtu volání do úložiště konfigurace. Operace aktualizace neaktualizuje hodnotu, dokud nevyprší platnost hodnoty uložené v mezipaměti, a to i v případě, že se hodnota v úložišti konfigurací změnila. Výchozí doba vypršení platnosti každé žádosti je 30 sekund. V případě potřeby ho můžete přepsat.

Můžete vyhledat aktualizované nastavení na vyžádání voláním `AppConfigurationRefresh` `refreshConfigurations()` metody.

Alternativně můžete použít `spring-cloud-azure-appconfiguration-config-web` balíček, který využívá závislost na `spring-web` zpracování automatizované aktualizace.

## <a name="use-automated-refresh"></a>Použít automatizovanou aktualizaci

Pokud chcete používat automatizovanou aktualizaci, začněte s aplikací pro spouštění pružiny, která používá konfiguraci aplikace, jako je třeba aplikace, kterou vytvoříte pomocí [rychlého startu při spuštění pro konfiguraci aplikací](quickstart-java-spring-app.md).

Pak otevřete soubor *pom.xml* v textovém editoru a přidejte `<dependency>` pro `spring-cloud-azure-appconfiguration-config-web` .

**Jarní Cloud 1.1. x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.5</version>
</dependency>
```

**Jarní Cloud 1.2. x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.7</version>
</dependency>
```

## <a name="run-and-test-the-app-locally"></a>Místní spuštění a otestování aplikace

1. Sestavte aplikaci Spring Boot pomocí Mavenu a spusťte ji.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Otevřete okno prohlížeče a použijte adresu URL: `http://localhost:8080` .  Zobrazí se zpráva přidružená k vašemu klíči. 

    Můžete také použít *oblé* k otestování aplikace, například: 
    
    ```cmd
    curl -X GET http://localhost:8080/
    ```

1. Pokud chcete otestovat dynamickou konfiguraci, otevřete portál Azure App Configuration Portal přidružený k vaší aplikaci. Vyberte **Průzkumník konfigurace** a aktualizujte hodnotu zobrazeného klíče, například:
    | Klíč | Hodnota |
    |---|---|
    | Application/config. Message | Hello – Aktualizováno |

1. Aktualizujte stránku prohlížeče, aby se zobrazila nová zpráva.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili aplikaci pro spouštění pružiny, která dynamicky aktualizuje nastavení konfigurace z konfigurace aplikace. Další informace o tom, jak používat spravovanou identitu Azure ke zjednodušení přístupu ke konfiguraci aplikace, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Spravovaná integrace identit](./howto-integrate-azure-managed-service-identity.md)
