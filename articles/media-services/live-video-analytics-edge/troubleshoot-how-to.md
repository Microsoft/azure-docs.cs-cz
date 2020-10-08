---
title: Řešení potíží se službou Live video Analytics v IoT Edge – Azure
description: Tento článek popisuje postup řešení potíží pro analýzy živých videí na IoT Edge.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: c297a189f3b13ca8e72daf4eef009bc28fac32bf
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91823201"
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

Pokud je hraniční infrastruktura v pořádku, můžete vyhledat problémy se souborem manifestu nasazení. Chcete-li nasadit Live video Analytics na IoT Edge modul na IoT Edge zařízení spolu s jinými moduly IoT, použijte manifest nasazení, který obsahuje IoT Edgeho centra, agenta IoT Edge a další moduly a jejich vlastnosti. Pokud kód JSON nemá správný formát, může se zobrazit následující chyba: 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

Nepodařilo se analyzovat JSON ze souboru: ' <deployment manifest.json> ' pro Argument ' Content ' s výjimkou: "extra data: řádek 101 sloupec 1 (znak 5325)"

Pokud se zobrazí tato chyba, doporučujeme, abyste si vyhledáte ve formátu JSON chybějící závorky nebo jiné problémy se strukturou souboru. Chcete-li ověřit strukturu souborů, můžete použít klienta, jako je [Poznámkový blok](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) , nebo online nástroj, jako je například [formátovací modul JSON &](https://jsonformatter.curiousconcept.com/).

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>Během nasazování: Diagnostika pomocí přímých metod Media graphu 

Po správném nasazení nástroje Live video Analytics v modulu IoT Edge na zařízení IoT Edge můžete vytvořit a spustit mediální graf vyvoláním [přímých metod](direct-methods.md). Pomocí Azure Portal můžete spustit diagnostiku mediálního grafu prostřednictvím přímých metod:

1. V Azure Portal přejdete do služby IoT Hub, která je připojená k vašemu zařízení IoT Edge.

1. Vyhledejte **automatickou správu zařízení**a pak vyberte **IoT Edge**.  

1. V seznamu hraničních zařízení vyberte zařízení, které chcete diagnostikovat.  
         
    ![Snímek obrazovky Azure Portal zobrazující seznam hraničních zařízení](./media/troubleshoot-how-to/lva-sample-device.png)

1. Zkontrolujte, zda je kód odpovědi *200 – OK*. Další kódy odpovědí pro modul [runtime IoT Edge](../../iot-edge/iot-edge-runtime.md) zahrnují:
    * 400 – konfigurace nasazení je poškozená nebo neplatná.
    * 417 – zařízení nemá nastavenou konfiguraci nasazení.
    * 412 – verze schématu v konfiguraci nasazení není platná.
    * 406 – IoT Edge zařízení je offline nebo neodesílá zprávy o stavu.
    * 500 – při IoT Edge modulu runtime došlo k chybě.

1. Pokud obdržíte stav 501, zkontrolujte, zda je název přímé metody přesný. Pokud je název metody a datová část požadavku přesný, měli byste získat výsledky spolu s kódem úspěšnosti = 200. Pokud je datová část požadavku nepřesná, získáte stav = 400 a datovou část odpovědi, která označuje kód chyby a zprávu, která by měla pomáhat s diagnostikou problému pomocí přímé volání metody.
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

### <a name="edge-setup-script-issues"></a>Nastavení Edge – problémy se skripty

V rámci naší dokumentace jsme poskytli [instalační skript](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) pro nasazení hraničních a cloudových prostředků a začali začít se službou Live video Analytics Edge. V této části jsou uvedeny některé chyby skriptu, se kterými se můžete setkat, spolu s řešeními pro jejich ladění.

Problém: skript se spustí, částečně se vytvoří několik prostředků, ale dojde k chybě s následující zprávou:

```
registering device...

Unable to load extension 'eventgrid: unrecognized kwargs: ['min_profile']'. Use --debug for more information.
The command failed with an unexpected error. Here is the traceback:

No module named 'azure.mgmt.iothub.iot_hub_client'
Traceback (most recent call last):
File "/opt/az/lib/python3.6/site-packages/knack/cli.py", line 215, in invoke
  cmd_result = self.invocation.execute(args)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 631, in execute
  raise ex
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 695, in _run_jobs_serially
  results.append(self._run_job(expanded_arg, cmd_copy))
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 688, in _run_job
  six.reraise(*sys.exc_info())
File "/opt/az/lib/python3.6/site-packages/six.py", line 693, in reraise
  raise value
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 665, in _run_job
  result = cmd_copy(params)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 324, in __call__
  return self.handler(*args, **kwargs)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/__init__.py", line 574, in default_command_handler
  return op(**command_args)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 75, in iot_device_list
  result = iot_query(cmd, query, hub_name, top, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 45, in iot_query
  target = get_iot_hub_connection_string(cmd, hub_name, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/common/_azure.py", line 112, in get_iot_hub_connection_string
  client = iot_hub_service_factory(cmd.cli_ctx)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/_factory.py", line 28, in iot_hub_service_factory
  from azure.mgmt.iothub.iot_hub_client import IotHubClient
ModuleNotFoundError: No module named 'azure.mgmt.iothub.iot_hub_client'
```
    
Pokud chcete tento problém vyřešit:

1. Spusťte následující příkaz:

    ```
    az --version
    ```
1. Ujistěte se, že máte nainstalovanou následující rozšíření. Od publikování tohoto článku jsou rozšíření a jejich verze:

    | Linka | Verze |
    |---|---|
    |azure-cli   |      2.5.1|
    |příkazové moduly – nspkg         |   2.0.3|
    |Core  |    2.5.1|
    |nspkg    | 3.0.4|
    |telemetrie| 1.0.4|
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. Pokud máte nainstalované rozšíření, jehož verze je starší než číslo verze uvedené zde, aktualizujte rozšíření pomocí následujícího příkazu:

    ```
    az extension update --name <Extension name>
    ```

    Můžete například spustit `az extension update --name azure-iot` .

### <a name="sample-app-issues"></a>Problémy s ukázkovou aplikací

V rámci naší verze jsme získali několik ukázek kódu .NET, které vám pomůžou zajistit, aby naše komunita vývojářů mohla zavedena. V této části jsou uvedeny některé chyby, se kterými se můžete setkat při spuštění ukázkového kódu, spolu s řešeními pro jejich ladění.

Problém: Program.cs se nezdařila s následující chybou volání přímé metody:

```
Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
```

1. Ujistěte se, že máte nainstalované [nástroje Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) v prostředí Visual Studio Code a že jste nastavili připojení ke službě IoT Hub. Provedete to tak, že vyberete CTRL + SHIFT + P a pak zvolíte **vybrat IoT Hub metoda**.

1. Zkontrolujte, zda můžete vyvolat přímou metodu v modulu IoT Edge pomocí Visual Studio Code. Například zavolejte GraphTopologyList s následující datovou částí { &nbsp; " @apiVersion ": "1,0"}. Měla by se zobrazit následující odpověď: 

    ```
    {
      "status": 200,
      "payload": {
        "values": [
          {…
    …}
          ]
        }
    }
    ```

    ![Snímek obrazovky s odpovědí v Visual Studio Code.](./media/troubleshoot-how-to/visual-studio-code1.png)
1. Pokud předchozí řešení selžou, zkuste následující:

    a. V zařízení IoT Edge na příkazovém řádku a spusťte následující příkaz:
    
      ```
      sudo systemctl restart iotedge
      ```

      Tento příkaz restartuje zařízení IoT Edge a všechny moduly. Počkejte pár minut a potom se před dalším pokusem o použití přímé metody potvrďte, že moduly běží, spuštěním následujícího příkazu:

      ```
      sudo iotedge list
      ```

    b. Pokud předchozí přístup neproběhne úspěšně, zkuste restartovat virtuální počítač nebo počítač.

    c. Pokud všechny přístupy selžou, spusťte následující příkaz, který získá soubor zip se všemi [relevantními protokoly](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command)a připojí ho k [lístku podpory](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

    ```
    sudo iotedge support-bundle --since 2h
    ```

1. Pokud obdržíte kód odpovědi na chybu *400* , ujistěte se, že datová část volání metody je ve správném formátu, podle průvodce [přímými metodami](direct-methods.md) .
1. Pokud obdržíte stav *200* , znamená to, že vaše centrum funguje dobře a vaše nasazení modulu je správné a reaguje. 

1. Zkontrolujte, jestli je konfigurace aplikace přesná. Vaše konfigurace aplikace se skládá z následujících polí *appsettings.js* v souboru. Dvojitá kontrola ověří, že deviceId a moduleId jsou přesné. Snadný způsob, jak to provést, je projít si část rozšíření Azure IoT Hub v Visual Studio Code. Hodnoty v souboru *appsettings.json* a oddílu IoT Hub by se měly shodovat.
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

1. V *appsettings.jsv* souboru zkontrolujte, že jste zadali připojovací řetězec IoT Hub a *ne* připojovací řetězec IoT Hub zařízení, protože se [liší formát připojovacího řetězce](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/).

### <a name="live-video-analytics-working-with-external-modules"></a>Live video Analytics pracuje s externími moduly

Live video Analytics prostřednictvím procesoru rozšíření HTTP může rozšířit mediální graf tak, aby odesílal a přijímal data z jiných IoT Edge modulů přes HTTP pomocí REST. V takovém [případě](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension)může mediální graf odesílat snímky videa jako obrázky do externího modulu odvození, jako je Yolo v3, a získávat výsledky analýzy založené na JSON. V takové topologii je cíl pro události většinou centrum IoT. V situacích, kdy se v centru nezobrazuje události odvození, zkontrolujte následující:

* Zkontrolujte, jestli se na rozbočovače, na který se bude Publisher graf nahrává, a zda je vaše centrum, které zkoumáte, stejné. Při vytváření více nasazení může dorazit k několika rozbočovačům a omylem kontrolovat chybné rozbočovače pro události.
* V části Visual Studio Code zkontrolujte, jestli je externí modul nasazený a spuštěný. V tomto příkladu obrázku jsou rtspsim a CV IoT Edge moduly, které jsou spuštěny externě v modulu lvaEdge.

    ![Snímek obrazovky, který zobrazuje stav spuštěných modulů v Azure IoT Hub.](./media/troubleshoot-how-to/iot-hub.png)

* Zkontrolujte, zda odesíláte události do správného koncového bodu adresy URL. Externí kontejner AI zpřístupňuje adresu URL a port, přes který obdrží, a vrátí data z požadavků POST. Tato adresa URL je zadána jako `endpoint: url` vlastnost pro procesor rozšíření http. Jak je vidět na [adrese URL topologie](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json), koncový bod je nastaven na parametr adresy URL Inferencing. Ujistěte se, že výchozí hodnota pro parametr nebo předaná hodnota je přesná. Můžete otestovat a zjistit, zda funguje pomocí adresy URL klienta (kudrlinkou).  

    Tady je příklad kontejner Yolo v3, který běží na místním počítači s IP adresou 172.17.0.3. K vyhledání IP adresy použijte Docker kontroler.

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Vrácený výsledek:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

* Pokud používáte jednu nebo víc instancí grafu, které používají procesor rozšíření HTTP, měli byste před každým procesorem rozšíření HTTP mít filtr snímkové frekvence, abyste mohli spravovat míru snímků videa za sekundu (FPS). 

   V některých situacích, kdy je procesor nebo paměť hraničního počítače vysoce využitý, můžete přijít o určité události odvození. Pokud chcete tento problém vyřešit, nastavte pro vlastnost maximumFps v filtru frekvence snímků nízkou hodnotu. Můžete ji nastavit na 0,5 ("maximumFps": 0,5) na všech instancích grafu a pak znovu spustit instanci, abyste kontrolovali události odvození v centru.

   Alternativně můžete získat výkonnější hraniční počítač s vyšším využitím procesoru a paměti.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Paralelní více přímých metod – Chyba časového limitu 

Live video Analytics na IoT Edge poskytuje přímý programovací model založený na metodách, který umožňuje nastavit několik topologií a více instancí grafů. Jako součást nastavení topologie a grafu vyvoláte více volání přímé metody v modulu IoT Edge. Pokud vyvoláte tyto vícenásobné volání metody paralelně, zejména ty, které spouštějí a zastavují grafy, může docházet k vypršení časového limitu, například následující: 

Metoda inicializace sestavení Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync vygenerovala výjimku. Microsoft. Azure. Devices. Common. Exceptions. IotHubException: Microsoft. Azure. Devices. Common. Exceptions. IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

Doporučujeme *, abyste přímé metody nevolali* paralelně. Zavolejte je sekvenčně (to znamená, že je třeba provést jedno přímé volání metody pouze po dokončení předchozího postupu).

### <a name="collect-logs-for-submitting-a-support-ticket"></a>Shromažďovat protokoly pro odeslání lístku podpory

Když kroky pro řešení potíží s vlastním průvodcem nevyřeší váš problém, přečtěte si Azure Portal a [otevřete lístek podpory](../../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!WARNING]
> Protokoly mohou obsahovat identifikovatelné osobní údaje (PII), jako je například vaše IP adresa. Všechny místní kopie protokolů se odstraní hned po dokončení jejich prověření a uzavření lístku podpory.  

Pokud chcete shromáždit příslušné protokoly, které by se měly přidat k lístku, postupujte podle pokynů v dalších částech. Soubory protokolu můžete nahrát v podokně **podrobností** žádosti o podporu.

### <a name="use-the-support-bundle-command"></a>Použití příkazu support-komplet

Pokud potřebujete shromáždit protokoly ze zařízení IoT Edge, nejjednodušší způsob je použít `support-bundle` příkaz. Tento příkaz shromáždí:

- Protokoly modulů
- Protokoly IoT Edge Security Manager a modul kontejnerů
- Výstup JSON pro kontrolu Iotedge
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
1. Vyberte **zkontrolovat + vytvořit**. Pod zelenou bannerem se publikuje zpráva o úspěšném ověření.
1. Vyberte **Vytvořit**.
1. Aktualizujte **Nevlákennou identitu modulu** tak, aby odkazovala na parametr DebugLogsDirectory, který odkazuje na adresář, ve kterém jsou protokoly shromažďovány:

    a. V tabulce **moduly** vyberte **lvaEdge**.  
    b. V horní části podokna vyberte možnost **Nevlákenovaná identita modulu**. Otevře se upravitelné podokno.  
    c. V části **požadovaný klíč**přidejte následující dvojici klíč/hodnota:  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > Tento příkaz váže složky protokolů mezi hraničním zařízením a kontejnerem. Pokud chcete protokoly shromažďovat v jiném umístění v zařízení:
    > 1. Vytvořte vazbu pro umístění protokolu ladění v oddílu **Binds** a nahraďte **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** a **$Debug _LOG_LOCATION** umístění, které chcete: `/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`
    > 2. Použijte následující příkaz a nahraďte **$DEBUG _LOG_LOCATION** umístěním použitým v předchozím kroku:  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`  
    
    d. Vyberte **Uložit**.

1. Reprodukujte problém.
1. Připojte se k virtuálnímu počítači na stránce **IoT Hub** na portálu.
1. PSČ všechny soubory ve složce *debugLogs*

   > [!NOTE]
   > Tyto soubory protokolu nejsou určeny pro samostatnou diagnostiku. Jsou určeny pro technický tým Azure k analýze vašich problémů.

   a. V následujícím příkazu Nezapomeňte nahradit **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** umístění protokolů ladění na hraničním zařízení, které jste nastavili dříve.  

   ```
   sudo apt install zip unzip  
   zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
   ```

   b. Připojte soubor *debugLogs.zip* k lístku podpory.

1. Shromažďování protokolů můžete zastavit nastavením hodnoty v poli **Identita modulu** na *hodnotu null*. Vraťte se na stránku s **Nevlákenou identity modulu** a aktualizujte následující parametr jako:

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>Další kroky

[Kurz: nahrávání videa založeného na událostech do cloudu a přehrávání z cloudu](event-based-video-recording-tutorial.md)
