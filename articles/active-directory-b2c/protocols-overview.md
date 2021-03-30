---
title: Ověřovací protokoly v Azure Active Directory B2C | Microsoft Docs
description: Jak vytvářet aplikace přímo pomocí protokolů, které jsou podporovány nástrojem Azure Active Directory B2C.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "78183901"
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: ověřovací protokoly
Azure Active Directory B2C (Azure AD B2C) poskytuje pro vaše aplikace identitu jako službu, která podporuje dva oborové standardní protokoly: OpenID Connect a OAuth 2,0. Služba je kompatibilní se standardy, ale jakékoli dvě implementace těchto protokolů můžou mít drobné rozdíly.

Informace v této příručce jsou užitečné, pokud píšete kód přímým odesíláním a zpracováním požadavků HTTP namísto použití open source knihovny. Doporučujeme, abyste si přečetli tuto stránku před tím, než se podrobně do podrobností o jednotlivých specifických protokolech. Pokud jste už ale obeznámeni s Azure AD B2C, můžete přejít přímo na [referenční příručky k protokolu](#protocols).

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Základy
Každá aplikace, která používá Azure AD B2C musí být zaregistrovaná v adresáři B2C v [Azure Portal](https://portal.azure.com). Proces registrace aplikace shromáždí a přiřadí vaší aplikaci několik hodnot:

* **ID aplikace**, které jednoznačně identifikuje vaši aplikaci.
* Identifikátor **URI přesměrování** nebo **identifikátor balíčku** , který se dá použít k přímému směrování odpovědí zpět do vaší aplikace.
* Několik dalších hodnot specifických pro konkrétní scénář. Další informace najdete v tématu [Registrace aplikace](tutorial-register-applications.md).

Po registraci aplikace komunikuje s Azure Active Directory (Azure AD) odesláním požadavků do koncového bodu:

```
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token
```

Téměř všechny toky OAuth a OpenID Connect jsou zapojené do systému Exchange:

![Diagram znázorňující čtyři role OAuth 2,0](./media/protocols-overview/protocols_roles.png)

* **Autorizační Server** je koncový bod Azure AD. Zabezpečeně zpracovává všechny informace o uživatelích a přístup k nim. Také zpracovává vztahy důvěryhodnosti mezi stranami v toku. Zodpovídá za ověřování identity uživatele, udělování a odvolávání přístupu k prostředkům a vydávání tokenů. Označuje se také jako zprostředkovatel identity.

* **Vlastníkem prostředku** je obvykle koncový uživatel. Jedná se o stranu, která vlastní data a má oprávnění k tomu, aby třetí strany mohly získat přístup k těmto datům nebo prostředkům.

* **Klient OAuth** je vaše aplikace. Je identifikován jeho ID aplikace. Obvykle se jedná o stranu, se kterou koncoví uživatelé pracují. Také žádá o tokeny od autorizačního serveru. Vlastník prostředku musí udělit oprávnění klienta k přístupu k prostředku.

* **Server prostředků** je místo, kde se nachází prostředek nebo data. Důvěřuje autorizačnímu serveru za účelem bezpečného ověřování a autorizace klienta OAuth. Používá taky přístupové tokeny nosiče k zajištění toho, aby bylo možné udělit přístup k prostředku.

## <a name="policies-and-user-flows"></a>Zásady a toky uživatelů
Pravděpodobně jsou nejdůležitější funkce služby, které jsou v zásadách Azure AD B2C. Azure AD B2C rozšiřuje standardní protokoly OAuth 2,0 a OpenID Connect tím, že zavádí zásady. To umožňuje Azure AD B2C provádět mnohem více než jednoduché ověřování a autorizaci.

Aby vám pomohly nastavit nejběžnější úkoly identity, Azure AD B2C portál obsahuje předdefinované a konfigurovatelné zásady nazývané **uživatelské toky**. Tok uživatelů plně popisuje prostředí identity spotřebitelů, včetně registrace, přihlašování a úprav profilů. Toky uživatelů lze definovat v uživatelském rozhraní pro správu. Můžou se spouštět pomocí speciálního parametru dotazu v požadavcích na ověření protokolu HTTP.

Zásady a toky uživatelů nejsou standardními funkcemi OAuth 2,0 a OpenID Connect, takže byste měli čas porozumět. Další informace najdete v tématu [Průvodce odkazem na uživatelský tok Azure AD B2C](user-flow-overview.md).

## <a name="tokens"></a>Tokeny
Azure AD B2C implementace OAuth 2,0 a OpenID Connect využívá rozsáhlé použití nosných tokenů, včetně nosných tokenů, které jsou reprezentovány jako webové tokeny JSON (JWTs). Nosný token je jednoduchý token zabezpečení, který uděluje přístup k chráněnému prostředku "nosičem".

Nosič je kterákoli strana, která může token prezentovat. Aby mohl služba Azure AD získat token nosiče, musí nejdřív ověřit stranu. Pokud ale požadované kroky neprovedete k zabezpečení tokenu v přenosech a úložištích, může být zachycen a používán neúmyslnou stranou.

Některé tokeny zabezpečení mají předdefinované mechanismy, které brání neoprávněným stranám v jejich použití, ale nosné tokeny nemají tento mechanismus. Musí se přenášet na zabezpečený kanál, jako je například protokol HTTPS (Transport Layer Security).

Pokud se nosný token přenáší mimo zabezpečený kanál, může útočník získat token a použít ho k získání neoprávněného přístupu k chráněnému prostředku pomocí útoku prostředníkem. Stejné zásady zabezpečení platí i v případě, že jsou pro pozdější použití uloženy tokeny nosiče nebo ukládání do mezipaměti. Vždycky Ujistěte se, že vaše aplikace zabezpečeně přenáší a ukládá tokeny nosiče.

Další informace o zabezpečení nosných tokenů najdete v [části RFC 6750 část 5](https://tools.ietf.org/html/rfc6750).

Další informace o různých typech tokenů, které se používají v Azure AD B2C, jsou k dispozici v referenčních informacích k [tokenům Azure AD](tokens-overview.md).

## <a name="protocols"></a>Protokoly
Až budete připraveni na kontrolu některých ukázkových požadavků, můžete začít s jedním z následujících kurzů. Každý odpovídá konkrétnímu scénáři ověřování. Pokud potřebujete pomoci určit, který tok je pro vás nejvhodnější, podívejte [se na typy aplikací, které můžete vytvořit pomocí Azure AD B2C](application-types.md).

* [Vytváření mobilních a nativních aplikací pomocí OAuth 2,0](authorization-code-flow.md)
* [Sestavování webových aplikací pomocí OpenID Connect](openid-connect.md)
* [Vytváření jednostránkovéch aplikací pomocí implicitního toku OAuth 2,0](implicit-flow-single-page-application.md)

