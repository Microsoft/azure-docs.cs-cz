---
title: Řešení potíží se službou Live video Analytics v IoT Edge – Azure
description: Tento článek popisuje postup řešení potíží pro analýzy živých videí na IoT Edge.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 12/04/2020
ms.openlocfilehash: ee5ae7ca8b52d44f21c35df23ef92f61d38fc3c3
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051291"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Řešení potíží se službou Live video Analytics na IoT Edge

Tento článek popisuje postup řešení potíží pro LVA (Live video Analytics) na Azure IoT Edge.

## <a name="troubleshoot-deployment-issues"></a>Řešení problémů při nasazování

### <a name="diagnostics"></a>Diagnostika

Jako součást nasazení živé analýzy videí nastavujete prostředky Azure, jako jsou IoT Hub a IoT Edge zařízení. Jako první krok pro diagnostiku problémů vždy zajistěte, aby bylo hraniční zařízení správně nastavené pomocí následujících pokynů:

1. [Spusťte `check` příkaz](../../iot-edge/troubleshoot.md#run-the-check-command).
1. [Podívejte se na verzi IoT Edge](../../iot-edge/troubleshoot.md#check-your-iot-edge-version).
1. [Ověřte stav IoT Edge Security Manageru a jeho protokolech](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs).
1. [Prohlédněte si zprávy, které](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub)procházejí centrem centra IoT Edge.
1. [Restartujte kontejnery](../../iot-edge/troubleshoot.md#restart-containers).
1. [Ověřte bránu firewall a pravidla konfigurace portů](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules).

### <a name="pre-deployment-issues"></a>Problémy před nasazením

Pokud je hraniční infrastruktura v pořádku, můžete vyhledat problémy se souborem manifestu nasazení. Chcete-li nasadit Live video Analytics na IoT Edge modul na IoT Edge zařízení spolu s jinými moduly IoT, použijte manifest nasazení, který obsahuje IoT Edgeho centra, agenta IoT Edge a další moduly a jejich vlastnosti. K nasazení souboru manifestu můžete použít následující příkaz:

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```
Pokud kód JSON nemá správný formát, může se zobrazit následující chyba:   
&nbsp;&nbsp;&nbsp;**Nepodařilo se analyzovat JSON ze souboru: ' <deployment manifest.json> ' pro Argument ' Content ' s výjimkou: "extra data: řádek 101 sloupec 1 (znak 5325)"**

Pokud se zobrazí tato chyba, doporučujeme, abyste si vyhledáte ve formátu JSON chybějící závorky nebo jiné problémy se strukturou souboru. Chcete-li ověřit strukturu souborů, můžete použít klienta, jako je [Poznámkový blok](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) , nebo online nástroj, jako je například [formátovací modul JSON &](https://jsonformatter.curiousconcept.com/).

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>Během nasazování: Diagnostika pomocí přímých metod Media graphu 

Po správném nasazení nástroje Live video Analytics v modulu IoT Edge na zařízení IoT Edge můžete vytvořit a spustit mediální graf vyvoláním [přímých metod](direct-methods.md).  
>[!NOTE]
>  Volání přímé metody by se mělo provádět jenom v **`lvaEdge`** modulu.

Pomocí Azure Portal můžete spustit diagnostiku mediálního grafu pomocí přímých metod:

1. V Azure Portal přejdete do služby IoT Hub, která je připojená k vašemu zařízení IoT Edge.

1. Vyhledejte **automatickou správu zařízení** a pak vyberte **IoT Edge**.  

1. V seznamu hraničních zařízení vyberte zařízení, které chcete diagnostikovat.  
         
    ![Snímek obrazovky Azure Portal zobrazující seznam hraničních zařízení](./media/troubleshoot-how-to/lva-sample-device.png)


1. Zkontrolujte, zda je kód odpovědi *200 – OK*. Další kódy odpovědí pro modul [runtime IoT Edge](../../iot-edge/iot-edge-runtime.md) zahrnují:
    * 400 – konfigurace nasazení je poškozená nebo neplatná.
    * 417 – zařízení nemá nastavenou konfiguraci nasazení.
    * 412 – verze schématu v konfiguraci nasazení není platná.
    * 406 – IoT Edge zařízení je offline nebo neodesílá zprávy o stavu.
    * 500 – při IoT Edge modulu runtime došlo k chybě.

    > [!TIP]
    > Pokud dochází k potížím se spouštěním Azure IoT Edgech modulů ve vašem prostředí, použijte jako vodítko pro řešení potíží a diagnostiku **[Azure IoT Edge standardní diagnostické kroky](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** .
### <a name="post-deployment-direct-method-error-code"></a>Post Deployment: kód chyby přímé metody
1. Pokud se zobrazí stav `501 code` , zkontrolujte, zda je název přímé metody přesný. Pokud je název metody a datová část požadavku přesný, měli byste získat výsledky spolu s kódem úspěšnosti = 200. 
1. Pokud je datová část požadavku nepřesná, získáte stav `400 code` a datovou část odpovědi, která označuje kód chyby a zprávu, která by měla pomáhat s diagnostikou problému s přímým voláním metody.
    * Kontrola hlášených a požadovaných vlastností vám může pomáhat pochopit, jestli se vlastnosti modulu synchronizovaly s nasazením. Pokud ne, můžete restartovat zařízení IoT Edge. 
    * Použijte průvodce [přímými metodami](direct-methods.md) pro volání několika metod, zejména jednoduchých, jako je GraphTopologyList. Průvodce také určí očekávaná datová část požadavku a odpovědi a kódy chyb. Po úspěšném provedení jednoduchých přímých metod si můžete být jisti, že je modul IoT Edge pro analýzu videí v reálném čase funkčně v pořádku.
        
       ![Snímek obrazovky s podoknem "přímá metoda" pro modul IoT Edge](./media/troubleshoot-how-to/direct-method.png) 

1. Pokud se **ve sloupci nasazení** a **hlášené podle zařízení** zobrazí hodnota *Ano*, můžete vyvolat přímé metody v nástroji Live video Analytics v modulu IoT Edge. Vyberte modul, který chcete přejít na stránku, kde můžete kontrolovat požadované a hlášené vlastnosti a vyvolat přímé metody. Mějte na paměti následující: 

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>Po nasazení: diagnostikovat protokoly pro problémy při spuštění 

Protokoly kontejnerů pro váš modul IoT Edge by měly obsahovat diagnostické informace, které vám pomůžou s laděním vašich problémů během běhového modulu. V [protokolech kontejnerů](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) můžete vyhledat problémy a samy o sobě problém diagnostikovat. 

Pokud jste spustili všechny předchozí kontroly a stále dochází k problémům, Shromážděte protokoly ze zařízení IoT Edge [pomocí `support bundle` příkazu](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command) pro další analýzu týmu Azure. Můžete [nás kontaktovat](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) pro podporu a odeslat shromážděné protokoly.

## <a name="common-error-resolutions"></a>Řešení běžných chyb

Live video Analytics se nasadí jako modul IoT Edge v zařízení IoT Edge a spolupracuje s moduly agenta IoT Edge a moduly rozbočovače. Některé běžné chyby, ke kterým dojde s nasazením Live video Analytics, jsou způsobené problémy se základní infrastrukturou IoT. Mezi tyto chyby patří:

* [Agent IoT Edge se zastaví přibližně po minutě](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute).
* [Agent IoT Edge nemá přístup k imagi modulu (403)](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403).
* [Modul agenta IoT Edge nahlásí "prázdný konfigurační soubor" a žádné moduly na zařízení nezačínají](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
* [Nepodařilo se spustit centrum IoT Edge](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start).
* [Démon zabezpečení IoT Edge se nezdařil s neplatným názvem hostitele](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname).
* [Při živé analýze videa nebo jakémkoli jiném vlastním IoT Edge modulu se nepovede odeslat zpráva do centra Edge s 404 chybou](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
* [Modul IoT Edge se úspěšně nasazuje a pak zmizí ze zařízení](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device).

    > [!TIP]
    > Pokud dochází k potížím se spouštěním Azure IoT Edgech modulů ve vašem prostředí, použijte jako vodítko pro řešení potíží a diagnostiku **[Azure IoT Edge standardní diagnostické kroky](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** .

K problémům se můžete setkat také při spuštění **[skriptu pro nastavení prostředků nástroje Live video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)**. Mezi běžné problémy patří:

* Pomocí předplatného, ke kterému nemáte oprávnění vlastníka. Způsobí to, že se skript nezdařil s chybou **ForbiddenError** nebo **AuthorizationFailed** .
    * Pokud chcete tento problém obdržet, ujistěte se, že máte oprávnění **vlastníka** k předplatnému, které plánujete použít. Pokud to nemůžete udělat sami, obraťte se na správce předplatného, aby udělil správná oprávnění.
* **Nasazení šablony se nepovedlo kvůli porušení zásad.**
    * Pokud chcete tento problém vyřešit, obraťte se prosím na správce IT a ujistěte se, že volání, která mají vytvořit virtuální počítač, se budou používat k blokování ověřování SSH. Tato akce nebude nutná, protože používáme zabezpečenou bastionu síť, která pro komunikaci s prostředky Azure vyžaduje uživatelské jméno a heslo. Tyto přihlašovací údaje se uloží do souboru **~/clouddrive/lva-sample/vm-edge-device-credentials.txt** v Cloud Shell, jakmile se virtuální počítač úspěšně vytvoří, nasadí a připojí k IoT Hub.
* Instalační skript nemůže vytvořit instanční objekt a prostředky Azure.
    * Pokud chcete tento problém obdržet, zkontrolujte prosím, že vaše předplatné a tenant Azure nedosáhly svých maximálních limitů služeb. Přečtěte si další informace o [limitech a omezeních služby Azure AD](https://docs.microsoft.com/azure/active-directory/enterprise-users/directory-service-limits-restrictions) a omezeních [, kvótách a omezeních předplatného a služeb Azure.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)

> [!TIP]
> Pokud jsou k dispozici nějaké další problémy, se kterými budete možná potřebovat pomoc, **[Shromážděte prosím protokoly a odešlete lístek podpory](#collect-logs-for-submitting-a-support-ticket)**. Můžete nás taky kontaktovat odesláním e-mailu na adresu **[amshelp@microsoft.com](mailto:amshelp@microsoft.com)** .
### <a name="live-video-analytics-working-with-external-modules"></a>Live video Analytics pracuje s externími moduly

Live video Analytics můžou pomocí procesorů rozšíření pro multimediální graf rozšířit mediální graf tak, aby odesílal a přijímal data z jiných IoT Edge modulů pomocí protokolů HTTP nebo gRPC. V takovém [případě](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension)může tento mediální graf odesílat snímky videa jako obrázky do externího modulu odvození, jako je Yolo v3, a získávat výsledky analýzy založené na JSON pomocí protokolu HTTP. V takové topologii je cíl pro události většinou centrum IoT. V situacích, kdy se v centru nezobrazuje události odvození, zkontrolujte následující:

* Zkontrolujte, jestli se na rozbočovače, na který se bude Publisher graf nahrává, a zda je vaše centrum, které zkoumáte, stejné. Při vytváření více nasazení může dorazit k několika rozbočovačům a omylem kontrolovat chybné rozbočovače pro události.
* V části Azure Portal zkontrolujte, jestli je externí modul nasazený a spuštěný. V příkladu obrázku zde jsou rtspsim, yolov3, tinyyolov3 a logAnalyticsAgent IoT Edge moduly, které jsou spuštěny externě v modulu lvaEdge.

    [![Snímek obrazovky, který zobrazuje stav spuštěných modulů v Azure IoT Hub. ](./media/troubleshoot-how-to/iot-hub-azure.png)](./media/troubleshoot-how-to/iot-hub-azure.png#lightbox)

* Zkontrolujte, zda odesíláte události do správného koncového bodu adresy URL. Externí kontejner AI zpřístupňuje adresu URL a port, přes který obdrží, a vrátí data z požadavků POST. Tato adresa URL je zadána jako `endpoint: url` vlastnost pro procesor rozšíření http. Jak je vidět na [adrese URL topologie](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/2.0/topology.json), koncový bod je nastaven na parametr adresy URL Inferencing. Ujistěte se, že výchozí hodnota pro parametr nebo předaná hodnota je přesná. Můžete otestovat a zjistit, zda funguje pomocí adresy URL klienta (kudrlinkou).  

    Tady je příklad kontejner Yolo v3, který běží na místním počítači s IP adresou 172.17.0.3.  
    
    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Vrácený výsledek:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```
    > [!TIP]
    > K vyhledání IP adresy počítače použijte **[příkaz Docker prověřit](https://docs.docker.com/engine/reference/commandline/inspect/)** .
    
* Pokud používáte jednu nebo více instancí grafu, které používají procesor rozšíření Media Graph, měli byste `samplingOptions` pole použít ke správě míry snímků videa za sekundu. 

   * V některých situacích, kdy je procesor nebo paměť hraničního počítače vysoce využitý, můžete přijít o určité události odvození. Chcete-li tento problém vyřešit, nastavte pro `maximumSamplesPerSecond` vlastnost v poli nízkou hodnotu `samplingOptions` . Můžete ho nastavit na 0,5 ("maximumSamplesPerSecond": "0,5") na všech instancích grafu a pak znovu spustit instanci, abyste kontrolovali události odvození v centru.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Paralelní více přímých metod – Chyba časového limitu 

Live video Analytics na IoT Edge poskytuje přímý programovací model založený na metodách, který umožňuje nastavit několik topologií a více instancí grafů. Jako součást nastavení topologie a grafu vyvoláte více volání přímé metody v modulu IoT Edge. Pokud vyvoláte tyto vícenásobné volání metody paralelně, zejména ty, které spouštějí a zastavují grafy, může docházet k vypršení časového limitu, například následující: 

Metoda inicializace sestavení Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync vygenerovala výjimku. Microsoft. Azure. Devices. Common. Exceptions. IotHubException: Microsoft. Azure. Devices. Common. Exceptions. IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

Doporučujeme *, abyste přímé metody nevolali* paralelně. Zavolejte je sekvenčně (to znamená, že je třeba provést jedno přímé volání metody pouze po dokončení předchozího postupu).

### <a name="collect-logs-for-submitting-a-support-ticket"></a>Shromažďovat protokoly pro odeslání lístku podpory

Když kroky pro řešení potíží s vlastním průvodcem nevyřeší váš problém, přečtěte si Azure Portal a [otevřete lístek podpory](../../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!WARNING]
> Protokoly mohou obsahovat identifikovatelné osobní údaje (PII), jako je například vaše IP adresa. Všechny místní kopie protokolů se odstraní hned po dokončení jejich prověření a uzavření lístku podpory.  

Pokud chcete shromáždit příslušné protokoly, které by se měly přidat k lístku, postupujte podle pokynů níže v uvedeném pořadí a nahrajte soubory protokolů v podokně **podrobností** žádosti o podporu.  
1. [Konfigurace modulu Live video Analytics pro shromažďování podrobných protokolů](#configure-live-video-analytics-module-to-collect-verbose-logs)
1. [Zapnout protokoly ladění](#live-video-analytics-debug-logs)
1. Reprodukujte problém.
1. Připojení k virtuálnímu počítači ze stránky **IoT Hub** na portálu
    1. PSČ všechny soubory ve složce *debugLogs*

       > [!NOTE]
       > Tyto soubory protokolu nejsou určeny pro samostatnou diagnostiku. Jsou určeny pro technický tým Azure k analýze vašich problémů.

       * V následujícím příkazu Nezapomeňte nahradit **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** umístění protokolů ladění na hraničním zařízení, které jste nastavili dříve v **kroku 2**.  

           ```
           sudo apt install zip unzip  
           zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
           ```

    1. Připojte soubor *debugLogs.zip* k lístku podpory.
1. Spusťte [příkaz sady prostředků podpory](#use-the-support-bundle-command), Shromážděte protokoly a připojte se k lístku podpory.

### <a name="configure-live-video-analytics-module-to-collect-verbose-logs"></a>Konfigurace modulu Live video Analytics pro shromažďování podrobných protokolů
Nakonfigurujte modul Live video Analytics tak, aby shromáždil podrobné protokoly, a to nastavením `logLevel` a následujícím `logCategories` způsobem:
```
"logLevel": "Verbose",
"logCategories": "Application,Events,MediaPipeline",
```

Můžete to provést v těchto akcích:
* V **Azure Portal** aktualizujete ve vlastnostech nefunkčních vlastností identity modulu funkční vlastnosti [ ![ identity ](media/troubleshoot-how-to/module-twin.png) modulu](media/troubleshoot-how-to/module-twin.png#lightbox) Live video Analytics.    
* Případně můžete v souboru **manifestu nasazení** přidat tyto položky do uzlu Properties (Live video Analytics Module).

### <a name="use-the-support-bundle-command"></a>Použití příkazu support-komplet

Pokud potřebujete shromáždit protokoly ze zařízení IoT Edge, nejjednodušší způsob je použít `support-bundle` příkaz. Tento příkaz shromáždí:

- Protokoly modulů
- Protokoly IoT Edge Security Manager a modul kontejnerů
- IoT Edge výstup JSON
- Užitečné informace o ladění

1. Spusťte `support-bundle` příkaz s příznakem *--od* , který určuje, kolik času má vaše protokoly pokrýt. Například 2 – 2 – 2 obdrží protokoly za poslední dvě hodiny. Hodnotu tohoto příznaku můžete změnit tak, aby zahrnovala protokoly pro různá období.

    ```
    sudo iotedge support-bundle --since 2h
    ```

   Tento příkaz vytvoří soubor s názvem *support_bundle.zip* v adresáři, ve kterém jste spustili příkaz. 
   
1. Připojte soubor *support_bundle.zip* k lístku podpory.

### <a name="live-video-analytics-debug-logs"></a>Protokoly ladění Live video Analytics

Pokud chcete nakonfigurovat Live video Analytics v modulu IoT Edge tak, aby generovala protokoly ladění, udělejte toto:

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a pokračujte do služby IoT Hub.
1. V levém podokně vyberte **IoT Edge**.
1. V seznamu zařízení vyberte ID cílového zařízení.
1. V horní části podokna vyberte **nastavit moduly**.

   ![Snímek obrazovky s tlačítkem "nastavit moduly" v Azure Portal.](media/troubleshoot-how-to/set-modules.png)

1. V části **IoT Edge moduly** vyhledejte a vyberte **lvaEdge**.
1. Vyberte **kontejner možnosti vytvoření**.
1. V části **vazby** přidejte následující příkaz:

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > Tento příkaz váže složky protokolů mezi hraničním zařízením a kontejnerem. Pokud chcete protokoly shromáždit v jiném umístění, použijte následující příkaz a nahraďte **$LOG _LOCATION_ON_EDGE_DEVICE** umístění, které chcete použít: `/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. Vyberte **Aktualizovat**.
1. Vyberte **Zkontrolovat a vytvořit**. Pod zelenou bannerem se publikuje zpráva o úspěšném ověření.
1. Vyberte **Vytvořit**.
1. Aktualizujte **Nevlákennou identitu modulu** tak, aby odkazovala na parametr DebugLogsDirectory, který odkazuje na adresář, ve kterém jsou protokoly shromažďovány:

    a. V tabulce **moduly** vyberte **lvaEdge**.  
    b. V horní části podokna vyberte možnost **Nevlákenovaná identita modulu**. Otevře se upravitelné podokno.  
    c. V části **požadovaný klíč** přidejte následující dvojici klíč/hodnota:  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > Tento příkaz váže složky protokolů mezi hraničním zařízením a kontejnerem. Pokud chcete protokoly shromažďovat v jiném umístění v zařízení:
    > 1. Vytvořte vazbu pro umístění protokolu ladění v oddílu **Binds** a nahraďte **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** a **$Debug _LOG_LOCATION** umístění, které chcete: `/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`
    > 2. Použijte následující příkaz a nahraďte **$DEBUG _LOG_LOCATION** umístěním použitým v předchozím kroku:  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`  
    
    d. Vyberte **Uložit**.


1. Shromažďování protokolů můžete zastavit nastavením hodnoty v poli **Identita modulu** na *hodnotu null*. Vraťte se na stránku s **Nevlákenou identity modulu** a aktualizujte následující parametr jako:

    `"DebugLogsDirectory": ""`

### <a name="best-practices-around-logging"></a>Osvědčené postupy kolem protokolování

[Monitorování a protokolování](monitoring-logging.md) by mělo pomáhat při porozumění taxonomii a způsobu generování protokolů, které vám pomůžou s laděním problémů s lva. 

Protože implementace gRPC serveru se v různých jazycích liší, neexistuje standardní způsob přidávání protokolování do serveru.  

Pokud například sestavíte Server gRPC pomocí .NET Core, služba gRPC přidá do kategorie **gRPC** protokoly. Pokud chcete povolit podrobné protokoly z gRPC, nakonfigurujte předpony Grpc na úroveň ladění ve vašem appsettings.jsv souboru přidáním následujících položek do dílčí části LogLevel v protokolování: 

```
{ 
  "Logging": { 
    "LogLevel": { 
      "Default": "Debug", 
      "System": "Information", 
      "Microsoft": "Information", 
      "Grpc": "Debug" 
       } 
  } 
} 
``` 

Můžete ho taky nakonfigurovat v souboru Startup.cs pomocí ConfigureLogging: 

```
public static IHostBuilder CreateHostBuilder(string[] args) => 
    Host.CreateDefaultBuilder(args) 
        .ConfigureLogging(logging => 
        { 

           logging.AddFilter("Grpc", LogLevel.Debug); 
        }) 
        .ConfigureWebHostDefaults(webBuilder => 
        { 
            webBuilder.UseStartup<Startup>(); 
        }); 

``` 

[Protokolování a diagnostika v gRPC v rozhraní .NET](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1) poskytují pokyny pro shromažďování některých diagnostických protokolů ze serveru gRPC. 

### <a name="a-failed-grpc-connection"></a>Neúspěšné připojení gRPC 

Pokud je graf aktivní a streamuje z kamery, zachová se připojení ve službě Live video Analytics. 

### <a name="monitoring-and-balancing-the-load-of-cpu-and-gpu-resources-when-these-resources-become-bottlenecks"></a>Monitorování a vyrovnávání zatížení prostředků procesoru a GPU, když se tyto prostředky stanou kritickými body

Live video Analytics nesleduje ani neposkytuje žádné monitorování hardwarových prostředků. Vývojáři budou muset používat řešení monitorování výrobců hardwaru. Pokud ale používáte kontejnery Kubernetes, můžete zařízení monitorovat pomocí [řídicího panelu Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/). 

gRPC v dokumentech .NET Core také sdílí některé cenné informace o [osvědčených postupech výkonu](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1) a [Vyrovnávání zatížení](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1#load-balancing).  

### <a name="troubleshooting-an-inference-server-when-it-does-not-receive-any-frames-and-you-are-receiving-an-unknown-protocol-error"></a>Řešení potíží s odvozeným serverem, když neobdrží žádné rámce a přijímáte, Chyba protokolu "Neznámý". 

K dispozici je několik věcí, které vám pomohou získat další informace o problému.  

* Do požadovaných vlastností modulu Live video Analytics zahrňte kategorii protokolu **ediaPipeline** a zajistěte, aby byla úroveň protokolu nastavena na `Information` .  
* Pokud chcete otestovat připojení k síti, můžete z hraničního zařízení spustit následující příkaz. 

   ```
   sudo docker exec lvaEdge /bin/bash -c “apt update; apt install -y telnet; telnet <inference-host> <inference-port>” 
   ```

   Pokud příkaz vypíše krátký řetězec jumbled textu, program Telnet byl úspěšně schopný otevřít připojení k serveru odvození a otevřít binární kanál gRPC. Pokud to nevidíte, odešle protokol Telnet chybu sítě. 
* V rámci odvozeného serveru můžete povolit další protokolování v knihovně gRPC. To může poskytovat další informace o samotném kanálu gRPC. To se liší podle jazyka, tady jsou pokyny pro [C#](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1). 

### <a name="picking-more-images-from-buffer-of-grpc-without-sending-back-result-for-first-buffer"></a>Vybírání dalších imagí z vyrovnávací paměti gRPC bez odeslání výsledku zpět pro první vyrovnávací paměť

Jako součást smlouvy pro přenos dat gRPC by se měly potvrdit všechny zprávy, které posílá Live video Analytics na server gRPC Inferencing. Nepotvrzující příjem rámce obrázku přerušuje kontrakt dat a může mít za následek nepožadovanou situaci.  

Pokud chcete používat server gRPC s živými video analýzami, můžete k dosažení nejlepšího výkonu použít sdílenou paměť. To vyžaduje, abyste používali funkce sdílené paměti pro Linux zveřejněné programovacím jazykem nebo prostředím. 

1. Otevřete popisovač sdílené paměti systému Linux.
1. Po přijetí snímku přejděte na posun adresy v rámci sdílené paměti.
1. Potvrďte dokončení zpracování snímků, aby jeho paměť mohla být uvolněna živým analýzou videí.

   > [!NOTE]
   > Pokud se zpožděním přiřadíte příjem rámce do živé analýzy videí po dlouhou dobu, může dojít k úplnému zaplnění sdílené paměti a k poškození dat.
1. Uložte jednotlivé rámce do datové struktury podle svého výběru (seznam, pole a tak dále) na serveru Inferencing.
1. Logiku zpracování pak můžete spustit, když máte požadovaný počet snímků obrázku.
1. Až budete připraveni, vraťte výsledek Inferencing zpět do živé analýzy videí.

## <a name="next-steps"></a>Další kroky

[Kurz: nahrávání videa založeného na událostech do cloudu a přehrávání z cloudu](event-based-video-recording-tutorial.md)