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
ms.date: 07/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 7ddef1e78b4f8f62145e10b4cabc4537e28aba2f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969900"
---
# <a name="content-key-policies"></a>Zásady symetrických klíčů

Pomocí služby Media Services můžete doručovat na vyžádání a živého obsahu dynamicky šifrován Advanced Encryption Standard (AES-128) nebo některý z systémy tři hlavní digital rights management (DRM): Apple FairPlay, Microsoft PlayReady a Google Widevine. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM (PlayReady, Widevine a FairPlay) licence autorizovaným klientům. 

Pokud chcete pro svůj Stream zadat možnosti šifrování, musíte vytvořit [zásadu streamování](streaming-policy-concept.md) a přidružit ji k [lokátoru streamování](streaming-locators-concept.md). [Zásadu pro klíč obsahu](https://docs.microsoft.com/rest/api/media/contentkeypolicies) vytvoříte pro konfiguraci způsobu doručení klíče obsahu (který poskytuje zabezpečený přístup k vašim [prostředkům](assets-concept.md)) koncovým klientům. Je nutné nastavit požadavky (omezení) na zásady klíče obsahu, které musí být splněny, aby bylo možné klíče se zadanou konfigurací doručovat klientům. Zásady klíče obsahu není potřeba pro vymazání streamování nebo stahování. 

Obvykle přidružíte zásady klíčů obsahu k [lokátoru streamování](streaming-locators-concept.md). Případně můžete určit zásady klíče obsahu v rámci [zásad streamování](streaming-policy-concept.md) (při vytváření vlastních zásad streamování pro pokročilé scénáře). 

## <a name="best-practices-and-considerations"></a>Osvědčené postupy a požadavky

> [!IMPORTANT]
> Přečtěte si následující doporučení.

* Měli byste navrhnout omezené sady zásad pro svůj účet Media Service a znovu je použít pro Lokátory streamování, pokud jsou potřeba stejné možnosti. Další informace najdete v tématu [kvóty a omezení](limits-quotas-constraints.md).
* Zásady klíčů obsahu je možné aktualizovat. Aktualizace a výběr aktualizované zásady může trvat až 15 minut, než se mezipaměť pro doručování klíčů aktualizuje. 

   Když tuto zásadu aktualizujete, přepíšete stávající mezipaměť CDN, která by mohla způsobit problémy s přehráváním pro zákazníky, kteří používají obsah uložený v mezipaměti.  
* Doporučujeme, abyste pro každý Asset nevytvořili nové zásady klíče obsahu. Hlavními výhodami sdílení stejných zásad pro klíč obsahu mezi prostředky, které potřebují stejné možnosti zásad, jsou:
   
   * Správa malého počtu zásad je snazší.
   * Pokud potřebujete provést aktualizace zásad klíče obsahu, změny se projeví u všech nových požadavků na licence téměř hned.
* Pokud potřebujete vytvořit novou zásadu, budete muset vytvořit nový Lokátor streamování pro daný prostředek.
* Doporučuje se, aby Media Services vygenerovat klíč obsahu. 

   Obvykle byste používali dlouhotrvající klíč a kontrolovali existenci zásad klíče obsahu pomocí [Get](https://docs.microsoft.com/rest/api/media/contentkeypolicies/get). Chcete-li získat klíč, je nutné zavolat samostatnou metodu akce pro získání tajných kódů nebo přihlašovacích údajů, viz následující příklad.

## <a name="example"></a>Příklad:

Pokud se chcete dostat do klíče, použijte `GetPolicyPropertiesWithSecretsAsync`, jak je znázorněno v příkladu [získání podpisového klíče v existující zásadě](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets) .

## <a name="filtering-ordering-paging"></a>Filtrování, řazení, stránkování

Viz téma [filtrování, řazení, stránkování Media Services entit](entities-overview.md).

## <a name="additional-notes"></a>Další poznámky

* Vlastnosti zásad klíče obsahu, které jsou typu `Datetime`, jsou vždy ve formátu UTC.
* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="next-steps"></a>Další kroky

* [Použití dynamického šifrování AES-128 a služby doručování klíčů](protect-with-aes128.md)
* [Použití ochrany DRM s dynamickým šifrováním a služby doručování licencí](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
