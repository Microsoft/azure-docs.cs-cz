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
ms.date: 02/03/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: d9e86c45d535862e0c3d02b3f331bc40ebb7f6c7
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745117"
---
# <a name="content-key-policies"></a>Zásady symetrických klíčů

Pomocí služby Media Services můžete doručovat na vyžádání a živé obsah dynamicky šifrován Advanced Encryption Standard (AES-128) nebo některým z tři systémů hlavní digital rights management (DRM): Microsoft PlayReady, Google Widevine a Apple FairPlay. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM (PlayReady, Widevine a FairPlay) licence autorizovaným klientům.

Postup určení možností šifrování na datový proud, je potřeba vytvořit [obsahu zásad klíče](https://docs.microsoft.com/rest/api/media/contentkeypolicies) a přidružte jej k vaší **Lokátor streamování**. **Zásad klíče k obsahu** nakonfiguruje, jak je klíč k obsahu doručit koncovým klientům prostřednictvím součást doručení klíče služby Media Services. Služba Media Services můžete nechat automaticky vygenerovat klíče k obsahu. Obvykle by použít dlouhodobá klíč a provedení kontroly existence zásady pomocí Get. K získání klíče, je třeba volat metodu samostatnou akci pro tajné kódy a přihlašovací údaje, najdete v následujícím příkladu.

**Obsah zásady klíčů** je možné aktualizovat. Například můžete chtít aktualizovat zásady, pokud je třeba provést obměny klíče. Můžete aktualizovat primární ověřovací klíč a seznam klíčů alternativní ověření v existující zásady. Může trvat až 15 minut pro doručení klíče mezipaměti aktualizace a vyzvednutí aktualizované zásady. 

> [!IMPORTANT]
> * Vlastnosti **obsahu zásady klíčů** jsou DateTime typu jsou vždy ve formátu UTC.
> * Navrhněte omezenou sadu zásad pro svůj účet Media Service a znovu je použít pro vaše lokátory streamování pokaždé, když jsou potřeba stejné možnosti. 

## <a name="example"></a>Příklad:

Chcete-li získat klíče, použijte **GetPolicyPropertiesWithSecretsAsync**, jak je znázorněno v následujícím příkladu.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="filtering-ordering-paging"></a>Filtrování, řazení, stránkování

Zobrazit [filtrování, řazení, stránkování, Media Services entit](entities-overview.md).

## <a name="next-steps"></a>Další postup

* [Použití dynamického šifrování AES-128 a doručení klíče služby](protect-with-aes128.md)
* [Pomocí DRM dynamického šifrování a licence služby pro doručování](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
