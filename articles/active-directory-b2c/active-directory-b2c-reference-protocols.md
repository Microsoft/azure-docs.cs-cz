---
title: 'Azure Active Directory B2C: Protokoly pro ověřování | Microsoft Docs'
description: Jak vytvářet aplikace přímo pomocí protokolů, které podporuje Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/07/2017
ms.author: davidmu
ms.openlocfilehash: 9159a1ff55ada7f2c3bdcc60a15eae5ddc7e08b2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: Protokoly pro ověřování
Azure Active Directory B2C (Azure AD B2C) poskytuje identitu jako služba pro aplikací díky podpoře dvou standardních protokolů: OpenID Connect a OAuth 2.0. Služba je kompatibilní se standardy, ale žádné dvě implementace těchto protokolů můžete mít jemně lišit. 

Informace v této příručce je užitečné, pokud napíšete kód přímo odeslání a zpracování žádostí HTTP, nikoli pomocí knihovny otevřeným zdrojem. Doporučujeme, abyste si přečetli tuto stránku před podrobně podrobnosti o jednotlivých určitý protokol. Ale pokud jste již obeznámeni s Azure AD B2C, můžete přejít rovnou na [referenční příručky protokol](#protocols).

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Základy
Každá aplikace používající Azure AD B2C musí být zaregistrovaný ve svém adresáři B2C v [portál Azure](https://portal.azure.com). Proces registrace aplikace shromáždí a přiřadí vaší aplikaci několik hodnot:

* **ID aplikace**, které jednoznačně identifikuje vaši aplikaci.
* A **identifikátor URI pro přesměrování** nebo **balíček identifikátor** který lze použít k cílení odpovědí zpět do vaší aplikace.
* Pár dalších hodnot konkrétní scénáře. Další informace najdete další [postup registrace vaší aplikace](active-directory-b2c-app-registration.md).

Po registraci aplikace komunikuje se službou Azure Active Directory (Azure AD) pomocí zasílání požadavků do koncového bodu:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Téměř všechny toky OAuth a OpenID Connect čtyři strany podílejí na exchange:

![Role OAuth 2.0](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

* **Serveru ověřování** je koncový bod Azure AD. Bezpečně zpracovává nic související s informace o uživateli a přístup. Také obstará vztahy důvěryhodnosti mezi stranami v toku. Zodpovídá za ověření identity uživatele, udělení a odvolání přístupu k prostředkům a vystavování tokenů. Je také označované jako zprostředkovatele identity.

* **Vlastník prostředku** je obvykle koncového uživatele. Je stranu, která je vlastníkem dat a má možnost povolit třetím stranám umožnit přístup k dané dat nebo prostředků.

* **Klienta OAuth** je vaše aplikace. Je identifikována jeho ID aplikace. Je obvykle stranu, která koncovým uživatelům interakci s. Také vyžádá tokeny ze serveru ověřování. Vlastník prostředku musí udělit oprávnění k klienta pro přístup k prostředku.

* **Server prostředků** je, kde se nachází prostředků nebo data. Důvěřovat serveru ověřování k bezpečně ověřování a autorizaci klienta OAuth. Používá také přístupových tokenů nosiče k zajištění, že lze udělit přístup k prostředku.

## <a name="policies"></a>Zásady
Pravděpodobně Azure AD B2C zásady jsou nejdůležitější funkce služby. Azure AD B2C rozšiřuje standardní protokoly OAuth 2.0 a OpenID Connect zavedením zásad. Tyto rutiny umožňují Azure AD B2C provést mnohem víc než jednoduché ověřování a autorizace. 

Zásady plně popisují činnosti identity uživatelů, včetně registrace, přihlášení a úpravy profilu. Zásady můžete být definován v správu uživatelského rozhraní. Mohou být provedeny s použitím parametru speciální dotazu v žádosti o ověření protokolu HTTP. 

Zásady nejsou standardní funkce OAuth 2.0 a OpenID Connect, takže byste měli vzít je doba, která je pochopit. Další informace najdete v tématu [Azure AD B2C zásad referenční příručka](active-directory-b2c-reference-policies.md).

## <a name="tokens"></a>Tokeny
Azure AD B2C provádění OAuth 2.0 a OpenID Connect díky rozsáhlé používání tokenů nosiče, včetně nosné tokeny, které jsou reprezentovány jako webové tokeny JSON (Jwt). Je token nosiče tokenu lightweight zabezpečení, který uděluje přístup "nosiče" k chráněnému prostředku.

Nosiče je jakékoli strany, který může být token. Azure AD musí je nejdřív ověřit a stranou předtím, než mohl přijímat token nosiče. Ale požadované kroky nebudou přijata zabezpečit token v přenosu a úložiště, můžete zachytit a použít nezamýšleným strana.

Některé tokeny zabezpečení mají integrovaný mechanismus, které brání neoprávněným stranám jejich používání, ale nosné tokeny nemají tento mechanismus. Musí být přenosu v zabezpečený kanál, jako je například transportní vrstvy zabezpečení (HTTPS). 

Pokud token nosiče je přenesen mimo zabezpečený kanál, škodlivý strany slouží útok man-in-the-middle k získání tokenu a použít ho k získání neoprávněného přístupu k chráněnému prostředku. Stejné zásady zabezpečení platí, když jsou nosné tokeny uložené nebo ukládání do mezipaměti pro pozdější použití. Vždy zajistěte, aby vaše aplikace odesílá a ukládá nosné tokeny zabezpečeným způsobem.

Důležité informace o dalších nosiče tokenu zabezpečení, najdete v části [RFC 6750 část 5](http://tools.ietf.org/html/rfc6750).

Další informace o různých typech tokenů, které se používají v Azure AD B2C jsou k dispozici v [odkaz tokenu Azure AD](active-directory-b2c-reference-tokens.md).

## <a name="protocols"></a>Protokoly
Až budete připraveni ke kontrole některých požadavků příklad, můžete spustit některý z následujících kurzů. Každý scénář konkrétní ověřování odpovídá. Pokud potřebujete pomoc při rozhodování, které tok je pro vás správný, podívejte se na [s typy aplikací můžete vytvořit pomocí Azure AD B2C](active-directory-b2c-apps.md).

* [Vytvářet aplikace, mobilní a nativní pomocí standardu OAuth 2.0](active-directory-b2c-reference-oauth-code.md)
* [Vytvoření webové aplikace pomocí OpenID Connect](active-directory-b2c-reference-oidc.md)
* [Vytvářet jednostránkové aplikace pomocí implicitního toku OAuth 2.0](active-directory-b2c-reference-spa.md)

