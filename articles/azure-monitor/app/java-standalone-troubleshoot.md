---
title: Řešení potíží s Azure Monitor Application Insights pro Java
description: Informace o řešení potíží s agentem Java pro Azure Monitor Application Insights
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: 14644f76b7de53b2b6ee3f04131daaf59267a5ff
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507638"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>Průvodce odstraňováním potíží: Azure Monitor Application Insights pro Java

V tomto článku se zaměříme na některé běžné problémy, které byste mohli využít při instrumentaci aplikace Java pomocí agenta Java pro Application Insights. Popisujeme také postup řešení těchto problémů. Application Insights je funkce služby Azure Monitor Platform.

## <a name="check-the-self-diagnostic-log-file"></a>Podívejte se na soubor protokolu, který je v samostatném diagnostickém

Ve výchozím nastavení agent Java 3,0 pro Application Insights vytvoří soubor protokolu s názvem `applicationinsights.log` ve stejném adresáři, ve kterém je uložený `applicationinsights-agent-3.0.0.jar` soubor.

Tento soubor protokolu je prvním místem, kde můžete vyhledat tipy pro případné problémy, se kterými se můžete setkat.

## <a name="upgrade-from-the-application-insights-java-2x-sdk"></a>Upgrade z Application Insights Java 2. x SDK

Pokud už v aplikaci používáte sadu Application Insights Java 2. x SDK, můžete ji dál používat. Agent Java 3,0 ho detekuje. Další informace najdete v tématu [upgrade ze sady Java 2. x SDK](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-application-insights-java-30-preview"></a>Upgrade z verze Application Insights Java 3,0 Preview

Pokud upgradujete z agenta Java 3,0 Preview, pečlivě si Projděte všechny [Možnosti konfigurace](./java-standalone-config.md) . Struktura JSON se úplně změnila ve verzi 3,0 pro obecné dostupnosti (GA).

Mezi tyto změny patří:

-  Změnil se název konfiguračního souboru z `ApplicationInsights.json` na `applicationinsights.json` .
-  `instrumentationSettings`Uzel již není k dispozici. Veškerý obsah v nástroji `instrumentationSettings` je přesunut na kořenovou úroveň. 
-  Konfigurační uzly jako `sampling` , `jmxMetrics` , `instrumentation` a `heartbeat` jsou přesunuty z `preview` úrovně na kořenovou úroveň.

## <a name="some-logging-is-not-auto-collected"></a>Některé protokolování není automaticky shromážděno

Protokolování je zachyceno pouze v případě, že nejprve splňuje prahovou hodnotu nakonfigurovanou rozhraní protokolování a druhá také splňuje Application Insights nakonfigurovanou prahovou hodnotu.

Nejlepším způsobem, jak zjistit, jestli konkrétní příkaz protokolování splňuje prahovou hodnotu nakonfigurovaných protokolovacích rozhraní, je potvrzení, že se zobrazuje v normálním aplikačním protokolu (např. soubor nebo konzola).

Další podrobnosti najdete v tématu [Automatická shromážděná konfigurace protokolování](./java-standalone-config.md#auto-collected-logging) .

## <a name="import-ssl-certificates"></a>Importovat certifikáty SSL

Pokud používáte výchozí úložiště klíčů Java, bude už mít všechny kořenové certifikáty certifikační autority. Nemusíte potřebovat naimportovat víc certifikátů SSL.

Pokud používáte vlastní úložiště klíčů Java, možná budete muset importovat do něj certifikáty Application Insights koncového bodu SSL.

### <a name="key-terminology"></a>Klíčová terminologie
*Úložiště klíčů* je úložiště certifikátů, veřejných klíčů a privátních klíčů. Distribuce sady Java Development Kit obvykle obsahuje spustitelný soubor, který je spravuje: `keytool` .

Následující příklad představuje jednoduchý příkaz pro import certifikátu protokolu SSL do úložiště klíčů:

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-an-ssl-certificate"></a>Postup stažení a přidání certifikátu SSL

1.  Otevřete oblíbený prohlížeč a pokračujte na `IngestionEndpoint` adresu URL v připojovacím řetězci, který se používá k instrumentaci vaší aplikace.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="Snímek obrazovky zobrazující připojovací řetězec Application Insights":::

2.  Vyberte ikonu **Zobrazit informace o lokalitě** (uzamknout) v prohlížeči a pak vyberte možnost **certifikát** .

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Snímek obrazovky s možností certifikátu v informacích o lokalitě":::

3.  Přejít na kartu **Podrobnosti** a vyberte **Kopírovat do souboru**.
4.  Klikněte na tlačítko **Další** a vyberte **kódovaný znak X. 509 s kódováním Base-64 (. CER)** a pak znovu vyberte **Další** .

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="Snímek obrazovky Průvodce exportem certifikátu s vybraným formátem":::

5.  Zadejte soubor, do kterého chcete uložit certifikát SSL. Pak vyberte **Další**  >  **Dokončit**. Měla by se zobrazit zpráva o úspěšném exportu.
6.  Po vytvoření certifikátu je čas importovat certifikát do úložiště klíčů Java. K importu certifikátů použijte [předchozí příkaz](#key-terminology) .

> [!WARNING]
> Chcete-li získat nový certifikát před vypršením platnosti aktuálního certifikátu, je nutné tento postup opakovat. Informace o vypršení platnosti najdete na kartě **Podrobnosti** v dialogovém okně **certifikát** .
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Snímek obrazovky zobrazující podrobnosti certifikátu SSL":::
