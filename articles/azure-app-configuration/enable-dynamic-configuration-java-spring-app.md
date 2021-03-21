---
title: Použití dynamické konfigurace v aplikaci pružinového spuštění
titleSuffix: Azure App Configuration
description: Naučte se dynamicky aktualizovat konfigurační data pro aplikace pro spouštění pružiny.
services: azure-app-configuration
author: mrm9084
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 12/09/2020
ms.custom: devx-track-java
ms.author: mametcal
ms.openlocfilehash: 076ab0bb7dbc85a31b626a24d977e6fea558143e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102636534"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Kurz: použití dynamické konfigurace v aplikaci Java pružiny

Konfigurace aplikace má dvě knihovny pro jarní. `spring-cloud-azure-appconfiguration-config` vyžaduje pružinové spouštění a má závislost na `spring-cloud-context` . `spring-cloud-azure-appconfiguration-config-web` vyžaduje jarní Web spolu s jarním spouštěním. Obě knihovny podporují ruční aktivaci pro kontrolu aktualizovaných hodnot konfigurace. `spring-cloud-azure-appconfiguration-config-web` také přidává podporu pro automatickou kontrolu konfigurace aktualizace.

Aktualizace umožňuje aktualizovat hodnoty konfigurace bez nutnosti restartovat aplikaci, i když dojde k opětovnému vytvoření všech fazolí `@RefreshScope` . Klientská knihovna ukládá do mezipaměti ID algoritmu hash aktuálně načtených konfigurací, aby nedocházelo k příliš velkému počtu volání do úložiště konfigurace. Operace aktualizace neaktualizuje hodnotu, dokud nevyprší platnost hodnoty uložené v mezipaměti, a to i v případě, že se hodnota v úložišti konfigurací změnila. Výchozí doba vypršení platnosti každé žádosti je 30 sekund. V případě potřeby ho můžete přepsat.

`spring-cloud-azure-appconfiguration-config-web`Automatizovaná aktualizace se spouští na základě aktivity, specificky jarního webu `ServletRequestHandledEvent` . Pokud `ServletRequestHandledEvent` se neaktivuje, `spring-cloud-azure-appconfiguration-config-web` automatizovaná aktualizace se neaktivuje, i když vypršela doba vypršení platnosti mezipaměti.

## <a name="use-manual-refresh"></a>Použít ruční aktualizaci

Vystavování konfigurace aplikace `AppConfigurationRefresh` , které se dá použít ke kontrole, jestli vypršela platnost mezipaměti a jestli vypršela její platnost, se spustí aktualizace.

```java
import com.microsoft.azure.spring.cloud.config.AppConfigurationRefresh;

...

@Autowired
private AppConfigurationRefresh appConfigurationRefresh;

...

public void myConfigurationRefreshCheck() {
    Future<Boolean> triggeredRefresh = appConfigurationRefresh.refreshConfigurations();
}
```

`AppConfigurationRefresh``refreshConfigurations()`vrátí hodnotu `Future` , která je true, pokud byla aktivována aktualizace a false, pokud ne. Hodnota false znamená, že doba vypršení platnosti mezipaměti nevypršela, nedošlo k žádné změně nebo jiné vlákno aktuálně kontroluje aktualizaci.

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
