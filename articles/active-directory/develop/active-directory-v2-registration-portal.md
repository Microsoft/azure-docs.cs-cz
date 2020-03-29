---
title: Odkaz na registrační portál aplikace | Azure
titleSuffix: Microsoft identity platform
description: Popis funkcí na portálu pro registraci aplikací Microsoftu.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: ryanwi
ms.reviewer: lenalepa
ms.custom: aaddev
ms.openlocfilehash: 26ebee446523c138569b9d5379c9a5e1b9e93e7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76698437"
---
# <a name="app-registration-reference"></a>Reference k registraci aplikací

Tento dokument obsahuje kontext a popisy různých funkcí, které se nacházejí v prostředí [registrace aplikací](https://aka.ms/appregistrations) na webu Azure Portal.

## <a name="my-applications-or-converged-applications"></a>Moje aplikace nebo konvergované aplikace

Tento seznam obsahuje všechny aplikace registrované pro použití s koncovým bodem platformy microsoft identit (v2.0). Tyto aplikace mají možnost přihlašovat uživatele pomocí osobních účtů Microsoft i pracovních a školních účtů z Azure Active Directory. Další informace o koncovém bodu platformy identity najdete [v přehledu v2.0](active-directory-appmodel-v2-overview.md). Tyto aplikace lze také integrovat s koncovým bodem `https://login.live.com`ověřování účtu Microsoft .

## <a name="azure-ad-only-applications"></a>Jenom aplikace Azure AD

Tento seznam obsahuje všechny vaše aplikace registrované pro použití s koncovým bodem Azure AD v1.0. Tyto aplikace mají jenom možnost přihlásit uživatele pomocí pracovních a školních účtů z Azure Active Directory. Tento seznam obsahuje aplikace, které byly zaregistrovány pomocí **prostředí registrace aplikací** na webu Azure [Portal](https://portal.azure.com).

## <a name="live-sdk-applications"></a>Živé aplikace sady SDK

Tento seznam obsahuje všechny vaše aplikace registrované pro použití výhradně s účtem Microsoft. Nejsou povoleny pro použití s Azure Active Directory. Toto je místo, kde najdete všechny aplikace, které `https://account.live.com/developers/applications`byly dříve registrovány na portálu pro vývojáře MSA na adrese . Všechny funkce, které `https://account.live.com/developers/applications` jste dříve provedli, lze nyní provádět v [registracích aplikací](https://aka.ms/appregistrations).

## <a name="application-secrets"></a>Tajné klíče aplikace

Tajné klíče aplikací jsou přihlašovací údaje, které umožňují vaší aplikaci provádět spolehlivé [ověřování klientů](https://tools.ietf.org/html/rfc6749#section-2.3) pomocí služby Azure AD. V OAuth & OpenID Connect se tajný klíč `client_secret`aplikace běžně označuje jako . V protokolu v2.0 musí každá aplikace, která obdrží token zabezpečení ve `https` webovém adresovatelném umístění (pomocí schématu), použít tajný klíč aplikace k identifikaci ve službě Azure AD při uplatnění tohoto tokenu zabezpečení. Kromě toho každý nativní klient, který přijímá tokeny na zařízení bude zakázáno používat tajný klíč aplikace k provádění ověřování klienta. To odrazuje od ukládání tajných kódů v nezabezpečených prostředích.

Každá aplikace může obsahovat dvě platné tajné klíče aplikace v daném okamžiku. Udržováním dvou tajných kódů máte možnost provádět periodický přechod pomocí klíče v celém prostředí aplikace. Po migraci celé aplikace na nový tajný klíč můžete starý tajný klíč odstranit a zřídit nový.

V současné době jsou povoleny pouze dva typy tajných kódů aplikace na portálu pro registraci aplikací. Výběrgenerovat **nové heslo** generuje a ukládá sdílený tajný klíč v příslušném úložišti dat, které můžete použít ve vaší aplikaci. Výběrgenerovat **nový pár klíčů** vytvoří nový pár veřejného a soukromého klíče, který lze stáhnout a použít pro ověřování klientů do Služby Azure AD. Volba **Nahrát veřejný klíč** umožňuje použít vlastní pár veřejného a soukromého klíče.
Musíte nahrát certifikát, který obsahuje veřejný klíč.

## <a name="profile"></a>Profil

Část profilu na portálu pro registraci aplikací lze použít k přizpůsobení přihlašovací stránky pro vaši aplikaci. V tuto chvíli můžete změnit logo aplikace přihlašovací stránky, adresu URL služby a adresu URL prohlášení o zásadách ochrany osobních údajů. Logo musí být průhledný obraz 48 x 48 nebo 50 x 50 pixelů v souboru GIF, PNG nebo JPEG, který je 15 KB nebo menší. Zkuste změnit hodnoty a zobrazit výslednou přihlašovací stránku!

## <a name="live-sdk-support"></a>Podpora pro Live SDK

Když povolíte "Live SDK Support", všechny tajné kódy aplikací, které vytvoříte, budou zřízeny do datových úložišť Azure AD i účtu Microsoft. To umožňuje aplikaci integrovat přímo se službou Účet Microsoft (login.live.com). Pokud chcete vytvořit aplikaci pomocí účtu Microsoft přímo (na rozdíl od použití koncového bodu v2.0), měli byste se ujistit, že je povolena podpora Live SDK.

Zakázání podpory live SDK zajišťuje, že tajný klíč aplikace je pouze zapsána do úložiště dat Azure AD. Úložiště dat Azure AD zahrnuje předpisy na podnikové úrovni, které mu umožňují splňovat určité standardy, jako je dodržování předpisů FISMA. Pokud povolíte podporu live SDK, vaše aplikace nemusí dosáhnout souladu s některými z těchto standardů.

Pokud plánujete používat pouze koncový bod v2.0, můžete bezpečně zakázat podporu sady Live SDK.
