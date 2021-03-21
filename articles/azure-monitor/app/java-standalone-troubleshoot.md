---
title: Řešení potíží s Azure Monitor Application Insights pro Java
description: Informace o řešení potíží s agentem Java pro Azure Monitor Application Insights
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: f971466f25c2b7a4bd28e5b7eec6268f1b2e8b3d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103225570"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>Průvodce odstraňováním potíží: Azure Monitor Application Insights pro Java

V tomto článku se zaměříme na některé běžné problémy, které byste mohli využít při instrumentaci aplikace Java pomocí agenta Java pro Application Insights. Popisujeme také postup řešení těchto problémů. Application Insights je funkce služby Azure Monitor Platform.

## <a name="check-the-self-diagnostic-log-file"></a>Podívejte se na soubor protokolu, který je v samostatném diagnostickém

Ve výchozím nastavení agent Java 3,0 pro Application Insights vytvoří soubor protokolu s názvem `applicationinsights.log` ve stejném adresáři, ve kterém je uložený `applicationinsights-agent-3.0.2.jar` soubor.

Tento soubor protokolu je prvním místem, kde můžete vyhledat tipy pro případné problémy, se kterými se můžete setkat.

## <a name="jvm-fails-to-start"></a>JVM se nepodařilo spustit.

Pokud se JVM nepovede a spustí se chyba při otevírání souboru ZIP nebo chybějícího manifestu JAR, zkuste znovu stáhnout soubor JAR agenta, protože je možná poškozený během přenosu souborů.

## <a name="upgrade-from-the-application-insights-java-2x-sdk"></a>Upgrade z Application Insights Java 2. x SDK

Pokud už v aplikaci používáte sadu Application Insights Java 2. x SDK, můžete ji dál používat. Agent Java 3,0 ho detekuje. Další informace najdete v tématu [upgrade ze sady Java 2. x SDK](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-application-insights-java-30-preview"></a>Upgrade z verze Application Insights Java 3,0 Preview

Pokud upgradujete z agenta Java 3,0 Preview, pečlivě si Projděte všechny [Možnosti konfigurace](./java-standalone-config.md) . Struktura JSON se úplně změnila ve verzi 3,0 pro obecné dostupnosti (GA).

Mezi tyto změny patří:

-  Změnil se název konfiguračního souboru z `ApplicationInsights.json` na `applicationinsights.json` .
-  `instrumentationSettings`Uzel již není k dispozici. Veškerý obsah v nástroji `instrumentationSettings` je přesunut na kořenovou úroveň. 
-  Konfigurační uzly jako `sampling` , `jmxMetrics` , `instrumentation` a `heartbeat` jsou přesunuty z `preview` úrovně na kořenovou úroveň.

## <a name="some-logging-is-not-auto-collected"></a>Některé protokolování není automaticky shromážděno

Protokolování je zachyceno pouze v případě, že nejprve splňuje úroveň nakonfigurovanou pro protokolovací rozhraní a druhá také splňuje úroveň nakonfigurovanou pro Application Insights.

Pokud je například vaše protokolovací rozhraní nakonfigurované na protokolovat `WARN` (a vyšší) z balíčku `com.example` a Application Insights je nakonfigurované na zachycení `INFO` (a vyšší), Application Insights bude zachytit `WARN` (a výše) z balíčku `com.example` .

Nejlepším způsobem, jak zjistit, jestli konkrétní příkaz protokolování splňuje prahovou hodnotu nakonfigurovaných protokolovacích rozhraní, je potvrzení, že se zobrazuje v normálním aplikačním protokolu (např. soubor nebo konzola).

Všimněte si také, že pokud je objekt výjimky předán do protokolovacího nástroje, pak se zpráva protokolu (a podrobnosti objektu výjimky) zobrazí v Azure Portal v `exceptions` tabulce místo v `traces` tabulce.

Další podrobnosti najdete v tématu [Automatická shromážděná konfigurace protokolování](./java-standalone-config.md#auto-collected-logging) .

## <a name="import-ssl-certificates"></a>Importovat certifikáty SSL

Tato část vám pomůže vyřešit a případně opravit výjimky týkající se certifikátů SSL při použití agenta Java.

Pro řešení tohoto problému jsou k dispozici dvě různé cesty:
* Pokud používáte výchozí úložiště klíčů Java
* Pokud používáte vlastní úložiště klíčů Java

Pokud si nejste jisti, jakou cestu chcete sledovat, zkontrolujte, jestli máte JVM ARG `-Djavax.net.ssl.trustStore=...` .
Pokud takový ARG _JVM nemáte,_ budete pravděpodobně používat výchozí úložiště klíčů Java.
Pokud takový JVM _arg máte,_ budete pravděpodobně používat vlastní úložiště klíčů a v JVM ARG budete odkazovat na vlastní úložiště klíčů.

### <a name="if-using-the-default-java-keystore"></a>Pokud používáte výchozí úložiště klíčů Java:

Výchozí úložiště klíčů Java už bude mít všechny kořenové certifikáty certifikační autority. Může však dojít k nějakým výjimkám, například certifikát koncového bodu ingestování může být podepsán jiným kořenovým certifikátem. Proto pro vyřešení tohoto problému doporučujeme následující tři kroky:

1.  Ověřte, zda je kořenový certifikát použitý k podepsání Application Insightsho koncového bodu ve výchozím úložišti klíčů již přítomen. Certifikáty důvěryhodné certifikační autority jsou ve výchozím nastavení uloženy v `$JAVA_HOME/jre/lib/security/cacerts` . Chcete-li zobrazit seznam certifikátů v úložišti klíčů Java, použijte následující příkaz:
    > `keytool -list -v -keystore $PATH_TO_KEYSTORE_FILE`
 
    Výstup můžete přesměrovat na dočasný soubor (bude se snadno vyhledávat později).
    > `keytool -list -v -keystore $JAVA_HOME/jre/lib/security/cacerts > temp.txt`

2. Až budete mít seznam certifikátů, postupujte podle těchto [kroků](#steps-to-download-ssl-certificate) a stáhněte kořenový certifikát, který se použil k podepsání Application Insightsho koncového bodu.

    Po stažení certifikátu vygenerujte na certifikátu hodnotu hash SHA-1 pomocí příkazu níže:
    > `keytool -printcert -v -file "your_downloaded_root_certificate.cer"`
 
    Zkopírujte hodnotu SHA-1 a ověřte, jestli se tato hodnota nachází v souboru temp.txt, který jste předtím uložili.  Pokud nemůžete najít hodnotu SHA-1 v dočasném souboru, znamená to, že ve výchozím úložišti klíčů Java chybí stažený kořenový certifikát.


3. Naimportujte kořenový certifikát do výchozího úložiště klíčů Java pomocí následujícího příkazu:
    >   `keytool -import -file "the cert file" -alias "some meaningful name" -keystore "path to cacerts file"`
 
    V takovém případě bude
 
    > `keytool -import -file "your downloaded root cert file" -alias "some meaningful name" $JAVA_HOME/jre/lib/security/cacerts`


### <a name="if-using-a-custom-java-keystore"></a>Pokud používáte vlastní úložiště klíčů Java:

Pokud používáte vlastní úložiště klíčů Java, možná budete muset do něj naimportovat kořenových certifikátů SSL (Application Insights).
K vyřešení tohoto problému doporučujeme následující dva kroky:
1. Pomocí těchto [kroků](#steps-to-download-ssl-certificate) stáhnete kořenový certifikát z Application Insightsho koncového bodu.
2. Pomocí následujícího příkazu naimportujte kořenový certifikát SSL do vlastního úložiště klíčů Java:
    > `keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name.cer" -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-ssl-certificate"></a>Postup stažení certifikátu SSL

1.  Otevřete oblíbený prohlížeč a pokračujte na `IngestionEndpoint` adresu URL v připojovacím řetězci, který se používá k instrumentaci vaší aplikace.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png" alt-text="Snímek obrazovky zobrazující připojovací řetězec Application Insights" lightbox="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png":::

2.  Vyberte ikonu **Zobrazit informace o lokalitě** (uzamknout) v prohlížeči a pak vyberte možnost **certifikát** .

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Snímek obrazovky s možností certifikátu v informacích o lokalitě" lightbox="media/java-ipa/troubleshooting/certificate-icon-capture.png":::

3.  Místo stažení certifikátu list byste si měli stáhnout kořenový certifikát, jak je znázorněno níže. Později musíte kliknout na "cesta k certifikátu" – > vybrat kořenový certifikát-> klikněte na Zobrazit certifikát. Tím se otevře nabídka nový certifikát a můžete si stáhnout certifikát z nabídky nový.

    :::image type="content" source="media/java-ipa/troubleshooting/root-certificate-selection.png" alt-text="Snímek obrazovky s postupem výběru kořenového certifikátu" lightbox="media/java-ipa/troubleshooting/root-certificate-selection.png":::

4.  Přejít na kartu **Podrobnosti** a vyberte **Kopírovat do souboru**.
5.  Klikněte na tlačítko **Další** a vyberte **kódovaný znak X. 509 s kódováním Base-64 (. CER)** a pak znovu vyberte **Další** .

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="Snímek obrazovky Průvodce exportem certifikátu s vybraným formátem" lightbox="media/java-ipa/troubleshooting/certificate-export-wizard.png":::

6.  Zadejte soubor, do kterého chcete uložit certifikát SSL. Pak vyberte **Další**  >  **Dokončit**. Měla by se zobrazit zpráva o úspěšném exportu.

> [!WARNING]
> Chcete-li získat nový certifikát před vypršením platnosti aktuálního certifikátu, je nutné tento postup opakovat. Informace o vypršení platnosti najdete na kartě **Podrobnosti** v dialogovém okně **certifikát** .
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Snímek obrazovky zobrazující podrobnosti certifikátu SSL" lightbox="media/java-ipa/troubleshooting/certificate-details.png":::
