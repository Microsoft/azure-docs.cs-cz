---
title: Zásady klíčů obsahu v Media Services – Azure
description: Tento článek obsahuje vysvětlení toho, jaké zásady klíčů obsahu jsou a jak se používají v Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 6e60292c817ccad8eb1dd6cb3c33b944ab1c18a0
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277676"
---
# <a name="content-key-policies"></a>Zásady symetrických klíčů

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Pomocí Media Services můžete doručovat živý obsah na vyžádání a dynamicky šifrovaný pomocí standard AES (Advanced Encryption Standard) (AES-128) nebo kteréhokoli ze tří hlavních systémů DRM (Digital Rights Management): Microsoft PlayReady, Google Widevine a Apple FairPlay. Media Services taky poskytuje službu pro doručování klíčů AES a licencí DRM (PlayReady, Widevine a FairPlay) autorizovaným klientům. 

Pokud chcete pro svůj Stream zadat možnosti šifrování, musíte vytvořit [zásadu streamování](stream-streaming-policy-concept.md) a přidružit ji k [lokátoru streamování](stream-streaming-locators-concept.md). [Zásadu pro klíč obsahu](/rest/api/media/contentkeypolicies) vytvoříte pro konfiguraci způsobu doručení klíče obsahu (který poskytuje zabezpečený přístup k vašim [prostředkům](assets-concept.md)) koncovým klientům. Je nutné nastavit požadavky (omezení) na zásady klíče obsahu, které musí být splněny, aby bylo možné klíče se zadanou konfigurací doručovat klientům. Zásady klíče obsahu není potřeba pro vymazání streamování nebo stahování. 

Obvykle přidružíte zásady klíčů obsahu k [lokátoru streamování](stream-streaming-locators-concept.md). Případně můžete určit zásady klíče obsahu v rámci [zásad streamování](stream-streaming-policy-concept.md) (při vytváření vlastních zásad streamování pro pokročilé scénáře). 

## <a name="best-practices-and-considerations"></a>Osvědčené postupy a požadavky

> [!IMPORTANT]
> Přečtěte si následující doporučení.

* Měli byste navrhnout omezené sady zásad pro svůj účet Media Service a znovu je použít pro Lokátory streamování, pokud jsou potřeba stejné možnosti. Další informace najdete v tématu [kvóty a omezení](limits-quotas-constraints-reference.md).
* Zásady klíčů obsahu je možné aktualizovat. Aktualizace a výběr aktualizované zásady může trvat až 15 minut, než se mezipaměť pro doručování klíčů aktualizuje. 

   Když tuto zásadu aktualizujete, přepíšete stávající mezipaměť CDN, která by mohla způsobit problémy s přehráváním pro zákazníky, kteří používají obsah uložený v mezipaměti.  
* Doporučujeme, abyste pro každý Asset nevytvořili nové zásady klíče obsahu. Hlavními výhodami sdílení stejných zásad pro klíč obsahu mezi prostředky, které potřebují stejné možnosti zásad, jsou:
   
   * Správa malého počtu zásad je snazší.
   * Pokud potřebujete provést aktualizace zásad klíče obsahu, změny se projeví u všech nových požadavků na licence téměř hned.
* Pokud potřebujete vytvořit novou zásadu, budete muset vytvořit nový Lokátor streamování pro daný prostředek.
* Doporučuje se, aby Media Services vygenerovat klíč obsahu. 

   Obvykle byste používali dlouhotrvající klíč a kontrolovali existenci zásad klíče obsahu pomocí [Get](/rest/api/media/contentkeypolicies/get). Chcete-li získat klíč, je nutné zavolat samostatnou metodu akce pro získání tajných kódů nebo přihlašovacích údajů, viz následující příklad.

## <a name="example"></a>Příklad

K získání klíče použijte `GetPolicyPropertiesWithSecretsAsync` , jak je znázorněno v příkladu [získání podpisového klíče z existujícího zásad](drm-get-content-key-policy-dotnet-how-to.md#get-contentkeypolicy-with-secrets) .

## <a name="filtering-ordering-paging"></a>Filtrování, řazení, stránkování

Viz téma [filtrování, řazení, stránkování Media Services entit](filter-order-page-entitites-how-to.md).

## <a name="additional-notes"></a>Další poznámky

* Vlastnosti zásad klíče obsahu, které jsou `Datetime` typu, jsou vždy ve formátu UTC.
* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="next-steps"></a>Další kroky

* [Použití dynamického šifrování AES-128 a služby doručování klíčů](drm-playready-license-template-concept.md)
* [Použití ochrany DRM s dynamickým šifrováním a služby doručování licencí](drm-protect-with-drm-tutorial.md)
* [Základní kódování klíče standardu AES a ukázkový kód streamování](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)
