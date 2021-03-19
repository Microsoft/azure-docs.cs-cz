---
title: Diagnostika reverzního proxy serveru Azure Service Fabric
description: Naučte se monitorovat a diagnostikovat zpracování požadavků na reverzním proxy serveru pro aplikaci Azure Service Fabric.
author: kavyako
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: bbc1fe5a76ecb5720bc49e0a082d5e9151b403d8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "75645459"
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>Monitorování a diagnostika zpracování požadavků na reverzním proxy serveru

Od verze 5,7 Service Fabric jsou pro kolekci k dispozici události reverzního proxy serveru. Události jsou k dispozici ve dvou kanálech, jednu s chybovými událostmi, které souvisí s chybou zpracování žádostí na reverzním proxy serveru a druhým kanálem obsahujícím podrobné události s položkami pro úspěšné i neúspěšné požadavky.

Podívejte se na téma [shromáždění událostí reverzního proxy](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations) a umožněte shromažďování událostí z těchto kanálů v místních a Service Fabricch clusterech Azure.

## <a name="troubleshoot-using-diagnostics-logs"></a>Řešení potíží pomocí diagnostických protokolů
Tady je několik příkladů, jak interpretovat běžné protokoly selhání, ke kterým může dojít:

1. Reverzní proxy server vrátí stavový kód odpovědi 504 (časový limit).

    Důvodem může být to, že se služba nedaří odpovědět v časovém limitu žádosti.
   První událost níže zaznamená Podrobnosti žádosti přijaté na reverzním proxy serveru. 
   Druhá událost indikuje, že se požadavek při přeposílání do služby nezdařil, protože došlo k vnitřní chybě = ERROR_WINHTTP_TIMEOUT. 

    Datová část obsahuje:

   * **traceId**: Tento identifikátor GUID lze použít ke korelaci všech událostí odpovídajících jednomu požadavku. V níže uvedených dvou událostech traceId = **2f87b722-e254-4ac2-A802-fd315c1a0271**, což předpokládá, že patří ke stejné žádosti.
   * **requestUrl**: adresa URL (reverzní proxy adresa URL), na kterou se odeslal požadavek.
   * **příkaz**: příkaz HTTP.
   * **remoteAddress**: adresa klienta odesílajícího požadavek.
   * **resolvedServiceUrl**: adresa URL koncového bodu služby, na kterou byl příchozí požadavek vyřešen. 
   * **errorDetails**: Další informace o selhání.

     ```
     {
     "Timestamp": "2017-07-20T15:57:59.9871163-07:00",
     "ProviderName": "Microsoft-ServiceFabric",
     "Id": 51477,
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Request url = https://localhost:19081/LocationApp/LocationFEService?zipcode=98052, verb = GET, remote (client) address = ::1, resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052, request processing start time =     15:58:00.074114 (745,608.196 MSec) ",
     "ProcessId": 57696,
     "Level": "Informational",
     "Keywords": "0x1000000000000021",
     "EventName": "ReverseProxy",
     "ActivityID": null,
     "RelatedActivityID": null,
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?zipcode=98052",
      "verb": "GET",
      "remoteAddress": "::1",
      "resolvedServiceUrl": "Https://localhost:8491/LocationApp/?zipcode=98052",
      "requestStartTime": "2017-07-20T15:58:00.0741142-07:00"
     }
     }

     {
     "Timestamp": "2017-07-20T16:00:01.3173605-07:00",
     ...
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Error while forwarding request to service: response status code = 504, description = Reverse proxy Timeout, phase = FinishSendRequest, internal error = ERROR_WINHTTP_TIMEOUT ",
     ...
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "statusCode": 504,
      "description": "Reverse Proxy Timeout",
      "sendRequestPhase": "FinishSendRequest",
      "errorDetails": "internal error = ERROR_WINHTTP_TIMEOUT"
     }
     }
     ```

2. Reverzní proxy server vrátí stavový kód odpovědi 404 (Nenalezeno). 
    
    Tady je příklad události, kde reverzní proxy vrátí 404, protože se nepovedlo najít odpovídajícího koncového bodu služby.
    Zde jsou datové položky, které vás zajímají:
   * **processRequestPhase**: Určuje fázi během zpracování žádosti, když došlo k chybě, **_TryGetEndpoint_** , tj. Při pokusu o načtení koncového bodu služby pro přeposílání. 
   * **errorDetails**: vypíše kritéria hledání koncových bodů. Tady vidíte **, že seznam** koncových bodů repliky obsahuje jenom naslouchací proces s názvem **OldListener**.
    
     ```
     {
     ...
     "Message": "c1cca3b7-f85d-4fef-a162-88af23604343 Error while processing request, cannot forward to service: request url = https://localhost:19081/LocationApp/LocationFEService?ListenerName=FrontEndListener&zipcode=98052, verb = GET, remote (client) address = ::1, request processing start time = 16:43:02.686271 (3,448,220.353 MSec), error = FABRIC_E_ENDPOINT_NOT_FOUND, message = , phase = TryGetEndoint, SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"\":\"Https:\/\/localhost:8491\/LocationApp\/\"}} ",
     "ProcessId": 57696,
     "Level": "Warning",
     "EventName": "ReverseProxy",
     "Payload": {
      "traceId": "c1cca3b7-f85d-4fef-a162-88af23604343",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?ListenerName=NewListener&zipcode=98052",
      ...
      "processRequestPhase": "TryGetEndoint",
      "errorDetails": "SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Https:\/\/localhost:8491\/LocationApp\/\"}}"
     }
     }
     ```
     Další příklad, kdy může reverzní proxy vracet hodnotu 404, je: parametr konfigurace ApplicationGateway\Http **SecureOnlyMode** je nastaven na true s reverzním proxy serverem naslouchání na **https**, ale všechny koncové body repliky nejsou zabezpečené (naslouchá na http).
     Reverzní proxy vrátí 404, protože nemůže najít koncový bod, který naslouchá na HTTPS, aby předal požadavek. Analýza parametrů v datové části události pomáhá zúžit problém:
    
     ```
      "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
     ```

3. Požadavek na reverzní proxy se nezdařil s chybou vypršení časového limitu. 
    Protokoly událostí obsahují událost s podrobnostmi o přijatém požadavku (tady není zobrazený).
    Další událost ukazuje, že služba odpověděla stavovým kódem 404 a reverzní proxy iniciuje opakované přeložení. 

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Request to service returned: status code = 404, status description = , Reresolving ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "statusCode": 404,
        "statusDescription": ""
      }
    }
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Re-resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052 ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "requestUrl": "Https://localhost:8491/LocationApp/?zipcode=98052"
      }
    }
    ```
    Při shromažďování všech událostí se zobrazí vlak událostí se všemi řešeními a dalším pokusem.
    Poslední událost v řadě ukazuje zpracování žádosti se nezdařila s časovým limitem, a to spolu s počtem úspěšných pokusů o vyřešení.
    
    > [!NOTE]
    > Doporučuje se ve výchozím nastavení zakázat shromažďování událostí podrobného kanálu a povolit ho pro řešení potíží podle potřeby.

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Error while processing request: number of successful resolve attempts = 12, error = FABRIC_E_TIMEOUT, message = , phase = ResolveServicePartition,  ",
      "EventName": "ReverseProxy",
      ...
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "resolveCount": 12,
        "errorval": -2147017729,
        "errorMessage": "",
        "processRequestPhase": "ResolveServicePartition",
        "errorDetails": ""
      }
    }
    ```
    
    Pokud je povolená kolekce jenom pro události kritické/chyby, zobrazí se jedna událost s podrobnostmi o časovém limitu a počtu pokusů o vyřešení. 
    
    Služby, které hodlají odeslat stavový kód 404 zpět uživateli, by měly v odpovědi přidat hlavičku "X-ServiceFabric". Po přidání hlavičky do odpovědi přesměruje proxy kód stavu zpět do klienta.  

4. Případy, kdy klient odpojil požadavek.

    Pokud reverzní proxy předává odpověď klientovi, ale klient se odpojí, je zaznamenána následující událost:

    ```
    {
      ...
      "Message": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8 Unable to send response to client: phase = SendResponseHeaders, error = -805306367, internal error = ERROR_SUCCESS ",
      "ProcessId": 57696,
      "Level": "Warning",
      ...
      "EventName": "ReverseProxy",
      "Payload": {
        "traceId": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8",
        "sendResponsePhase": "SendResponseHeaders",
        "errorval": -805306367,
        "winHttpError": "ERROR_SUCCESS"
      }
    }
    ```
5. Reverzní proxy vrátí 404 FABRIC_E_SERVICE_DOES_NOT_EXIST

    Pokud není zadáno schéma identifikátoru URI pro koncový bod služby v manifestu služby, je vrácena FABRIC_E_SERVICE_DOES_NOT_EXIST chyba.

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    Chcete-li tento problém vyřešit, zadejte v manifestu schéma identifikátoru URI.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> Události související s zpracováním požadavků WebSocket se momentálně neprotokolují. Tato akce bude přidána v příští verzi.

## <a name="next-steps"></a>Další kroky
* [Agregace a shromažďování událostí pomocí Azure Diagnostics Windows](service-fabric-diagnostics-event-aggregation-wad.md) pro povolení shromažďování protokolů v clusterech Azure.
* Postup zobrazení Service Fabricch událostí v aplikaci Visual Studio naleznete v tématu [monitorování a diagnostika místně](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
* V tématu [Konfigurace reverzního proxy serveru pro připojení k zabezpečeným službám](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services) pro Azure Resource Manager ukázkové šablony pro konfiguraci zabezpečeného reverzního proxy serveru pomocí různých možností ověřování certifikátů služby.
* Další informace najdete [Service Fabric reverzní proxy serveru](service-fabric-reverseproxy.md) .
