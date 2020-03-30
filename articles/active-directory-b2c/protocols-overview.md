---
title: Ověřovací protokoly ve službě Azure Active Directory B2C | Dokumenty společnosti Microsoft
description: Jak vytvářet aplikace přímo pomocí protokolů, které jsou podporované službou Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ed393f721d4461ebadea41f8dad707d4881865cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183901"
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: Ověřovací protokoly
Azure Active Directory B2C (Azure AD B2C) poskytuje identitu jako službu pro vaše aplikace podporou dvou standardních protokolů: OpenID Connect a OAuth 2.0. Služba je kompatibilní se standardy, ale všechny dvě implementace těchto protokolů mohou mít jemné rozdíly.

Informace v této příručce jsou užitečné, pokud píšete kód přímým odesíláním a zpracováním požadavků HTTP, nikoli pomocí knihovny s otevřeným zdrojovým kódem. Doporučujeme, abyste si přečetli tuto stránku před ponořením do podrobností o jednotlivých konkrétních protokolech. Ale pokud jste již obeznámeni s Azure AD B2C, můžete přejít přímo na [průvodce odkazna protokol](#protocols).

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Základy
Každá aplikace, která používá Azure AD B2C, musí být zaregistrovaná ve vašem adresáři B2C na [webu Azure Portal](https://portal.azure.com). Proces registrace aplikace shromáždí a přiřadí vaší aplikaci několik hodnot:

* **ID aplikace**, které jednoznačně identifikuje vaši aplikaci.
* Identifikátor **URI přesměrování** nebo identifikátor **balíčku,** který lze použít k přímé odpovědi zpět do vaší aplikace.
* Několik dalších hodnot specifických pro scénář. Další informace naleznete v informacích o [registraci přihlášky](tutorial-register-applications.md).

Po registraci aplikace komunikuje s Azure Active Directory (Azure AD) odesláním požadavků do koncového bodu:

```
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token
```

Téměř ve všech tocích OAuth a OpenID Connect jsou do výměny zapojeny čtyři strany:

![Diagram znázorňující čtyři role OAuth 2.0](./media/protocols-overview/protocols_roles.png)

* **Autorizační server** je koncový bod Azure AD. Bezpečně zpracovává vše, co souvisí s informacemi o uživateli a přístupem. Také zpracovává vztahy důvěryhodnosti mezi stranami v toku. Je zodpovědný za ověření identity uživatele, udělení a zrušení přístupu k prostředkům a vydávání tokenů. Je také označován jako zprostředkovatel identity.

* Vlastník **prostředku** je obvykle koncový uživatel. Je to strana, která vlastní data a má pravomoc umožnit třetím stranám přístup k těmto datům nebo prostředkům.

* **Klient OAuth** je vaše aplikace. Je identifikován a jeho ID aplikace. Je to obvykle strana, se kterou koncoví uživatelé komunikují. Také požaduje tokeny z autorizačního serveru. Vlastník prostředku musí udělit klientovi oprávnění pro přístup k prostředku.

* **Server prostředků** je místo, kde se nachází prostředek nebo data. Důvěřuje autorizačnímu serveru k bezpečnému ověření a autorizaci klienta OAuth. Používá také tokeny přístupu nosiče k zajištění, že přístup k prostředku může být udělena.

## <a name="policies-and-user-flows"></a>Zásady a toky uživatelů
Zásady Azure AD B2C jsou pravděpodobně nejdůležitější funkce služby. Azure AD B2C rozšiřuje standardní protokoly OAuth 2.0 a OpenID Connect zavedením zásad. Ty umožňují Azure AD B2C provádět mnohem více než jednoduché ověřování a autorizace.

Portál Azure AD B2C obsahuje předdefinované konfigurovatelné zásady nazývané **toky uživatelů,** které vám pomohou nastavit nejběžnější úlohy identity. Toky uživatelů plně popisují prostředí identity příjemce, včetně registrace, přihlášení a úprav profilu. Toky uživatelů lze definovat v uživatelském rozhraní pro správu. Lze je spustit pomocí speciálního parametru dotazu v požadavcích na ověřování HTTP.

Zásady a toky uživatelů nejsou standardní funkce OAuth 2.0 a OpenID Connect, takže byste měli mít čas na jejich pochopení. Další informace naleznete v [příručce reference uživatelského toku Azure AD B2C](user-flow-overview.md).

## <a name="tokens"></a>Tokeny
Implementace Azure AD B2C OAuth 2.0 a OpenID Connect umožňuje rozsáhlé použití nosné tokeny, včetně nosné tokeny, které jsou reprezentovány jako webové tokeny JSON (JWTs). Nosný token je lehký token zabezpečení, který uděluje "nosič" přístup k chráněnému prostředku.

Nosič je každá strana, která může předložit token. Azure AD musí nejprve ověřit stranu před tím, než může získat token nosiče. Pokud však nejsou podniknuty požadované kroky k zabezpečení tokenu v přenosu a úložišti, může být zachycen a použit nechtěnou stranou.

Některé tokeny zabezpečení mají integrované mechanismy, které brání neoprávněným stranám v jejich použití, ale tokeny nosiče nemají tento mechanismus. Musí být přepravovány v zabezpečeném kanálu, jako je například zabezpečení transportní vrstvy (HTTPS).

Pokud je nosný token přenášen mimo zabezpečený kanál, může škodlivá strana použít útok prostředníkem k získání tokenu a jeho použití k získání neoprávněného přístupu k chráněnému prostředku. Stejné zásady zabezpečení platí při nosné tokeny jsou uloženy nebo uloženy do mezipaměti pro pozdější použití. Vždy se ujistěte, že vaše aplikace přenáší a ukládá žetony na doručitele bezpečným způsobem.

Další důležité informace o zabezpečení nože naleznete v části [5 rfc 6750](https://tools.ietf.org/html/rfc6750).

Další informace o různých typech tokenů, které se používají v Azure AD B2C jsou k dispozici v [odkazu tokenu Azure AD](tokens-overview.md).

## <a name="protocols"></a>Protokoly
Až budete připraveni zkontrolovat některé ukázkové požadavky, můžete začít s jedním z následujících kurzů. Každý odpovídá konkrétní scénář ověřování. Pokud potřebujete pomoc s určením, který tok je pro vás ten pravý, podívejte se [na typy aplikací, které můžete vytvářet pomocí Azure AD B2C](application-types.md).

* [Vytváření mobilních a nativních aplikací pomocí OAuth 2.0](authorization-code-flow.md)
* [Vytváření webových aplikací pomocí OpenID Connect](openid-connect.md)
* [Vytváření jednostránkových aplikací pomocí implicitního toku OAuth 2.0](implicit-flow-single-page-application.md)

