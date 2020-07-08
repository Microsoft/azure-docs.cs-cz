---
title: Řešení potíží se službou Live video Analytics v IoT Edge – Azure
description: Tento článek popisuje postup řešení potíží pro analýzy živých videí na IoT Edge.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: dd55050521a1791a11f220cd5617d9df2fa2d160
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045568"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Řešení potíží se službou Live video Analytics na IoT Edge

Tento článek popisuje postup řešení potíží pro analýzy živých videí na IoT Edge.

## <a name="troubleshoot-deployment-issues"></a>Řešení problémů při nasazování

### <a name="diagnostics"></a>Diagnostika

V rámci nasazení služby Live video Analytics nastavíte prostředky Azure, například IoT Hub a IoT Edge zařízení. Jako první krok pro diagnostiku problémů vždy zajistí, že je hrana správně nastavená pomocí následujících pokynů:

1. [Spuštění příkazu check](https://docs.microsoft.com/azure/iot-edge/troubleshoot#run-the-check-command)
1. [Ověřit verzi IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-iot-edge-version)
1. [Kontrolovat stav IoT Edge Security Manageru a jeho protokolů](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-the-status-of-the-iot-edge-security-manager-and-its-logs)
1. [Zobrazení zpráv, které procházejí centrem centra IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot#view-the-messages-going-through-the-iot-edge-hub)
1. [Restartovat kontejnery](https://docs.microsoft.com/azure/iot-edge/troubleshoot#restart-containers)
1. [Ověřte bránu firewall a pravidla konfigurace portů.](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-firewall-and-port-configuration-rules)

### <a name="pre-deployment-issues"></a>Problémy před nasazením

Pokud je hraniční infrastruktura v pořádku, můžete vyhledat problémy se souborem manifestu nasazení. Aby bylo možné nasadit Live video Analytics IoT Edge na hraničním zařízení spolu s jinými moduly IoT, použijte manifest nasazení, který obsahuje hraniční centrum, hraničního agenta a další moduly s jejich vlastnostmi. Pokud kód JSON není ve správném formátu, můžete získat chybu, jak je uvedeno níže: 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

Nepodařilo se analyzovat JSON ze souboru: ' <deployment manifest.json> ' pro Argument ' Content ' s výjimkou: "extra data: řádek 101 sloupec 1 (znak 5325)"

Pokud dojde k této chybě, doporučuje se v souboru JSON vyhledat chybějící závorky nebo jiné problémy se strukturou souboru. K ověření struktury souborů můžete použít klienta, jako je [Poznámkový blok nebo nástroj s modulem JSON Viewer](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) nebo online nástroj, jako je například https://jsonformatter.curiousconcept.com/ .

### <a name="deployment--diagnose-with-media-graph-direct-methods"></a>Nasazení – diagnostika pomocí přímých metod Media graphu 

Jakmile se na hraničním zařízení správně nasadí Live video Analytics na IoT Edge, můžete vytvořit a spustit Media Graph vyvoláním [přímých metod](direct-methods.md). Pomocí portálu můžete spustit diagnostiku mediálního grafu prostřednictvím přímých metod:

1. Přes portál, přejdete na IoT Hub, který je připojený k vašemu hraničnímu zařízení.
    1. V okně IoT Hub vyhledejte automatické řízení zařízení->IoT Edge.
    1. Kliknutím na IoT Edge by se měl zobrazit seznam hraničních zařízení. Vyberte zařízení, které chcete diagnostikovat.
         
        ![Hraniční zařízení](./media/troubleshoot-how-to/lva-sample-device.png)
    1. Ověřte, jestli je kód odpovědi 200 – OK. K dispozici jsou různé kódy odpovědí pro [IoT Edge runtime](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime) , jako například:
        1. 400 – konfigurace nasazení je poškozená nebo neplatná.
        1. 417 – zařízení nemá nastavenou konfiguraci nasazení.
        1. 412 – verze schématu v konfiguraci nasazení není platná.
        1. 406 – IoT Edge zařízení je offline nebo neodesílá zprávy o stavu.
        1. 500 – při IoT Edge modulu runtime došlo k chybě.
    1. Po kliknutí na zařízení by se měl také zobrazit seznam očekávaných IoT Edge modulů, které byly nasazeny, a jejich stav
    1. Pokud sloupce zadané v nasazení a hlášené podle zařízení označují možnost Ano, můžete vyvolat přímé metody v nástroji Live video Analytics v modulu IoT Edge. Klikněte na modul a přejdete na obrazovku, kde můžete kontrolovat požadované a hlášené vlastnosti a může vyvolat přímé metody. 
        1. Kontrola hlášených a požadovaných vlastností vám pomůže pochopit, jestli se vlastnosti modulu v nasazení synchronizoval. Pokud ne, můžete restartovat svoji hranici. 
        1. Pomocí průvodce [přímými metodami](direct-methods.md) zavolejte několik metod, zejména jednoduché, jako je GraphTopologyList. Průvodce také určí očekávaná datová část požadavku a odpovědi a kódy chyb. Po úspěšném provedení jednoduchých přímých metod můžete zajistit, aby byl modul video Analytics pro živé video fungovat funkčně v pořádku.
        
        ![Direct – metoda](./media/troubleshoot-how-to/direct-method.png) 
1. Pokud obdržíte stav 501, zkontrolujte prosím, jestli je název přímé metody přesný. Pokud je název metody a datová část požadavku přesný, měli byste získat výsledky zpátky spolu s kódem úspěšnosti = 200. Pokud je datová část požadavku nepřesná, získáte stav = 400 a datovou část odpovědi, která označuje kód chyby a zprávu, která by měla pomáhat s diagnostikou problému pomocí přímé volání metody. 

### <a name="post-deployment--diagnose-logs-for-issues-during-run"></a>Po nasazení – diagnostikovat protokoly pro problémy během běhu 

Protokoly kontejneru pro náš modul Edge by měly mít diagnostiku.<!--<todo:add link to diagnostics doc>--> informace, které by měly pomáhat při ladění potíží během běhu modulu. Můžete [zkontrolovat protokoly kontejnerů pro problémy](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-container-logs-for-issues) a samočinnou diagnostiku, ale pokud jste provedli všechny výše uvedené kontroly a pořád máte problémy, pak Shromážděte protokoly ze zařízení IoT Edge [pomocí příkazu "sada prostředků](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command) ", který může být dále analyzován týmem Azure. K podpoře a odesílání shromážděných protokolů se můžete [obrátit](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) na nás.

## <a name="common-error-resolutions"></a>Řešení běžných chyb

Live video Analytics se na hraničním zařízení nasadí jako IoT Edge modul a spolupracuje s moduly IoT Edge a agenty centra. Některé běžné chyby, na které se můžete setkat s nasazením Live video Analytics, budou způsobené problémy se základní infrastrukturou IoT. Mezi běžné chyby, které IoT Edge agenta a centra, patří:

1. [Agent IoT Edge se zastaví po uplynutí minuty](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-stops-after-about-a-minute).
1. [Agent IoT Edge nemá přístup k imagi modulu (403)](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-cant-access-a-modules-image-403).
1. [Modul Edge agent oznamuje prázdný konfigurační soubor a na zařízení nezačíná žádné moduly](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
1. [Nepodařilo se spustit centrum IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-hub-fails-to-start).
1. Funkce [démon zabezpečení IoT Edge se nezdařila s neplatným názvem hostitele](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-security-daemon-fails-with-an-invalid-hostname).
1. [Live video Analytics nebo jakýkoli jiný vlastní IoT Edge modul nedokáže odeslat zprávu do hraničního centra s 404 chybou](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
1. [Nasazení modulu IoT Edge se úspěšně nasazuje ze zařízení](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-deploys-successfully-then-disappears-from-device).

### <a name="edge-set-up-script-issues"></a>Hraniční nastavení problémů se skripty

V rámci naší dokumentace jsme dopustili [nastavení skriptu](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) pro nasazení hraničních a cloudových prostředků, abyste mohli začít se službou Live video Analytics Edge. V této části jsme nachytili chyby, se kterými se můžete setkat s skriptem a jak je ladit.

Skript spustí částečně vytváření několika prostředků, ale u této zprávy dojde k chybě:

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
1. Ujistěte se, že máte nainstalovanou následující rozšíření. V době psaní tohoto průvodce používala Tato verze rozšíření následující:

    | Linka | Verze |
    |---|---|
    |azure-cli   |      2.5.1|
    |příkazové moduly – nspkg         |   2.0.3|
    |Core  |    2.5.1|
    |nspkg    | 3.0.4|
    |telemetrie| 1.0.4|
    |SND    ||
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. Pokud je některá z rozšíření starší než výše uvedená čísla vydání, aktualizujte rozšíření na nejnovější verzi pomocí příkazu:

    ```
    az extension update --name <Extension name>
    ```

    Například `az extension update --name azure-iot`.

### <a name="sample-app-issues"></a>Problémy s ukázkovou aplikací

V rámci naší verze jsme získali nějaký ukázkový kód .NET, který získá naši komunitu vývojářů v zavedeném prostředí. V této části jsme nachytili chyby, ke kterým může dojít při spuštění ukázkového kódu, a jak tyto chyby ladit.

1. Program.cs se nezdařila s následující chybou volání přímé metody:

    ```
    Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
    ```

    1. Ujistěte se, že máte nainstalované [nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) v prostředí vs Code a připojení k vašemu IoT Hub nastavení. (CTRL + SHIFT + P a potom vyberte vybrat IoT Hub způsob připojení k předplatnému a IoT Hub)
1. Ověřte, zda lze vyvolat přímou metodu v modulu Edge prostřednictvím VS Code (například volejte GraphToplogyList s následující datovou částí {" @apiVersion ": "1,0"}) a měli byste získat následující odpověď zpět. 

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

    ![Visual Studio Code](./media/troubleshoot-how-to/visual-studio-code1.png)
1. Pokud se výše uvedený postup nezdařil, vyzkoušejte následující:
    1. Do příkazového řádku na hraničním zařízení zadejte.
    
    ```
    sudo systemctl restart iotedge
    ```

    Tím dojde k restartování hraničního zařízení a všech modulů. Počkejte pár minut a spusťte následující příkaz, abyste potvrdili, že moduly běží, a teprve potom zkuste DirectMethod použít znovu.

    ```
    sudo iotedge list
    ```
    1. Pokud se to nepodaří, můžete restartovat virtuální počítač nebo počítač.
    1. Pokud se všechny nezdařily, spusťte následující příkaz, který získá soubor ZIP se všemi [souvisejícími protokoly](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command) , které se mají připojit k tomuto [lístku podpory](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

    ```
    sudo iotedge support-bundle --since 2h
    ```
1. Pokud získáte kód chybové odpovědi 400, ujistěte se, že datová část volání metody je ve správném formátu podle průvodce [přímými metodami](direct-methods.md) .
1. Pokud obdržíte stav 200, znamená to, že vaše centrum funguje dobře a vaše nasazení modulu je správné a reaguje. Dalším krokem je ověřit, jestli jsou konfigurace aplikace přesné. Vaše konfigurace aplikace se skládá z následujících polí appsettings.jsv souboru. Zkontrolujte prosím, že deviceId a moduleId jsou přesné. Snadný způsob, jak to zjistit, je prostřednictvím části rozšíření Azure IoT Hub v VSCode. Hodnoty v appsettings.jssouboru a IoT Hub oddílu by se měly shodovat.
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

    ![CENTRUM IOT](./media/troubleshoot-how-to/iot-hub.png)

1. Nakonec se ujistěte, že v rámci appsettings.jsjste zadali IoT Hub připojovací řetězec, nikoli připojovací řetězec IoT Hub zařízení, protože jejich [formáty](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/) se liší.

### <a name="live-video-analytics-working-with-external-modules"></a>Live video Analytics pracuje s externími moduly

Live video Analytics prostřednictvím procesoru rozšíření HTTP může rozšířit mediální graf tak, aby odesílal a přijímal data z jiných IoT Edge modulů přes HTTP pomocí REST.  Jako [konkrétní příklad](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension) může mediální graf odesílat snímky videa jako obrázky na externí odvozený modul, jako je Yolo v3, a získat zpět výsledky analýzy založené na formátu JSON. V takové topologii je koncovým cílem pro události většinou IoT Hub. V situacích, kdy se v centru nezobrazí události odvození, zkontrolujte prosím následující:

1. Podívejte se, jestli se v centru, ve kterém je Publisher, publikuje, na vs. ten, který zkoumáte, je stejný. Když vytváříte několik nasazení, skončíte s několika rozbočovači a může se stát, že nebudete moct omylem kontrolovat chybné centrum událostí.
1. Pokud je externí modul nasazený a spuštěný, podívejte se přes VSCode. V tomto příkladu obrázku jsou rtspsim a CV IoT Edge moduly, které spouštějí externí modul lvaEdge.

    ![CENTRUM IOT](./media/troubleshoot-how-to/iot-hub.png)
1. Ověřte, zda odesíláte události do správného koncového bodu adresy URL. Externí kontejner AI zpřístupňuje adresu URL a port, přes který obdrží, a vrátí data z požadavků POST. Tato adresa URL je zadána jako koncový bod: vlastnost URL pro procesor rozšíření http. Jak je vidět na [adrese URL topologie](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json) , je nastaven parametr adresy URL Inferencing. Zajistěte, aby byla výchozí hodnota parametru ( http://yolov3/score) nebo předaná hodnota přesná, a můžete ji otestovat, pokud pracuje pomocí hodnoty kudrlinkou.  
    1. Například kontejner Yolo V3 spuštěný v místním počítači a IP adresa pro kontejner je 172.17.0.3 (k vyhledání IP adresy použijte kontrolu Docker).

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Vrácený výsledek:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

1. Pokud používáte jednu nebo víc instancí grafu s využitím procesoru rozšíření http, měli byste mít filtr snímkové frekvence před každým procesorem rozšíření http ke správě snímků za sekundu (FPS) kanálu videa. V některých situacích, kdy je procesor/paměť hraničního počítače vysoce využitý, můžete přijít o určité události odvození. Pokud to chcete vyřešit, nastavte pro vlastnost maximumFps v filtru frekvence snímků nízkou hodnotu. Můžete ho nastavit na 0,5 ("maximumFps": 0,5) na všech instancích grafu a znovu spustit pro kontrolu odvození událostí v centru.
    1. Alternativně můžete také získat výkonnější hraniční počítač s vyšším využitím procesoru a paměti.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Paralelní více přímých metod – Chyba časového limitu 

Live video Analytics na IoT Edge poskytuje přímý programovací model založený na metodách, který umožňuje nastavit několik topologií a více instancí grafů. Jako součást nastavení topologie a grafu budete volat několik volání přímé metody v modulu Edge. Pokud vyvoláte tyto vícenásobná volání metody, zejména při spouštění a zastavování grafů, může docházet k nějakým chybám časového limitu, jako je například níže. 

Metoda inicializace sestavení Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync vygenerovala výjimku. Microsoft. Azure. Devices. Common. Exceptions. IotHubException: Microsoft. Azure. Devices. Common. Exceptions. IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

Doporučujeme, abyste nevolali přímé metody paralelně, ale provedete je sekvenčním způsobem, tj.  jedno přímé volání metody pouze po dokončení předchozí.

### <a name="collecting-logs-for-submitting-a-support-ticket"></a>Shromažďování protokolů pro odeslání lístku podpory

Když kroky pro řešení potíží s vlastním průvodcem nevyřeší vaše problémy, měli byste přejít na Azure Portal a [otevřít lístek podpory](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

Pomocí následujících kroků Shromážděte příslušné protokoly, které by se měly přidat k lístku. Soubory protokolů budete moct nahrát na kartě **Podrobnosti** žádosti o podporu.

### <a name="support-bundle"></a>Podpora – sada

Pokud potřebujete shromáždit protokoly ze zařízení IoT Edge, nejjednodušší způsob je použít `support-bundle` příkaz. Tento příkaz shromáždí:

- Protokoly modulů
- Protokoly IoT Edge Security Manager a modul kontejnerů
- Výstup JSON pro kontrolu Iotedge
- Užitečné informace o ladění

#### <a name="use-the-iot-edge-security-manager"></a>Použití Správce zabezpečení IoT Edge
 
IoT Edge Security Manager zodpovídá za operace, jako je inicializace IoT Edge systému při spuštění a zřizování zařízení. Pokud IoT Edge nezačnete, můžou vám poskytnout užitečné informace protokoly správce zabezpečení. Zobrazení podrobnějších protokolů IoT Edge Security Manageru:

1. Upravte nastavení IoT Edge démona na zařízení IoT Edge:

    ```
    sudo systemctl edit iotedge.service
    ```

1. Aktualizujte následující řádky:

    ```
    [Service]
    Environment=IOTEDGE_LOG=edgelet=debug
    ```

1. Restartujte IoT Edge démon zabezpečení spuštěním těchto příkazů:

    ```
    sudo systemctl cat iotedge.service
    sudo systemctl daemon-reload
    sudo systemctl restart iotedge
    ```

1. Spusťte `support-bundle` příkaz s příznakem--od, který určuje, jak dlouho od posledního chcete získat protokoly. Například 2 – 2. až za poslední dvě hodiny získají protokoly. Hodnotu tohoto příznaku můžete změnit tak, aby obsahovala protokoly pro jiné období.

    ```
    sudo iotedge support-bundle --since 2h
    ```

### <a name="lva-debug-logs"></a>Protokoly ladění LVA

Pomocí těchto kroků nakonfigurujete modul LVA on IoT Edge pro generování protokolů ladění:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do služby IoT Hub.
1. V nabídce vyberte **IoT Edge** .
1. V seznamu zařízení klikněte na ID cílového zařízení.
1. V horní nabídce klikněte na odkaz **nastavit moduly** .

  ![nastavení modulů na portálu Azure Portal](media/troubleshoot-how-to/set-modules.png)

5. V části IoT Edge moduly vyhledejte a klikněte na **lvaEdge**.
1. Klikněte na **kontejner možnosti vytvoření kontejneru**.
1. V části vazby přidejte následující příkaz:

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    Tím se vytvoří vazba složek protokolů mezi hraničním zařízením a kontejnerem.

1. Klikněte na tlačítko **aktualizovat** .
1. V dolní části stránky klikněte na tlačítko **Revize + vytvořit** . Bude provedeno jednoduché ověření a zpráva o úspěšném ověření bude potištěna pod zelenou bannerovou zprávou.
1. Klikněte na tlačítko **vytvořit** .
1. Dále aktualizujte **vlákna identity modulu** , aby odkazovalo na parametr DebugLogsDirectory tak, aby odkazovalo na adresář, do kterého budou protokoly shromažďovány:
    1. V tabulce **moduly** vyberte **lvaEdge** .
    1. Klikněte na odkaz **Nevlákenovaná identita modulu** . Najdete ho v horní části stránky. Otevře se podokno upravitelný.
    1. Do **požadovaného klíče**přidejte následující pár klíč-hodnota:

        `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    1. Klikněte na **Uložit**.

1. Reprodukujte problém.
1. Připojte se k virtuálnímu počítači ze stránky IoT Hub na portálu.
1. Přejděte do `/var/local/mediaservices/logs` složky a pak na zip obsah přihrádky této složky a sdílejte ji s námi. (Tyto soubory protokolu nejsou určeny pro samočinnou diagnostiku. Jsou určeny k analýze vašich problémů v rámci Azure.)

1. Shromažďování protokolů je možné zastavit nastavením hodnoty v modulu na *hodnotu null* u **identity** . Vraťte se na stránku s **Nevlákenou identity modulu** a aktualizujte následující parametr jako:

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>Další kroky

[Kurz: nahrávání videa založeného na událostech do cloudu a přehrávání z cloudu](event-based-video-recording-tutorial.md)
