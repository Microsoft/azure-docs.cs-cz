---
title: Obsah zásady klíčů ve službě Media Services – Azure | Dokumentace Microsoftu
description: Tento článek obsahuje vysvětlení, co jsou zásady obsahu klíčů a jejich použití Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/22/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 68778cea51144ec33efd4d5843a51b489ea17ca4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155725"
---
# <a name="content-key-policies"></a>Zásady symetrických klíčů

Pomocí služby Media Services můžete doručovat na vyžádání a živé obsah dynamicky šifrován Advanced Encryption Standard (AES-128) nebo některým z tři systémů hlavní digital rights management (DRM): Microsoft PlayReady, Google Widevine a Apple FairPlay. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM (PlayReady, Widevine a FairPlay) licence autorizovaným klientům.

Postup určení možností šifrování na datový proud, je potřeba vytvořit [streamování zásad](streaming-policy-concept.md) a přidružte jej k vaší [Lokátor streamování](streaming-locators-concept.md). Je potřeba vytvořit [zásad klíče k obsahu](https://docs.microsoft.com/rest/api/media/contentkeypolicies) konfigurace jak klíč obsahu (, který zajišťuje zabezpečený přístup k vaší [prostředky](assets-concept.md)) se doručí do koncovým klientům. **Obsahu zásad klíče** je také přidružené k vaší **Lokátor streamování**. Je nutné nastavit požadavky (omezení) na obsahu zásad klíče, které musí splnit, aby klíče se zadaným nastavením, který bude doručen do klientů. 

Doporučujeme nechat Media Services automaticky vygenerovat symetrické klíče. Obvykle by použít dlouhodobá klíč a provedení kontroly existence zásady s **získat**. K získání klíče, je třeba volat metodu samostatnou akci pro tajné kódy a přihlašovací údaje, najdete v následujícím příkladu.

**Obsah zásady klíčů** je možné aktualizovat. Například můžete chtít aktualizovat zásady, pokud je třeba provést obměny klíče. Můžete aktualizovat primární ověřovací klíč a seznam klíčů alternativní ověření v existující zásady. Může trvat až 15 minut pro doručení klíče mezipaměti aktualizace a vyzvednutí aktualizované zásady. 

> [!IMPORTANT]
> * Vlastnosti **obsahu zásady klíčů** jsou DateTime typu jsou vždy ve formátu UTC.
> * Navrhněte omezenou sadu zásad pro svůj účet Media Service a znovu je použít pro vaše lokátory streamování pokaždé, když jsou potřeba stejné možnosti. Další informace najdete v tématu [kvóty a omezení](limits-quotas-constraints.md).

## <a name="example"></a>Příklad:

Chcete-li získat klíče, použijte **GetPolicyPropertiesWithSecretsAsync**, jak je znázorněno v následujícím příkladu.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="filtering-ordering-paging"></a>Filtrování, řazení, stránkování

Zobrazit [filtrování, řazení, stránkování, Media Services entit](entities-overview.md).

## <a name="next-steps"></a>Další postup

* [Použití dynamického šifrování AES-128 a doručení klíče služby](protect-with-aes128.md)
* [Pomocí DRM dynamického šifrování a licence služby pro doručování](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
