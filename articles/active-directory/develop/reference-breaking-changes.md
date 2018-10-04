---
title: Azure Active Directory rozbíjející změny v odkazu | Dokumentace Microsoftu
description: Další informace o změnách provedených protokoly služby Azure AD, které může mít vliv na vaše aplikace.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 266d9ff9ceb4aa71429a9afc3056d23d222a9c7b
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48247367"
---
# <a name="whats-new-for-authentication"></a>Co je nového v ověřování? 

>Dostanete oznámení o aktualizacích na tuto stránku. Stačí přidat elementy [tuto adresu URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) na svém informačním kanálu RSS kanálů.

Ověřování systému mění a přidává funkce pro zlepšení zabezpečení a dodržování standardů průběžně. Abyste mohli používat aktuální pomocí nejnovější vývoj, tento článek vám poskytne následující informace:

- Nejnovější funkce
- Známé problémy
- Protokol změn
- Zastaralé funkce

> [!TIP] 
> Tato stránka se pravidelně aktualizuje, takže časté návštěvy tohoto webu. Pokud není uvedeno jinak, tyto změny jsou vloženy pouze na místě pro nově registrovaná aplikace.  

## <a name="upcoming-changes"></a>Připravované změny

### <a name="authorization-codes-can-no-longer-be-reused"></a>Už nebude možné znovu použít autorizační kódy

**Datum účinnosti**: 10. října 2018 **koncové body, které jsou ovlivněné**: jak v1.0 a v2.0 **protokolu vliv**: [Code flow](v2-oauth2-auth-code-flow.md)

Od 10. října 2018, Azure AD, přestane přijímat dříve používali ověřovací kódy pro aplikace. Tato změna zabezpečení pomáhá vám Azure AD podle specifikace OAuth a vynutí u koncových bodů v1 i v2.

Pokud vaše aplikace opakovaně používá autorizační kódy k získání tokenů pro několik prostředků, doporučujeme použít kód pro získání tokenu obnovení a pak pomocí tohoto tokenu obnovení získat další tokeny pro jiné prostředky. Autorizační kódy lze použít pouze jednou, ale obnovovací tokeny lze použít více než jednou v několika prostředcích. Všechny nové aplikace, která se pokusí znovu použít ověřovacího kódu během toku OAuth kódu se zobrazí chyba invalid_grant.

Další informace o tokeny obnovení najdete v tématu [aktualizaci přístupových tokenů](v1-protocols-oauth-code.md#refreshing-the-access-tokens).

> [!NOTE]
> Ve snaze o přerušení co nejméně aplikace jako je to možné, existující aplikace, které jsou závislé na této funkci byl přiřazen výjimku pro tento požadavek.  Všechny aplikace s více než 10 přihlášení spoléhat na to považovaná za den spoléhání se na tento model.  

## <a name="may-2018"></a>Květen 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Tokeny typu ID nejde použít pro tok OBO

**Datum**: 1. květnem 2018 **koncové body, které jsou ovlivněné**: jak v1.0 a v2.0 **protokoly vliv**: implicitní tok a [OBO toku](v1-oauth2-on-behalf-of-flow.md)

Od 1. května 2018 id_tokens nelze jako kontrolní výraz ve OBO toku pro nové aplikace. Místo toho by měla sloužit přístupové tokeny, které zabezpečená rozhraní API, dokonce i mezi klientem a stejné aplikace střední vrstvy. Aplikace registrované před 1. květnem 2018 bude i nadále fungovat a mít k výměně pro přístupový token; id_tokens ale to se nepovažuje za osvědčený postup.

Chcete-li vyřešit tuto změnu, vám pomůžou následující:

1. Vytvoření webového rozhraní API pro aplikace střední vrstvy pomocí jednoho nebo víc oborů. To umožní přesnější možnosti řízení a zabezpečení.
1. V manifestu aplikace v [webu Azure portal](https://portal.azure.com) nebo [portál pro registraci aplikace](https://apps.dev.microsoft.com), ujistěte se, že aplikace může vystavovat přístupové tokeny pomocí implicitního toku. To je řízen pomocí `oauth2AllowImplicitFlow` klíč.
1. Když klientské aplikace požádá o tokentu id_token prostřednictvím `response_type=id_token`, také požádat o přístupový token (`response_type=token`) pro webové rozhraní API vytvořili výše. Proto při použití koncového bodu v2.0 `scope` parametr by měl vypadat podobně jako `api://GUID/SCOPE`. Na koncovém bodu v1.0 `resource` parametr by měl být identifikátor URI webového rozhraní API aplikace.
1. Střední vrstva místo požadavku id_token předejte Tento přístupový token.  
