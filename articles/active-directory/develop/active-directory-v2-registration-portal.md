---
title: Témata nápovědy portálu registrace aplikace | Dokumentace Microsoftu
description: Popis různých funkcí v portálu pro registraci aplikace Microsoftu.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: celested
ms.reviewer: lenalepa
ms.custom: aaddev
ms.openlocfilehash: 0a9ec2e84197729f98fbf90bac53b505a8c99a19
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52427324"
---
# <a name="app-registration-reference"></a>Reference k registraci aplikací
Tento dokument poskytuje kontext a popisy různých funkcí najdete v [portál pro registraci aplikací](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/).

## <a name="my-applications-or-converged-applications"></a>Moje aplikace nebo Konvergované aplikace
Tento seznam obsahuje všechny vaše aplikace zaregistrovaná pro použití s koncovým bodem v2.0 Azure AD. Tyto aplikace se budou moct přihlásit uživatele pomocí osobních účtů Microsoft a pracovních nebo školních účtů ze služby Azure Active Directory. Další informace o koncový bod Azure AD v2.0, najdete v článku [v2.0 přehled](active-directory-appmodel-v2-overview.md). Tyto aplikace lze také integrovat koncový bod Microsoft účet ověřování `https://login.live.com`.

## <a name="azure-ad-only-applications"></a>Jenom aplikace Azure AD
Tento seznam obsahuje všechny vaše aplikace zaregistrovaná pro použití ke koncovému bodu Azure AD verze 1.0. Tyto aplikace pouze se budou moct přihlásit uživatele pomocí pracovních nebo školních účtů ze služby Azure Active Directory. Tento seznam obsahuje aplikace, které jste zaregistrovali pomocí **registrace aplikací** dojít [webu Azure Portal](https://portal.azure.com).

## <a name="live-sdk-applications"></a>Aplikace sady Live SDK
Tento seznam obsahuje všechny vaše aplikace zaregistrovaná pro použití výhradně pomocí účtu Microsoft. Nejsou povoleny pro použití se službou Azure Active Directory. To je, kde můžete najít všechny aplikace, které byly zaregistrovány pomocí portálu pro vývojáře MSA na `https://account.live.com/developers/applications`. Všechny funkce, které jste provedli dříve na `https://account.live.com/developers/applications` se teď dá provádět na tomto novém portálu `https://apps.dev.microsoft.com`.

## <a name="application-secrets"></a>Tajné kódy aplikace
Tajné kódy aplikace jsou přihlašovací údaje, které umožňují aplikaci provádět spolehlivé [ověření klienta](https://tools.ietf.org/html/rfc6749#section-2.3) s Azure AD. V OAuth a OpenID Connect, tajný klíč aplikace se obvykle označuje jako `client_secret`. V protokolu v2.0, všechny aplikace, která přijímá token zabezpečení do adresovatelného umístění webové (pomocí `https` schéma) musíte použít tajný klíč aplikace identifikuje do služby Azure AD po uplatnění tohoto tokenu zabezpečení. Nativního klienta, které přijímá tokeny na zařízení se navíc je zakázané používat tajný klíč aplikace k ověřování klienta. To odrazuje od úložiště tajných kódů v nezabezpečené prostředí.

Každá aplikace může obsahovat dva tajné kódy platnou aplikaci v daném okamžiku. Udržovat dva tajné kódy, máte možnost provádět pravidelné výměny klíčů napříč celým prostředím vaší aplikace. Až provedete migraci celé vaší aplikace na nový tajný kód, můžete odstranit staré tajného kódu a zřízení nového.

V tuto chvíli v portálu pro registraci aplikace jsou povoleny pouze dva druhy tajných klíčů aplikací. Výběr **generovat nové heslo** generuje a uloží sdílený tajný klíč v příslušné datové úložiště, který můžete použít ve vaší aplikaci. Výběr **generovat nový pár klíč** vytvoří nový pár veřejného a privátního klíče, který je možné stáhnout a použít pro ověření klienta ke službě Azure AD. Výběr **nahrát veřejný klíč** umožňuje používat vlastní dvojice veřejného/soukromého klíče.
Je potřeba nahrát certifikát, který obsahuje veřejný klíč.

## <a name="profile"></a>Profil
Profil části portálu pro registraci aplikace je možné přizpůsobit přihlašovací stránku vaší aplikace. V tuto chvíli je možné změnit logo aplikace stránky přihlášení, podmínky adresa URL služby a adresu URL prohlášení o ochraně osobních údajů. Logo musí být průhledný obrázek 48×48 nebo 50×50 pixelů v souboru GIF, PNG nebo JPEG o velikosti 15 kB a míň. Zkuste změna hodnot a zobrazení výsledné přihlašovací stránky!

## <a name="live-sdk-support"></a>Živá podpora SDK
Když povolíte "podporu SDK Live", všechny tajné kódy aplikace vytvoříte, se zřídí do Azure AD a Microsoft Account datových úložišť. To umožňuje vaší aplikaci integrují přímo se službou Microsoft Account (login.live.com). Pokud chcete vytvořit aplikaci pomocí Microsoft Account přímo (na rozdíl od použití koncového bodu Azure AD v2.0), by se zkontrolujte, zda že je povolena podpora Live SDK.

Zakázat podporu Live SDK zajistí, že tajný klíč aplikace pouze zápisu do Azure AD data ukládat. Data služby Azure AD úložiště zahrnuje předpisy na podnikové úrovni, které povolit tak, aby vyhovovala určitými standardy, jako je například FISMA dodržování předpisů. Pokud povolíte podporu Live SDK, vaše aplikace nemusí dosáhnout souladu se standardem některé z těchto standardů.

Pokud chcete vždy jen pomocí koncového bodu Azure AD v2.0, můžete bezpečně zakázat podporu Live SDK.

