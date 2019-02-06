---
title: Streamování lokátory ve službě Azure Media Services | Dokumentace Microsoftu
description: Tento článek obsahuje vysvětlení, co jsou lokátory streamování a jak se používají Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/03/2019
ms.author: juliako
ms.openlocfilehash: be66dcf8115258b6f593ec913e75785a3f8dbe1f
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55743476"
---
# <a name="streaming-locators"></a>Lokátory streamování

Chcete-li videa ve výstupu prostředek k dispozici pro klienty pro přehrávání, je nutné vytvořit [Lokátor streamování](https://docs.microsoft.com/rest/api/media/streaminglocators) a následně vytvořit adresy URL pro streamování. Ukázku .NET najdete v tématu [získat Lokátor streamování](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Proces vytváření **Lokátor streamování** nazývá publikování. Ve výchozím nastavení **Lokátor streamování** platnost okamžitě po provedení volání rozhraní API a trvá, dokud je odstraníme, pokud nenakonfigurujete volitelné počáteční a koncový čas. 

Při vytváření **Lokátor streamování**, je třeba zadat [Asset](https://docs.microsoft.com/rest/api/media/assets) název a [streamování zásad](https://docs.microsoft.com/rest/api/media/streamingpolicies) název. Můžete buď použít jednu z předdefinovaných zásad streamování nebo vytvořit vlastní zásadu. Předdefinované zásady, které jsou aktuálně k dispozici jsou: "Predefined_DownloadOnly", "Predefined_ClearStreamingOnly", "Predefined_DownloadAndClearStreaming", "Predefined_ClearKey", "Predefined_MultiDrmCencStreaming" a "Predefined_MultiDrmStreaming". Při použití vlastního streamování zásad, doporučujeme navrhnout omezenou sadu zásad pro svůj účet Media Service a je znovu použít pro vaše lokátory streamování pokaždé, když se stejnými možnostmi a protokoly jsou potřeba. 

Pokud chcete zadat možnosti šifrování na datový proud, vytvořte [zásad klíče k obsahu](https://docs.microsoft.com/rest/api/media/contentkeypolicies) , který konfiguruje, jak je klíč k obsahu doručit koncovým klientům prostřednictvím součást doručení klíče služby Media Services. Přidružit vaše Lokátor streamování s **zásad klíče k obsahu** a klíče k obsahu. Služba Media Services můžete nechat automaticky vygenerovat klíč. Následující příklad .NET ukazuje, jak nakonfigurovat šifrování AES omezení s tokenem v Media Services v3: [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted). **Obsah zásady klíčů** jsou aktualizovat, můžete chtít aktualizovat zásady, pokud je třeba provést obměny klíče. Může trvat až 15 minut pro doručení klíče mezipaměti aktualizace a vyzvednutí aktualizované zásady. Doporučuje se tak, aby nevytvářela nové zásady obsahu klíč pro každý Lokátor streamování. Pokuste se znovu použít existující zásady, kdykoli je to stejné možnosti jsou třeba.

> [!IMPORTANT]
> * Vlastnosti **lokátory streamování** jsou DateTime typu jsou vždy ve formátu UTC.
> * Navrhněte omezenou sadu zásad pro svůj účet Media Service a je znovu použít pro vaše lokátory streamování pokaždé, když jsou potřeba stejné možnosti. 

## <a name="filtering-ordering-paging"></a>Filtrování, řazení, stránkování

Zobrazit [filtrování, řazení, stránkování, Media Services entit](entities-overview.md).

## <a name="next-steps"></a>Další postup

* [Kurz: Nahrávání, kódování a streamování videí pomocí .NET](stream-files-tutorial-with-api.md)
* [Pomocí DRM dynamického šifrování a licence služby pro doručování](protect-with-drm.md)
