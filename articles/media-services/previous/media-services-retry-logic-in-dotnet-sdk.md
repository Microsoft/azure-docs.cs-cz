---
title: Logika opakování v sadě Media Services SDK pro .NET | Microsoft Docs
description: Téma obsahuje přehled logiky opakování v sadě Media Services SDK pro .NET.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 120b7e044452dc47126923449a3e1a6e55cfd6a8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87000019"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Logika opakování v sadě Media Services SDK pro .NET  

Při práci s Microsoft Azure Services může docházet k přechodným chybám. Pokud dojde k přechodným chybám, ve většině případů po několika opakovaných pokusech operace proběhne úspěšně. Sada Media Services SDK pro .NET implementuje logiku opakování, která zpracovává přechodné chyby spojené s výjimkami a chybami, které jsou způsobené webovými požadavky, spouští dotazy, ukládají změny a operace úložiště.  Ve výchozím nastavení spustí sada Media Services SDK pro .NET čtyři opakované pokusy před opětovnou vyvoláním výjimky do vaší aplikace. Kód v aplikaci musí následně zpracovat tuto výjimku správně.  

 Následuje stručný návod pro zásady webového požadavku, úložiště, dotazu a SaveChanges:  

* Zásady úložiště se používají pro operace BLOB Storage (nahrávání nebo stahování souborů assetů).  
* Zásada webového požadavku se používá pro obecné webové požadavky (například pro získání ověřovacího tokenu a k překladu koncového bodu clusteru uživatelů).  
* Zásada dotazu se používá pro dotazování entit z REST (například mediaContext. assets. Where (...)).  
* Zásada SaveChanges se používá pro cokoli, co mění data v rámci služby (například vytvořením entity, která aktualizuje entitu, volání funkce služby pro operaci).  
  
  Toto téma obsahuje seznam typů výjimek a kódů chyb, které jsou zpracovávány Media Services SDK pro logiku opakování rozhraní .NET.  

## <a name="exception-types"></a>Typy výjimek
Následující tabulka popisuje výjimky, které sada Media Services SDK pro .NET zpracovává nebo nezpracovává u některých operací, které mohou způsobit přechodné chyby.  

| Výjimka | Webový požadavek | Storage | Dotaz | Metody |
| --- | --- | --- | --- | --- |
| WebException<br/>Další informace najdete v části [stavové kódy WebException](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) . |Ano |Ano |Ano |Ano |
| DataServiceClientException<br/> Další informace najdete v tématu [stavové kódy chyb protokolu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |No |Ano |Ano |Ano |
| DataServiceQueryException<br/> Další informace najdete v tématu [stavové kódy chyb protokolu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |No |Ano |Ano |Ano |
| DataServiceRequestException<br/> Další informace najdete v tématu [stavové kódy chyb protokolu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |No |Ano |Ano |Ano |
| DataServiceTransportException |No |No |Ano |Ano |
| TimeoutException |Ano |Ano |Ano |No |
| SocketException |Ano |Ano |Ano |Ano |
| StorageException |No |Yes |No |No |
| IOException |No |Yes |No |No |

### <a name="webexception-status-codes"></a><a name="WebExceptionStatus"></a>Stavové kódy WebException
Následující tabulka uvádí, pro které kódy chyb WebException je logika opakování implementovaná. Výčet [WebExceptionStatus rovným](/dotnet/api/system.net.webexceptionstatus?view=netcore-3.1) definuje stavové kódy.  

| Status | Webový požadavek | Storage | Dotaz | Metody |
| --- | --- | --- | --- | --- |
| ConnectFailure |Ano |Ano |Ano |Ano |
| NameResolutionFailure |Ano |Ano |Ano |Ano |
| ProxyNameResolutionFailure |Ano |Ano |Ano |Ano |
| SendFailure |Ano |Ano |Ano |Ano |
| PipelineFailure |Ano |Ano |Ano |No |
| ConnectionClosed |Ano |Ano |Ano |No |
| KeepAliveFailure |Ano |Ano |Ano |No |
| UnknownError |Ano |Ano |Ano |No |
| ReceiveFailure |Ano |Ano |Ano |No |
| RequestCanceled |Ano |Ano |Ano |No |
| Časový limit |Ano |Ano |Ano |No |
| ProtocolError <br/>Opakování na ProtocolError se řídí zpracováním kódu stavu HTTP. Další informace najdete v tématu [stavové kódy chyb protokolu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Ano |Ano |Ano |Ano |

### <a name="http-error-status-codes"></a><a name="HTTPStatusCode"></a>Stavové kódy chyb HTTP
Když operace Query a SaveChanges vyvolají DataServiceClientException, DataServiceQueryException nebo DataServiceQueryException, vrátí se kód stavu chyby HTTP ve vlastnosti StatusCode.  Následující tabulka uvádí, které chybové kódy jsou implementací logiky opakování implementovány.  

| Status | Webový požadavek | Storage | Dotaz | Metody |
| --- | --- | --- | --- | --- |
| 401 |No |Yes |No |No |
| 403 |No |Yes<br/>Zpracování opakovaných pokusů s delší dobou čekání. |No |No |
| 408 |Ano |Ano |Ano |Ano |
| 429 |Ano |Ano |Ano |Ano |
| 500 |Ano |Ano |Ano |No |
| 502 |Ano |Ano |Ano |No |
| 503 |Ano |Ano |Ano |Ano |
| 504 |Ano |Ano |Ano |No |

Pokud se chcete podívat na skutečnou implementaci logiky opakování pro sadu Media Services SDK pro .NET, přečtěte si téma [Azure-SDK-for-Media-Services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Další kroky
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
