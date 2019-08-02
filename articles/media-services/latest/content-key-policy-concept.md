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
ms.openlocfilehash: 8809bf25c3bcfb26fb0ad251a2b09dfdca2a3e04
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679196"
---
# <a name="content-key-policies"></a>Zásady symetrických klíčů

Pomocí Media Services můžete doručovat živý obsah na vyžádání a dynamicky šifrovaný pomocí standard AES (Advanced Encryption Standard) (AES-128) nebo kteréhokoli ze tří hlavních systémů DRM (Správa digitálních práv): Microsoft PlayReady, Google Widevine a Apple FairPlay. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM (PlayReady, Widevine a FairPlay) licence autorizovaným klientům. 

Pokud chcete pro svůj Stream zadat možnosti šifrování, musíte vytvořit [zásadu streamování](streaming-policy-concept.md) a přidružit ji k [lokátoru streamování](streaming-locators-concept.md). [Zásadu pro klíč obsahu](https://docs.microsoft.com/rest/api/media/contentkeypolicies) vytvoříte pro konfiguraci způsobu doručení klíče obsahu (který poskytuje zabezpečený přístup k vašim [prostředkům](assets-concept.md)) koncovým klientům. Je nutné nastavit požadavky (omezení) na zásady klíče obsahu, které musí být splněny, aby bylo možné klíče se zadanou konfigurací doručovat klientům. Zásady klíče obsahu není potřeba pro vymazání streamování nebo stahování. 

Obvykle přidružíte zásady klíčů obsahu k [lokátoru streamování](streaming-locators-concept.md). Případně můžete určit zásady klíče obsahu v rámci [zásad streamování](streaming-policy-concept.md) (při vytváření vlastních zásad streamování pro pokročilé scénáře). 

> [!NOTE]
> Vlastnosti zásad klíče obsahu, které jsou `Datetime` typu, jsou vždy ve formátu UTC.

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

## <a name="example"></a>Příklad

K získání klíče použijte `GetPolicyPropertiesWithSecretsAsync`, jak je znázorněno v příkladu [získání podpisového klíče z existujícího zásad](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets) .

## <a name="filtering-ordering-paging"></a>Filtrování, řazení, stránkování

Viz téma [filtrování, řazení, stránkování Media Services entit](entities-overview.md).

## <a name="next-steps"></a>Další postup

* [Použití dynamického šifrování AES-128 a doručení klíče služby](protect-with-aes128.md)
* [Pomocí DRM dynamického šifrování a licence služby pro doručování](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
