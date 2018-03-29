---
title: Azure Service Fabric reverse proxy diagnostiky | Microsoft Docs
description: Zjistěte, jak sledovat a diagnostikovat zpracování požadavku na reverzní proxy server.
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: e645d86e4527cab1e650575c4d9a10dbd4b9de4e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>Monitorování a diagnostikovat zpracování požadavku na reverzní proxy server

Události reverzní proxy server, počínaje 5.7 verzi Service Fabric, jsou k dispozici pro kolekci. Události jsou k dispozici ve dvou kanálů, jeden s pouze chybové události související s selhání zpracování požadavku na reverzní proxy server a druhý kanál obsahující podrobné události se záznamy pro úspěšných i neúspěšných požadavků.

Odkazovat na [shromažďování událostí reverzní proxy server](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations) Chcete-li povolit shromažďování událostí z těchto kanálů v místní a clustery Azure Service Fabric.

## <a name="troubleshoot-using-diagnostics-logs"></a>Řešení problémů pomocí protokolů diagnostiky
Zde jsou některé příklady o tom, jak interpretovat běžné selhání protokoly, které se můžete setkat jeden:

1. Reverzní proxy server vrátí stavový kód odpovědi 504 (časový limit).

    Jedním z důvodů může být z důvodu selhání odpověď v rámci časového limitu žádosti o služby.
První událost pod protokoly podrobnosti žádosti o přijme reverzní proxy server. Druhá událost označuje, že požadavek se nepodařilo při předávání služby, z důvodu "vnitřní chyba = ERROR_WINHTTP_TIMEOUT" 

    Datová část obsahuje:

    *  **traceId**: Tento identifikátor GUID slouží ke korelaci všechny události odpovídající jedné žádosti. V nižší než dvě události, traceId = **2f87b722-e254-4ac2-a802-fd315c1a0271**, zdání patří ke stejnému požadavku.
    *  **requestUrl**: adresa URL (URL reverzní proxy server), do které byl odeslán požadavek.
    *  **verb**: HTTP verb.
    *  **remoteAddress**: adresa odesílání požadavku klienta.
    *  **resolvedServiceUrl**: adresa URL koncového bodu, ke kterému byl vyřešen příchozí žádosti o služby. 
    *  **Detaily chyby**: Další informace o selhání.

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

2. Reverzní proxy server vrátí stavový kód odpovědi 404 (není nalezena). 
    
    Tady je příklad událost kde reverzní proxy server vrátí 404, protože se nepodařilo najít odpovídající koncový bod služby.
    Datové položky popište jsou:
    *  **processRequestPhase**: označuje fázi při zpracování požadavku, pokud došlo k selhání, ***TryGetEndpoint*** jednofaktorovému Při pokusu o načtení koncový bod služby pro předávání. 
    *  **Detaily chyby**: uvádí kritéria vyhledávání koncový bod. Zde se zobrazí, zda listenerName zadán = **FrontEndListener** zatímco seznam koncových bodů repliky obsahuje pouze naslouchací proces s názvem **OldListener**.
    
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
    Další příklad, kdy vrátit 404 reverzní proxy server nebyl nalezen je: ApplicationGateway\Http konfigurační parametr **SecureOnlyMode** nastaven na hodnotu true s reverzní proxy server naslouchá na **HTTPS**, ale všechny koncové body repliky nezabezpečeného (naslouchá na protokolu HTTP).
    Vzhledem k tomu, že nemůže najít naslouchá na HTTPS k předání požadavku koncového bodu se vrátit proxy vrátí 404. V případě, že datové části pomáhá zúžit problém, analýza parametry:
    
    ```
        "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
    ```

3. Požadavek na reverzní proxy server selže s chybou časový limit. 
    Protokoly událostí obsahují událost s podrobnostmi přijatý požadavek (není tady zobrazené).
    Následující události ukazuje, že služba odpověděla s 404 stavový kód a zahájí reverzní proxy server znovu vyřešit. 

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
    Při shromažďování všech událostí, uvidíte train událostí zobrazující každých vyřešte a předat dál pokus.
    Poslední událost v řadě ukazuje, že zpracování žádosti se nezdařilo s vypršení časového limitu, spolu s počtem pokusy o vyřešení úspěšné.
    
    > [!NOTE]
    > Doporučuje se ji povolit pro řešení potíží s na základě potřeba a zachovat shromažďování událostí podrobné kanál, který je ve výchozím nastavení zakázaná.

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
    
    Pokud kolekce je povolený pro události kritickou chybou pouze, zobrazí se jedna událost s podrobnostmi o časový limit a počet pokusů o vyřešení. 
    
    Služby, které chcete odeslat kód 404 stavu zpět uživateli, měli přidat hlavičku "X-ServiceFabric" v odpovědi. Po přidání hlavičky odpovědi reverzní proxy server předává kód stavu zpět do klienta.  

4. Případech, kdy klient se odpojil žádosti.

    Pokud reverzní proxy server je předávání odpověď klientovi, ale data se klient neodpojí je zaznamenána následující událost:

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
5. Vrátí 404 FABRIC_E_SERVICE_DOES_NOT_EXIST reverzní proxy server

    FABRIC_E_SERVICE_DOES_NOT_EXIST chyba je vrácena, pokud není zadán schéma identifikátoru URI pro koncový bod služby v service manifest.

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    Chcete-li problém vyřešit, zadejte schéma identifikátoru URI v manifestu.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> Aktuálně nejsou protokolovány události související s zpracování požadavků protokolu websocket. To bude přidán v příští verzi.

## <a name="next-steps"></a>Další postup
* [Seskupení událostí a kolekce s použitím Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) pro povolení shromažďování protokolů v Azure clustery.
* Chcete-li zobrazit události Service Fabric v sadě Visual Studio, najdete v části [monitorování a diagnostikovat místně](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
* Odkazovat na [konfigurovat reverzní proxy server pro připojení k zabezpečení služeb](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) pro Azure Resource Manager šablony ukázky konfigurace zabezpečení reverzní proxy server s jinou službu certifikátem možnosti ověřování.
* Čtení [Service Fabric reverse proxy](service-fabric-reverseproxy.md) Další informace.
