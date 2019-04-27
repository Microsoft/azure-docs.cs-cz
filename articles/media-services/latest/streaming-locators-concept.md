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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 51aa33e4ff387a1030dac42bce8d12cf72343b35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466727"
---
# <a name="streaming-locators"></a>Lokátory streamování

Pokud chcete, aby videa ve výstupním prostředku byla k dispozici klientům pro přehrávání, musíte vytvořit [lokátor streamování](https://docs.microsoft.com/rest/api/media/streaminglocators) a adresy URL pro streamování. Ukázku v .NET najdete v tématu věnovaném [získání lokátoru streamování](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Proces vytváření **Lokátor streamování** nazývá publikování. Ve výchozím nastavení **Lokátor streamování** platnost okamžitě po provedení volání rozhraní API a trvá, dokud je odstraníme, pokud nenakonfigurujete volitelné počáteční a koncový čas. 

Při vytváření **Lokátor streamování**, je třeba zadat [Asset](https://docs.microsoft.com/rest/api/media/assets) název a [streamování zásad](https://docs.microsoft.com/rest/api/media/streamingpolicies) název. Můžete buď použít jednu z předdefinovaných zásad streamování nebo vytvořit vlastní zásadu. Předdefinované zásady, které jsou aktuálně k dispozici jsou: "Predefined_DownloadOnly", "Predefined_ClearStreamingOnly", "Predefined_DownloadAndClearStreaming", "Predefined_ClearKey", "Predefined_MultiDrmCencStreaming" a "Predefined_MultiDrmStreaming". Při použití vlastního streamování zásad, doporučujeme navrhnout omezenou sadu zásad pro svůj účet Media Service a je znovu použít pro vaše lokátory streamování pokaždé, když se stejnými možnostmi a protokoly jsou potřeba. 

Pokud chcete zadat možnosti šifrování na datový proud, vytvořte [zásad klíče k obsahu](https://docs.microsoft.com/rest/api/media/contentkeypolicies) , který konfiguruje, jak je klíč k obsahu doručit koncovým klientům prostřednictvím součást doručení klíče služby Media Services. Přidružit vaše Lokátor streamování s **zásad klíče k obsahu** a klíče k obsahu. Služba Media Services můžete nechat automaticky vygenerovat klíč. Následující příklad .NET ukazuje, jak nakonfigurovat šifrování AES omezení s tokenem v Media Services v3: [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted). **Obsah zásady klíčů** jsou aktualizovat, můžete chtít aktualizovat zásady, pokud je třeba provést obměny klíče. Může trvat až 15 minut pro doručení klíče mezipaměti aktualizace a vyzvednutí aktualizované zásady. Doporučuje se tak, aby nevytvářela nové zásady obsahu klíč pro každý Lokátor streamování. Pokuste se znovu použít existující zásady, kdykoli je to stejné možnosti jsou třeba.

> [!IMPORTANT]
> * Vlastnosti **lokátory streamování** jsou DateTime typu jsou vždy ve formátu UTC.
> * Navrhněte omezenou sadu zásad pro svůj účet Media Service a je znovu použít pro vaše lokátory streamování pokaždé, když jsou potřeba stejné možnosti. 

## <a name="associate-filters-with-streaming-locators"></a>Lokátory streamování přidružit filtry

Můžete zadat seznam [asset nebo účet filtrů](filters-concept.md), které bude platit pro vaše [Lokátor streamování](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body). [Dynamické packager](dynamic-packaging-overview.md) platí tento seznam filtrů společně s ty klientem v adrese URL. Tato kombinace generuje [dyanamic manifestu](filters-dynamic-manifest-overview.md), která je založena na filtry v adrese URL a filtry, které jste zadali na Lokátor streamování. Doporučujeme použít tuto funkci, pokud chcete použít filtry, ale nechcete, aby k vystavení filtr názvů v adrese URL.

## <a name="filter-order-page-streaming-locator-entities"></a>Filtr, pořadí, stránka Lokátor streamování entity

Zobrazit [filtrování, řazení, stránkování, Media Services entit](entities-overview.md).

## <a name="next-steps"></a>Další postup

* [Kurz: Nahrávání, kódování a streamování videí pomocí .NET](stream-files-tutorial-with-api.md)
* [Použití ochrany DRM s dynamickým šifrováním a služby doručování licencí](protect-with-drm.md)
