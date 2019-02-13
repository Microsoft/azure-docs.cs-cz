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
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f4a04f1598b3ab0efd9ff95a707d3837bb37503
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56196021"
---
# <a name="whats-new-for-authentication"></a>Co je nového v ověřování? 

>Dostanete oznámení o aktualizacích na tuto stránku. Stačí přidat elementy [tuto adresu URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) na svém informačním kanálu RSS kanálů.

Ověřování systému mění a přidává funkce pro zlepšení zabezpečení a dodržování standardů průběžně. Udržovat si aktuální pomocí nejnovější vývoj, tento článek poskytuje informace o následující podrobnosti:

- Nejnovější funkce
- Známé problémy
- Protokol změn
- Zastaralé funkce

> [!TIP] 
> Tato stránka se pravidelně aktualizuje, takže časté návštěvy tohoto webu. Pokud není uvedeno jinak, tyto změny jsou vloženy pouze na místě pro nově registrovaná aplikace.  

## <a name="upcoming-changes"></a>Připravované změny

Nenaplánováno v tuto chvíli. 

## <a name="october-2018"></a>Říjen 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>Už nebude možné znovu použít autorizační kódy

**Datum účinnosti**: 15. listopadu 2018

**Koncové body, které jsou ovlivněné**: V1.0 a v2.0

**Protokol vliv**: [Tok kódu](v2-oauth2-auth-code-flow.md)

Od 15. listopadu 2018, Azure AD, přestane přijímat předchozích ověřovací kódy pro aplikace. Tato změna zabezpečení pomáhá vám Azure AD podle specifikace OAuth a vynutí u koncových bodů v1 i v2.

Pokud vaše aplikace opakovaně používá autorizační kódy k získání tokenů pro několik prostředků, doporučujeme použít kód pro získání tokenu obnovení a pak pomocí tohoto tokenu obnovení získat další tokeny pro jiné prostředky. Autorizační kódy lze použít pouze jednou, ale obnovovací tokeny lze použít více než jednou v několika prostředcích. Všechny nové aplikace, která se pokusí znovu použít ověřovacího kódu během toku OAuth kódu se zobrazí chyba invalid_grant.

Další informace o tokeny obnovení najdete v tématu [aktualizaci přístupových tokenů](v1-protocols-oauth-code.md#refreshing-the-access-tokens).  Pokud používáte knihovnu ADAL nebo MSAL, to se udělá za vás knihovnou – nahraďte druhou instanci 'AcquireTokenByAuthorizationCodeAsync"s"AcquireTokenSilentAsync". 

## <a name="may-2018"></a>Květen 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Tokeny typu ID nejde použít pro tok OBO

**Datum**: 1. května 2018

**Koncové body, které jsou ovlivněné**: V1.0 a v2.0

**Protokoly vliv**: Implicitní tok a [OBO toku](v1-oauth2-on-behalf-of-flow.md)

Od 1. května 2018 id_tokens nelze jako kontrolní výraz ve OBO toku pro nové aplikace. Místo toho by měla sloužit přístupové tokeny, které zabezpečená rozhraní API, dokonce i mezi klientem a stejné aplikace střední vrstvy. Aplikace registrované před 1. květnem 2018 bude i nadále fungovat a mít k výměně pro přístupový token; id_tokens Tento model se nepovažuje za osvědčený postup.

Chcete-li vyřešit tuto změnu, vám pomůžou následující:

1. Vytvoření webového rozhraní API pro vaši aplikaci s jeden nebo víc oborů. Tento explicitní vstupní bod vám umožní přesnější možnosti řízení a zabezpečení.
1. V manifestu aplikace v [webu Azure portal](https://portal.azure.com) nebo [portál pro registraci aplikace](https://apps.dev.microsoft.com), ujistěte se, že aplikace může vystavovat přístupové tokeny pomocí implicitního toku. To je řízen pomocí `oauth2AllowImplicitFlow` klíč.
1. Když klientské aplikace požádá o tokentu id_token prostřednictvím `response_type=id_token`, také požádat o přístupový token (`response_type=token`) pro webové rozhraní API vytvořili výše. Proto při použití koncového bodu v2.0 `scope` parametr by měl vypadat podobně jako `api://GUID/SCOPE`. Na koncovém bodu v1.0 `resource` parametr by měl být identifikátor URI webového rozhraní API aplikace.
1. Střední vrstva místo požadavku id_token předejte Tento přístupový token.  
