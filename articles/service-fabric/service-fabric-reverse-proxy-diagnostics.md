---
title: Diagnostika reverzního proxy serveru Azure Service Fabric
description: Zjistěte, jak monitorovat a diagnostikovat zpracování požadavků na reverzním proxy serveru pro aplikaci Azure Service Fabric.
author: kavyako
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: bbc1fe5a76ecb5720bc49e0a082d5e9151b403d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645459"
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>Monitorování a diagnostika zpracování požadavků na reverzním proxy serveru

Počínaje verzí 5.7 Service Fabric, reverzní proxy události jsou k dispozici pro sběr. Události jsou k dispozici ve dvou kanálech, jeden s pouze chybové události související s požadavek zpracování selhání na reverzní proxy a druhý kanál obsahující podrobné události s položkami pro úspěšné i neúspěšné požadavky.

Odkazovat [na shromažďovat reverzní proxy události](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations) povolit shromažďování událostí z těchto kanálů v místních clusterech a Azure Service Fabric.

## <a name="troubleshoot-using-diagnostics-logs"></a>Poradce při potížích s použitím protokolů diagnostiky
Zde je několik příkladů, jak interpretovat běžné protokoly selhání, které lze setkat:

1. Reverzní proxy vrátí stavový kód odpovědi 504 (Časový čas).

    Jedním z důvodů může být způsobeno tím, že služba neodpověděla v rámci časového lhůty požadavku.
   První událost níže zaznamenává podrobnosti o požadavku přijatém na reverzní proxy server. 
   Druhá událost označuje, že požadavek se nezdařil při předávání služby z důvodu "vnitřní chyba = ERROR_WINHTTP_TIMEOUT" 

    Datová část zahrnuje:

   * **traceId**: Tento identifikátor GUID lze použít ke korelaci všech událostí odpovídajících jednomu požadavku. V níže dvě události traceId = **2f87b722-e254-4ac2-a802-fd315c1a0271**, což znamená, že patří do stejné ho požadavku.
   * **requestUrl**: Adresa URL (Reverzní adresa URL proxy), na kterou byl požadavek odeslán.
   * **sloveso**: HTTP sloveso.
   * **remoteAddress**: Adresa klienta odesílajícího požadavek.
   * **resolvedServiceUrl**: Adresa URL koncového bodu služby, na kterou byl příchozí požadavek vyřešen. 
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

2. Reverzní proxy vrátí stavový kód odpovědi 404 (nebyl nalezen). 
    
    Zde je příklad události, kde reverzní proxy vrátí 404, protože se nepodařilo najít koncový bod odpovídající služby.
    Užitečné položky zatížení jsou zde:
   * **processRequestPhase**: Označuje fázi během zpracování požadavku, když došlo k ***chybě, TryGetEndpoint*** tj. při pokusu o načtení koncového bodu služby dopředu. 
   * **errorDetails**: Uvádí kritéria vyhledávání koncových bodů. Zde můžete vidět, že listenerName zadaný = **FrontEndListener** vzhledem k tomu, že seznam koncovýbod repliky obsahuje pouze naslouchací proces s názvem **OldListener**.
    
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
     Dalším příkladem, kde reverzní proxy může vrátit 404 Not Found je: ApplicationGateway\Http konfigurační parametr **SecureOnlyMode** je nastavena na true s reverzní proxy naslouchání na **HTTPS**, ale všechny koncové body repliky jsou nezabezpečené (naslouchání na HTTP).
     Reverzní proxy vrátí 404, protože nemůže najít koncový bod naslouchání na HTTPS předat požadavek. Analýza parametrů v datové části události pomáhá zúžit problém:
    
     ```
      "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
     ```

3. Požadavek na reverzní proxy server se nezdaří s chybou časového výpadku. 
    Protokoly událostí obsahují událost s podrobnostmi o přijatém požadavku (není zde zobrazeno).
    Další událost ukazuje, že služba odpověděla stavovým kódem 404 a reverzní proxy iniciuje opětovné vyřešení. 

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
    Při shromažďování všech událostí se zobrazí vlak událostí zobrazujících každý pokus o odhodlání a předání dál.
    Poslední událost v řadě ukazuje, že zpracování požadavku se nezdařilo s časovým limitem spolu s počtem úspěšných pokusů o vyřešení.
    
    > [!NOTE]
    > Doporučujeme zachovat kolekci podrobných událostí kanálu ve výchozím nastavení a povolit ji pro řešení potíží na základě potřeby.

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
    
    Pokud je kolekce povolena pouze pro kritické/chybové události, zobrazí se jedna událost s podrobnostmi o časovém odpočtu a počtu pokusů o vyřešení. 
    
    Služby, které mají v úmyslu odeslat stavový kód 404 zpět uživateli, by měl přidat hlavičku "X-ServiceFabric" v odpovědi. Po přidání záhlaví do odpovědi obrátí proxy server zpět stavový kód zpět klientovi.  

4. Případy, kdy klient odpojil požadavek.

    Následující událost je zaznamenána při zpětném proxy serveru předává odpověď klientovi, ale klient se odpojí:

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

    FABRIC_E_SERVICE_DOES_NOT_EXIST chyba je vrácena, pokud schéma URI není zadáno pro koncový bod služby v manifestu služby.

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    Chcete-li problém vyřešit, zadejte schéma URI v manifestu.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> Události související se zpracováním požadavků na websocket nejsou aktuálně protokolovány. To bude přidáno v další verzi.

## <a name="next-steps"></a>Další kroky
* [Agregace a shromažďování událostí pomocí diagnostiky Windows Azure](service-fabric-diagnostics-event-aggregation-wad.md) pro povolení shromažďování protokolů v clusterech Azure.
* Informace o zobrazení událostí Service Fabric v sadě Visual Studio naleznete v [tématu Monitorování a místní diagnostika](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
* Viz [Konfigurace reverzního proxy serveru pro připojení k zabezpečeným službám](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services) pro ukázky šablon Azure Resource Manager u ke konfiguraci zabezpečeného reverzního proxy serveru s různými možnostmi ověření certifikátu služby.
* Přečtěte si [service fabric reverzní proxy](service-fabric-reverseproxy.md) se dozvíte více.
