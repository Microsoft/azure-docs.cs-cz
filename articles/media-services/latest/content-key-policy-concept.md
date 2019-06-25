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
ms.date: 05/28/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: a597ab3519f4ba1696e111622541bcab89488558
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66425434"
---
# <a name="content-key-policies"></a>Zásady symetrických klíčů

Pomocí služby Media Services můžete doručovat na vyžádání a živé obsah dynamicky šifrován Advanced Encryption Standard (AES-128) nebo některým z tři systémů hlavní digital rights management (DRM): Microsoft PlayReady, Google Widevine a Apple FairPlay. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM (PlayReady, Widevine a FairPlay) licence autorizovaným klientům. 

Postup určení možností šifrování na datový proud, je potřeba vytvořit [streamování zásad](streaming-policy-concept.md) a přidružte jej k vaší [Lokátor streamování](streaming-locators-concept.md). Vytvoříte [zásad klíče k obsahu](https://docs.microsoft.com/rest/api/media/contentkeypolicies) nakonfigurovat jak klíč obsahu (, který zajišťuje zabezpečený přístup k vaší [prostředky](assets-concept.md)) se doručí do koncovým klientům. Je nutné nastavit požadavky (omezení) na obsahu zásad klíče, které musí splnit, aby klíče se zadaným nastavením, který bude doručen do klientů. Tato zásada obsahu klíč není potřeba pro streamování nebo stažení. 

Obvykle přidružit vaše **obsahu zásad klíče** s vaší [Lokátor streamování](streaming-locators-concept.md). Alternativně můžete zadat obsahu zásad klíče uvnitř [streamování zásad](streaming-policy-concept.md) (při vytváření vlastních zásad streamování pro pokročilé scénáře). 

Doporučujeme nechat Media Services automaticky vygenerovat symetrické klíče. Obvykle by použít dlouhodobá klíč a provedení kontroly existence zásady s **získat**. K získání klíče, je třeba volat metodu samostatnou akci pro tajné kódy a přihlašovací údaje, najdete v následujícím příkladu.

**Obsah zásady klíčů** je možné aktualizovat. Může trvat až 15 minut pro doručení klíče mezipaměti aktualizace a vyzvednutí aktualizované zásady. 

> [!IMPORTANT]
> * Vlastnosti **obsahu zásady klíčů** jsou DateTime typu jsou vždy ve formátu UTC.
> * Navrhněte omezenou sadu zásad pro svůj účet Media Service a znovu je použít pro vaše lokátory streamování pokaždé, když jsou potřeba stejné možnosti. Další informace najdete v tématu [kvóty a omezení](limits-quotas-constraints.md).

### <a name="example"></a>Příklad:

Chcete-li získat klíče, použijte **GetPolicyPropertiesWithSecretsAsync**, jak je znázorněno [získejte podpisový klíč ze stávající zásady](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets) příklad.

## <a name="filtering-ordering-paging"></a>Filtrování, řazení, stránkování

Zobrazit [filtrování, řazení, stránkování, Media Services entit](entities-overview.md).

## <a name="next-steps"></a>Další postup

* [Použití dynamického šifrování AES-128 a doručení klíče služby](protect-with-aes128.md)
* [Pomocí DRM dynamického šifrování a licence služby pro doručování](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
