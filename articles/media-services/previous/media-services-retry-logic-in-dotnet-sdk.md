---
title: Logika opakování v sadě Media Services SDK pro .NET | Dokumentace Microsoftu
description: Téma s přehledem logika opakovaných pokusů v sadě Media Services SDK pro .NET.
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
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: 909a68ff0fd78fbdd4870506d1ad579392036dbf
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55999194"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Logika opakování v sadě Media Services SDK pro .NET  

Při práci se službami Microsoft Azure, přechodné chyby můžou nastat. Pokud dojde k přechodných chyb, ve většině případů po několika pokusech operace proběhne úspěšně. Media Services SDK pro .NET implementuje logika opakovaných pokusů pro zpracování přechodných chyb spojených s výjimek a chyb, které jsou způsobeny webových požadavků, provádění dotazů, ukládají se změny a operace úložiště.  Ve výchozím nastavení spustí sadu Media Services SDK pro .NET do čtyř opakovaných vyhledávání před opětném vyvolávání výjimky pro vaši aplikaci. Kód ve vaší aplikaci pak musí správně zpracovat tuto výjimku.  

 Tady je stručný návod webového požadavku, úložiště, dotaz a SaveChanges zásad:  

* Zásady úložiště se používá pro operace úložiště objektů blob (nahrávání nebo stahování souborů prostředků).  
* Zásada webového požadavku se používá pro žádosti o obecný web (například pro získání ověřovacího tokenu a jejich řešení koncový bod clusteru uživatele).  
* Zásada dotazu se používá k dotazování entit na základě REST (například mediaContext.Assets.Where(...)).  
* Zásada SaveChanges se používá pro teď zrovna nic nedělá, která se mění data v rámci služby (například vytvoření entity aktualizaci entity, volání funkce služby pro operace).  
  
  Toto téma uvádí typy výjimek a logika opakování chybové kódy, které provádí služba Media Services SDK pro .NET.  

## <a name="exception-types"></a>Typy výjimek
Následující tabulka popisuje výjimky, které Media Services SDK pro .NET, zpracovává nebo nezpracovává pro některé operace, které by mohly způsobit přechodné chyby.  

| Výjimka | Webový požadavek | Storage | Dotaz | SaveChanges |
| --- | --- | --- | --- | --- |
| WebException<br/>Další informace najdete v tématu [o výjimku WebException stavové kódy](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) oddílu. |Ano |Ano |Ano |Ano |
| DataServiceClientException<br/> Další informace najdete v tématu [stavové kódy HTTP Chyba](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Ne |Ano |Ano |Ano |
| DataServiceQueryException<br/> Další informace najdete v tématu [stavové kódy HTTP Chyba](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Ne |Ano |Ano |Ano |
| DataServiceRequestException<br/> Další informace najdete v tématu [stavové kódy HTTP Chyba](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Ne |Ano |Ano |Ano |
| DataServiceTransportException |Ne |Ne |Ano |Ano |
| TimeoutException |Ano |Ano |Ano |Ne |
| SocketException |Ano |Ano |Ano |Ano |
| StorageException |Ne |Ano |Ne |Ne |
| IOException – |Ne |Ano |Ne |Ne |

### <a name="WebExceptionStatus"></a> O výjimku WebException stavové kódy
V následující tabulce jsou uvedeny pro kódy chyb o výjimku WebException logika opakovaných pokusů implementovaná. [WebExceptionStatus](https://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) výčet definuje kódy stavu.  

| Status | Webový požadavek | Storage | Dotaz | SaveChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |Ano |Ano |Ano |Ano |
| NameResolutionFailure |Ano |Ano |Ano |Ano |
| ProxyNameResolutionFailure |Ano |Ano |Ano |Ano |
| SendFailure |Ano |Ano |Ano |Ano |
| PipelineFailure |Ano |Ano |Ano |Ne |
| ConnectionClosed |Ano |Ano |Ano |Ne |
| KeepAliveFailure |Ano |Ano |Ano |Ne |
| Neznámé chyby |Ano |Ano |Ano |Ne |
| ReceiveFailure |Ano |Ano |Ano |Ne |
| RequestCanceled |Ano |Ano |Ano |Ne |
| Vypršení časového limitu |Ano |Ano |Ano |Ne |
| Požadavku <br/>Opakování v požadavku se řídí zpracování kód stavu HTTP. Další informace najdete v tématu [stavové kódy HTTP Chyba](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Ano |Ano |Ano |Ano |

### <a name="HTTPStatusCode"></a> Stavové kódy chyb HTTP
Když operace dotazů a SaveChanges throw DataServiceClientException, DataServiceQueryException nebo DataServiceQueryException, se ve vlastnosti StatusCode vrátí stavový kód chyby protokolu HTTP.  V následující tabulce jsou uvedeny pro kódy chyb se implementuje logika opakovaných pokusů.  

| Status | Webový požadavek | Storage | Dotaz | SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |Ne |Ano |Ne |Ne |
| 403 |Ne |Ano<br/>Zpracování opakovaných pokusů s delší dobu čekání. |Ne |Ne |
| 408 |Ano |Ano |Ano |Ano |
| 429 |Ano |Ano |Ano |Ano |
| 500 |Ano |Ano |Ano |Ne |
| 502 |Ano |Ano |Ano |Ne |
| 503 |Ano |Ano |Ano |Ano |
| 504 |Ano |Ano |Ano |Ne |

Pokud chcete se podívat na aktuální implementaci Media Services SDK pro .NET logiku opakování, přečtěte si téma [azure-sdk pro media-services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Další postup
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

