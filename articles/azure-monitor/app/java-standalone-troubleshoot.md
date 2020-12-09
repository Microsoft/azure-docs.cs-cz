---
title: Řešení potíží – Azure Monitor Application Insights Java
description: Řešení potíží s Azure Monitor Application Insights Java
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: cf27763f857cc1fd1aad5256d0c6cecf91251caf
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855619"
---
# <a name="troubleshooting-azure-monitor-application-insights-java"></a>Řešení potíží s Azure Monitor Application Insights Java

V tomto článku jsme pokryli některé běžné problémy, se kterými se uživatel může setkat při instrumentaci aplikace Java pomocí agenta Java společně s postupem, jak tyto problémy vyřešit.

## <a name="self-diagnostic-log-file"></a>Soubor protokolu samočinného diagnostiky

Ve výchozím nastavení Application Insights Java 3,0 vytvoří soubor protokolu s názvem `applicationinsights.log` ve stejném adresáři, ve kterém `applicationinsights-agent-3.0.0.jar` je soubor umístěný.

Tento soubor protokolu je prvním místem, kde můžete vyhledat tipy pro všechny problémy, se kterými se můžete setkat.

## <a name="upgrade-from-application-insights-java-2x-sdk"></a>Upgrade z Application Insights Java 2. x SDK

Viz [upgrade ze sady 2. x SDK](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-30-preview"></a>Upgrade z verze 3,0 Preview

Pokud upgradujete z verze 3,0 Preview, pečlivě si přečtěte všechny [Možnosti konfigurace](./java-standalone-config.md) , protože se struktura JSON úplně změnila ve verzi 3,0 GA.

Mezi tyto změny patří:

1.  Název konfiguračního souboru se změnil z `ApplicationInsights.json` na `applicationinsights.json` .
2.  `instrumentationSettings`Uzel již není k dispozici. Veškerý obsah v nástroji `instrumentationSettings` je přesunut na kořenovou úroveň. 
3.  Konfigurační uzly jako `sampling` , `jmxMetrics` `instrumentation` a `heartbeat` jsou přesunuty z `preview` kořenové úrovně.

## <a name="ssl-certificate-issues"></a>Problémy s certifikátem SSL

Pokud používáte výchozí úložiště klíčů Java, bude už mít všechny kořenové certifikáty certifikační autority a nemusíte naimportovat žádné další certifikáty SSL.

Pokud používáte vlastní úložiště klíčů Java, možná budete muset importovat do něj certifikáty Application Insights koncového bodu SSL.

### <a name="some-key-terminology"></a>Některá klíčová terminologie:
*Úložiště klíčů* je úložiště certifikátů, veřejných a privátních klíčů. JDK distribuce obvykle mají spustitelný soubor pro jejich správu – `keytool` .

Následující příklad představuje jednoduchý příkaz pro import certifikátu protokolu SSL do úložiště klíčů:

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-the-ssl-certificate"></a>Postup stažení a přidání certifikátu SSL:

1.  Otevřete oblíbený prohlížeč a `IngestionEndpoint` v připojovacím řetězci, který se používá k instrumentaci vaší aplikace, použijte adresu URL, jak je znázorněno níže.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="Připojovací řetězec Application Insights":::

2.  Klikněte na ikonu zobrazit informace o lokalitě (uzamknout) v prohlížeči a klikněte na možnost certifikát, jak je vidět níže.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Zachytávání certifikátů SSL":::

3.  Přejděte na kartu Podrobnosti a klikněte na Kopírovat do souboru.
4.  Klikněte na tlačítko Další a vyberte X. 509 s kódováním Base-64 (. CER) "formátovat a vybrat další.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="ExportWizard certifikátu SSL":::

5.  Zadejte soubor, do kterého chcete uložit certifikát SSL. Nakonec klikněte na další a dokončit. Měla by se zobrazit zpráva o úspěšném exportu.
6.  Jakmile certifikát naimportujete do úložiště klíčů Java, můžete certifikát naimportovat. Pomocí výše uvedeného [příkazu](#some-key-terminology) importujte certifikáty.

> [!WARNING]
> Tento postup se bude muset zopakovat, aby se nový certifikát dostal do vypršení platnosti aktuálního certifikátu. Informace o vypršení platnosti najdete na kartě Podrobnosti v překryvném okně certifikátu, jak je znázorněno níže.

:::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Podrobnosti certifikátu SSL":::
