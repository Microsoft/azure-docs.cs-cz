---
title: Azure Service Fabric Diagnostika reverzního proxy serveru | Dokumentace Microsoftu
description: Zjistěte, jak monitorovat a diagnostikovat zpracování požadavku na reverzní proxy server.
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: c9c8c649208cff95f4ee515d39cc8cca3e2c64bf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121481"
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>Monitorování a Diagnostika zpracování požadavku na reverzní proxy server

Od 5.7 verze služby Service Fabric, události reverzního proxy serveru jsou k dispozici pro kolekci. Události jsou k dispozici ve dvou kanálů, jednu s pouze chybové události související s selhání zpracování požadavku na reverzní proxy server a druhý kanál, který obsahuje podrobné události s položkami pro úspěšné i neúspěšné požadavky.

Odkazovat na [shromažďování událostí reverzní proxy server](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations) umožňuje shromažďování událostí z těchto kanálů v místní verzi a clustery Azure Service Fabric.

## <a name="troubleshoot-using-diagnostics-logs"></a>Odstraňování potíží pomocí protokolů diagnostiky
Tady jsou některé příklady o tom, jak interpretovat běžné protokoly chyb, které můžou mít jednu:

1. Reverzní proxy server vrátí stavový kód odpovědi 504 (vypršel časový limit).

    Jedním z důvodů může být způsobeno služeb při selhání pro odpověď v rámci časového limitu žádosti o služby.
   První událostí pod protokoly podrobnosti žádosti o přijetí na reverzní proxy server. 
   Druhá událost ukazuje na to, že požadavek se nezdařil při předávání do služby, z důvodu "vnitřní chyba = ERROR_WINHTTP_TIMEOUT" 

    Datová část obsahuje:

   * **traceId**: Tento identifikátor GUID lze použít ke korelaci všechny události, které odpovídá jedné žádosti. V následující dvě události, traceId = **2f87b722-e254-4ac2-a802-fd315c1a0271**, zdání patří ke stejnému požadavku.
   * **requestUrl**: URL (adresa URL reverzního proxy serveru), do kterého byla vyslána žádost.
   * **příkaz**: Příkaz HTTP.
   * **remoteAddress**: Adresa bylo zahájeno odesílání požadavku klienta.
   * **resolvedServiceUrl**: Adresa URL koncového bodu služby do kterého byla vyřešena příchozího požadavku. 
   * **Detaily chyby**: Další informace o selhání.

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
    
    Tady je příklad události kde reverzní proxy server vrátí 404, protože se nepodařilo najít odpovídající koncový bod služby.
    Datová část položky popište jsou:
   * **processRequestPhase**: Označuje fázi během zpracování požadavků, kdy došlo k selhání, ***TryGetEndpoint*** tj Při pokusu o načtení koncový bod služby pro předávání k. 
   * **Detaily chyby**: Uvádí kritéria hledání koncový bod. Tady vidíte, že zadaný listenerName = **FrontEndListener** vzhledem seznamu koncového bodu repliky obsahuje pouze naslouchací proces s názvem **OldListener**.
    
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
     Další příklad, kdy reverzní proxy server může vrátit kód 404 Nenalezeno je: Parametr konfigurace ApplicationGateway\Http **SecureOnlyMode** nastavenou na hodnotu true pomocí reverzní proxy server naslouchá na **HTTPS**, ale jsou všechny koncové body repliky nezabezpečené (naslouchání na protokolu HTTP).
     Reverzního proxy vrátí 404, protože nemůže najít koncový bod naslouchání na adrese HTTPS pro tento požadavek předá dál. Analýza parametrů v případě, že datová část umožňuje zúžit problém:
    
     ```
      "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
     ```

3. Požadavek na reverzní proxy server selže s chybou vypršení časového limitu. 
    Protokoly událostí obsahují události s podrobnostmi byl přijat požadavek (není tady zobrazené).
    Další události ukazuje, že služba odpověděla stavový kód 404 a reverzní proxy server zahájí znovu přeložit. 

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
    Při shromažďování všech událostí, uvidíte do vlaku zobrazující každé řešení a dopředné pokus o události.
    Poslední událost z této série ukazuje, že zpracování žádosti se nezdařilo s časovým limitem, spolu s počtem pokusů o přihlášení úspěšné vyřešit.
    
    > [!NOTE]
    > Doporučujeme zachovat shromažďování událostí podrobné kanál ve výchozím nastavení zakázána a povolit pro řešení potíží s podle potřeby.

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
    
    Pokud kolekce je povoleno pouze kritické chyby nebo události, zobrazí se jedna událost s podrobnostmi o časový limit a počet pokusů o vyřešení. 
    
    Služby, které chcete odeslat stavový kód 404 zpět uživateli, by měl přidat hlavičku "X-ServiceFabric" v odpovědi. Po přidání hlavičky odpovědi reverzní proxy server stavový kód předá zpět do klienta.  

4. Případech, kdy klient se odpojil požadavku.

    Tato událost je zaznamenána při reverzní proxy server je předávání odpověď do klienta, ale klient neodpojí:

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
5. Reverse Proxy returns 404 FABRIC_E_SERVICE_DOES_NOT_EXIST

    FABRIC_E_SERVICE_DOES_NOT_EXIST chyba je vrácena, pokud není zadáno schéma identifikátoru URI pro koncový bod služby v manifestu služby.

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    Chcete-li vyřešit tento problém, určete v manifestu schéma identifikátoru URI.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> Aktuálně nejsou protokolovány události související se zpracování požadavků protokolu websocket. Tím se přidají v další vydané verzi.

## <a name="next-steps"></a>Další postup
* [Agregace událostí a kolekce pomocí Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) pro povolení shromažďování protokolů v clusterech Azure.
* Pokud chcete zobrazit události Service Fabric v sadě Visual Studio, naleznete v tématu [monitorování a Diagnostika místně](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
* Odkazovat na [konfigurace reverzního proxy serveru pro připojení k zabezpečení služeb](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) pro Azure Resource Manager zabezpečení ukázkové šablony konfigurace reverzního proxy serveru s jinou službu certifikátem možnosti ověřování.
* Čtení [Service Fabric reverzní proxy server](service-fabric-reverseproxy.md) Další informace.
