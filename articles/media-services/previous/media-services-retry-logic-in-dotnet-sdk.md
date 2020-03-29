---
title: Logika opakování v sadě Media Services SDK pro rozhraní .NET | Dokumenty společnosti Microsoft
description: Toto téma poskytuje přehled logiky opakování v sadě Media Services SDK pro rozhraní .NET.
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
ms.openlocfilehash: 63715f668438519131eba5bfff7aa38fc73267d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61094647"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Logika opakování v sadě Media Services SDK pro rozhraní .NET  

Při práci se službami Microsoft Azure může dojít k přechodným chybám. Pokud dojde k přechodné chybě, ve většině případů po několika opakovaných pokusech operace proběhne úspěšně. Sada Media Services SDK pro rozhraní .NET implementuje logiku opakování pro zpracování přechodných chyb spojených s výjimkami a chybami, které jsou způsobeny webovými požadavky, prováděním dotazů, ukládáním změn a operacemi úložiště.  Ve výchozím nastavení sada Media Services SDK pro rozhraní .NET provede čtyři pokusy před opětovným vyvoláním výjimky do vaší aplikace. Kód v aplikaci pak musí správně zpracovat tuto výjimku.  

 Následuje stručné pokyny pro zásady webových požadavků, úložiště, dotazů a savechanges:  

* Zásady úložiště se používají pro operace úložiště objektů blob (nahrávání nebo stahování souborů datových zdrojů).  
* Zásady webového požadavku se používají pro obecné webové požadavky (například pro získání ověřovacího tokenu a řešení koncového bodu clusteru uživatelů).  
* Zásada dotazu se používá pro dotazování entit z REST (například mediaContext.Assets.Where(...)).  
* Zásada SaveChanges se používá k provedení cokoli, co změní data v rámci služby (například vytvoření entity aktualizace entity, volání funkce služby pro operaci).  
  
  Toto téma obsahuje seznam typů výjimek a kódů chyb, které jsou zpracovány sadou Media Services SDK pro logiku opakování rozhraní .NET.  

## <a name="exception-types"></a>Typy výjimek
Následující tabulka popisuje výjimky, které sada Media Services SDK pro rozhraní .NET zpracovává nebo nezpracovává pro některé operace, které mohou způsobit přechodné chyby.  

| Výjimka | Webový požadavek | Úložiště | Dotaz | Savechanges |
| --- | --- | --- | --- | --- |
| Webexception<br/>Další informace naleznete v části [Kódy stavu WebException.](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) |Ano |Ano |Ano |Ano |
| Výjimka klienta datového mechanismu<br/> Další informace naleznete v tématu [Chybové kódy chyb protokolu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Ne |Ano |Ano |Ano |
| Výjimka dataservicequeryexception<br/> Další informace naleznete v tématu [Chybové kódy chyb protokolu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Ne |Ano |Ano |Ano |
| Výjimka DataServiceRequestException<br/> Další informace naleznete v tématu [Chybové kódy chyb protokolu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Ne |Ano |Ano |Ano |
| Výjimka TransportService |Ne |Ne |Ano |Ano |
| Timeoutexception |Ano |Ano |Ano |Ne |
| Výjimka socketu |Ano |Ano |Ano |Ano |
| StorageException |Ne |Ano |Ne |Ne |
| Ioexception |Ne |Ano |Ne |Ne |

### <a name="webexception-status-codes"></a><a name="WebExceptionStatus"></a>Stavové kódy WebException
V následující tabulce je uvedeno, pro které kódy chyb WebException je implementována logika opakování. Výčet [WebExceptionStatus](https://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) definuje stavové kódy.  

| Status | Webový požadavek | Úložiště | Dotaz | Savechanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |Ano |Ano |Ano |Ano |
| NázevResolutionFailure |Ano |Ano |Ano |Ano |
| Selhání rezoluce__sychu |Ano |Ano |Ano |Ano |
| SendFailure |Ano |Ano |Ano |Ano |
| Selhání kanálu |Ano |Ano |Ano |Ne |
| Připojení uzavřeno |Ano |Ano |Ano |Ne |
| KeepAliveSelhání |Ano |Ano |Ano |Ne |
| Neznámýchyba |Ano |Ano |Ano |Ne |
| ReceiveFailure |Ano |Ano |Ano |Ne |
| Požadavek byl zrušen. |Ano |Ano |Ano |Ne |
| Časový limit |Ano |Ano |Ano |Ne |
| Chyba protokolu <br/>Opakování chyby protokolu je řízeno zpracováním stavového kódu HTTP. Další informace naleznete v tématu [Chybové kódy chyb protokolu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Ano |Ano |Ano |Ano |

### <a name="http-error-status-codes"></a><a name="HTTPStatusCode"></a>Stavové kódy chyb protokolu HTTP
Když operace Query a SaveChanges spustí DataServiceClientException, DataServiceQueryException nebo DataServiceQueryException, je ve vlastnosti StatusCode vrácen stavový kód chyby HTTP.  V následující tabulce jsou uvedeny kódy chyb, pro které je implementována logika opakování.  

| Status | Webový požadavek | Úložiště | Dotaz | Savechanges |
| --- | --- | --- | --- | --- |
| 401 |Ne |Ano |Ne |Ne |
| 403 |Ne |Ano<br/>Zpracování opakování s delší čekání. |Ne |Ne |
| 408 |Ano |Ano |Ano |Ano |
| 429 |Ano |Ano |Ano |Ano |
| 500 |Ano |Ano |Ano |Ne |
| 502 |Ano |Ano |Ano |Ne |
| 503 |Ano |Ano |Ano |Ano |
| 504 |Ano |Ano |Ano |Ne |

Pokud se chcete podívat na skutečné implementaci sady Media Services SDK pro logiku opakování rozhraní .NET, [přečtěte si viz azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Další kroky
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

