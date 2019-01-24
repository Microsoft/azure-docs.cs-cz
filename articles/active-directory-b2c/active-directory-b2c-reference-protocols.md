---
title: Protokoly pro ověřování v Azure Active Directory B2C | Dokumentace Microsoftu
description: Jak vytvářet aplikace přímo s použitím protokolů, které podporuje Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: de7c40bcf93eae357ad99613caa2274f9e77d884
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54853064"
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: Protokoly pro ověřování
Azure Active Directory B2C (Azure AD B2C) poskytuje identitu jako služba pro vaše aplikace díky podpoře dvou standardních oborových protokolů: OpenID Connect a OAuth 2.0. Služba je kompatibilní se standardy, ale žádné dvě implementace těchto protokolů můžete jemně lišit. 

Informace v této příručce je užitečný, pokud při psaní kódu tak, že přímo odesílání a zpracování žádostí HTTP, nikoli pomocí open source knihovna. Doporučujeme, abyste si přečetli tuto stránku předtím, než můžete přejít k podrobnostem jednotlivých určitý protokol. Ale pokud jste již obeznámeni s Azure AD B2C, můžete přejít přímo na [referenční příručky protokol](#protocols).

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Základy
Každá aplikace, kterou používá Azure AD B2C musí být zaregistrované ve svém adresáři B2C v [webu Azure portal](https://portal.azure.com). Proces registrace aplikace shromáždí a přiřadí vaší aplikaci několik hodnot:

* **ID aplikace**, které jednoznačně identifikuje vaši aplikaci.
* A **identifikátor URI pro přesměrování** nebo **balíček identifikátor** , který lze použít k cílení odpovědí zpět do vaší aplikace.
* Pár dalších hodnot specifické pro scénář. Další informace, přečtěte si [postup při registraci vaší aplikace](active-directory-b2c-app-registration.md).

Po registraci aplikace komunikuje se službou Azure Active Directory (Azure AD) pomocí zasílání požadavků na koncový bod:

```
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token
```

Téměř všechny toky OAuth a OpenID Connect čtyři strany podílejí na exchange:

![Role OAuth 2.0](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

* **Autorizační server** je koncový bod Azure AD. Zpracovává bezpečně nic související s informací o uživateli a přístup. Zpracovává také vztahy důvěryhodnosti mezi stranami v toku. Zodpovídá za ověření identity uživatele, poskytování a odvolání přístupu k prostředkům a vydávání tokenů. Je také označován jako zprostředkovatele identity.

* **Vlastníka prostředku** je obvykle koncového uživatele. Je stranu, která vlastní data a má možnost povolit třetími stranami pro přístup k této dat nebo prostředek.

* **Klienta OAuth** používá vaše aplikace. Je identifikován jeho ID aplikace. Je to obvykle stran, se koncovým uživatelům pracovat. Požadavky také tokeny od autorizačního serveru. Vlastník prostředku musí udělit svolení pro přístup k prostředku.

* **Server prostředků** je, ve kterém se nachází zdroj nebo data. Důvěřuje autorizační server pro zabezpečené ověřování a autorizaci klienta OAuth. Využívá také přístupových tokenů nosiče k zajištění, že lze udělit přístup k prostředku.

## <a name="policies-and-user-flows"></a>Zásady a uživatel toků
Nejdůležitější funkce služby jsou pravděpodobně, zásady Azure AD B2C. Úvod do zásad Azure AD B2C je rozšířením standardní protokoly OAuth 2.0 a OpenID Connect. Tyto rutiny umožňují Azure AD B2C k provedení mnohem více než jednoduché ověřování a autorizace. 

Můžete nastavit zvládnout běžné úkoly identity, na portálu Azure AD B2C zahrnuje předdefinované, Konfigurovatelné zásady volá **toky uživatelů**. Toky uživatelů plně popisují činnosti identity uživatelů, včetně registrace, přihlášení a úpravy profilu. Toky uživatelů lze definovat v správu uživatelského rozhraní. Mohou být provedeny s použitím parametru speciální dotazu v žádosti o ověření protokolu HTTP. 

Zásady a toky uživatelů nejsou standardních funkcí OAuth 2.0 a OpenID Connect, proto byste měli podniknout čas, abyste je líp pochopili. Další informace najdete v tématu [Azure AD B2C uživatele tok referenční příručka](active-directory-b2c-reference-policies.md).

## <a name="tokens"></a>Tokeny
Azure AD B2C provádění OAuth 2.0 a OpenID Connect díky rozsáhlé používání šířky nosné tokeny, včetně nosné tokeny, které jsou reprezentovány ve formě webové tokeny JSON (Jwt). Nosný token je token zjednodušené zabezpečení, která uděluje "nosiče" přístup k chráněnému prostředku.

Nositele je každá strana, která může představovat token. Azure AD musí nejdřív ověřit strana předtím, než může přijímat nosný token. Ale pokud nejsou požadované kroky k zabezpečení token v přenos a ukládání, můžete zachytit a používá nežádoucí osobou.

Některé tokeny zabezpečení mají vestavěné mechanismy, které brání neoprávněnému strany jejich používání, ale tento mechanismus není nutné nosné tokeny. Musí být přenášet v zabezpečeného kanálu, například zabezpečení transportní vrstvy (HTTPS). 

Pokud nosný token je přenesen mimo zabezpečený kanál, můžete použít škodlivý stran útok man-in-the-middle k získání tokenu a použije ho k získání neoprávněného přístupu k chráněnému prostředku. Stejné zásady zabezpečení platí při nosné tokeny jsou uložená nebo ukládání do mezipaměti pro pozdější použití. Vždy zajistěte, aby vaše aplikace odesílá a ukládá nosné tokeny bezpečným způsobem.

Důležité informace o další nosný token zabezpečení, najdete v části [5 část dokumentu RFC 6750](https://tools.ietf.org/html/rfc6750).

Další informace o různých typech tokenů, které se používají v Azure AD B2C jsou k dispozici v [odkaz tokenu Azure AD](active-directory-b2c-reference-tokens.md).

## <a name="protocols"></a>Protokoly
Až budete připraveni ke kontrole některé příklad požadavky, můžete začít s jednou z následujících kurzů. Každý odpovídá konkrétní ověřovacím scénáři. Pokud potřebujete pomoc při rozhodování, která tok je pro vás ta pravá, prohlédněte si [typy aplikací můžete vytvořit pomocí Azure AD B2C](active-directory-b2c-apps.md).

* [Vytvářet mobilní a nativní aplikace s použitím OAuth 2.0](active-directory-b2c-reference-oauth-code.md)
* [Tvořte webové aplikace s použitím OpenID Connect](active-directory-b2c-reference-oidc.md)
* [Sestavení jednostránkové aplikace pomocí implicitního toku OAuth 2.0](active-directory-b2c-reference-spa.md)

