---
title: Témata nápovědy portálu pro registraci aplikací | Microsoft Docs
description: Popis různých funkcí na portálu pro registraci aplikací společnosti Microsoft.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: ryanwi
ms.reviewer: lenalepa
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49675d8b18020c73a27a41fedff47697e29d829e
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988493"
---
# <a name="app-registration-reference"></a>Reference k registraci aplikací
Tento dokument obsahuje kontext a popisy různých funkcí, které najdete v Azure Portal prostředí [Registrace aplikací](https://aka.ms/appregistrations) .

## <a name="my-applications-or-converged-applications"></a>Moje aplikace nebo sblížené aplikace
Tento seznam obsahuje všechny vaše aplikace zaregistrované pro použití s koncovým bodem Microsoft Identity Platform (v 2.0). Tyto aplikace mají možnost přihlásit uživatele pomocí osobních účtů Microsoft i pracovních nebo školních účtů z Azure Active Directory. Další informace o koncovém bodu platformy identity najdete v tématu [Přehled verze 2.0](active-directory-appmodel-v2-overview.md). Tyto aplikace je také možné použít k integraci s koncovým bodem `https://login.live.com`ověřování účet Microsoft.

## <a name="azure-ad-only-applications"></a>Jenom aplikace Azure AD
Tento seznam obsahuje všechny vaše aplikace zaregistrované pro použití s koncovým bodem Azure AD v 1.0. Tyto aplikace umožňují uživatelům přihlašovat se pomocí pracovních nebo školních účtů z Azure Active Directory. Tento seznam obsahuje aplikace, které byly zaregistrovány pomocí prostředí **Registrace aplikací** v [Azure Portal](https://portal.azure.com).

## <a name="live-sdk-applications"></a>Živé aplikace sady SDK
Tento seznam obsahuje všechny vaše aplikace zaregistrované pro použití výhradně s účet Microsoft. Nejsou povolené pro použití s Azure Active Directory. Tady najdete všechny aplikace, které byly dříve zaregistrované na portálu pro vývojáře MSA na `https://account.live.com/developers/applications`adrese. Všechny funkce, které jste dříve provedli `https://account.live.com/developers/applications` , se teď dají provádět v [Registrace aplikací](https://aka.ms/appregistrations).

## <a name="application-secrets"></a>Tajné kódy aplikace
Tajné klíče aplikace jsou přihlašovací údaje, které umožňují vaší aplikaci provádět spolehlivé [ověřování klientů](https://tools.ietf.org/html/rfc6749#section-2.3) pomocí Azure AD. V protokolu OAuth & OpenID Connect se klíč aplikace často označuje jako `client_secret`. V protokolu v 2.0 musí každá aplikace, která obdrží token zabezpečení v umístění web s adresou (pomocí `https` schématu), používat tajný klíč aplikace k identifikaci služby Azure AD při uplatnění tohoto tokenu zabezpečení. Kromě toho bude pro všechny nativní klienty, kteří přijímají tokeny na zařízení, zakázáno používat k ověřování klientů tajný klíč aplikace. Tím se nedoporučuje ukládání tajných klíčů v nezabezpečených prostředích.

Každá aplikace může v daném okamžiku obsahovat dvě platné tajné klíče aplikace. Díky udržování dvou tajných kódů máte možnost provádět pravidelné výměny klíčů v celém prostředí vaší aplikace. Po dokončení migrace celé aplikace na nový tajný kód můžete odstranit starý tajný klíč a zřídit nový.

V tuto chvíli jsou na portálu pro registraci aplikací povolené jenom dva typy tajných klíčů. Zvolením možnosti **Generovat nové heslo** vygenerujete a uložíte sdílený tajný klíč do příslušného úložiště dat, které můžete použít ve své aplikaci. Výběrem možnosti **generovat nový pár klíčů** se vytvoří nový pár veřejného a privátního klíče, který se dá stáhnout a použít pro ověřování klientů v Azure AD. Možnost **nahrát veřejný klíč** vám umožní používat vlastní pár veřejného a privátního klíče.
Je nutné nahrát certifikát, který obsahuje veřejný klíč.

## <a name="profile"></a>Profil
Oddíl profil portálu pro registraci aplikací se dá použít k přizpůsobení přihlašovací stránky pro vaši aplikaci. V tuto chvíli můžete změnit logo aplikace přihlašovací stránky, adresu URL podmínek služby a adresu URL prohlášení o zásadách ochrany osobních údajů. Logo musí být průhledný obrázek 48×48 nebo 50×50 pixelů v souboru GIF, PNG nebo JPEG o velikosti 15 kB a míň. Zkuste změnit hodnoty a zobrazit výslednou přihlašovací stránku!

## <a name="live-sdk-support"></a>Podpora sady Live SDK
Pokud povolíte podporu sady Live SDK, budou se všechny tajné klíče aplikace, které vytvoříte, zřídí do úložišť dat Azure AD i z účtu Microsoft. Díky tomu může být vaše aplikace integrována přímo se službou účtu Microsoft (login.live.com). Pokud chcete vytvořit aplikaci pomocí účtu Microsoft přímo (na rozdíl od použití koncového bodu v 2.0), měli byste zajistit, aby byla povolená podpora živé sady SDK.

Když se zakáže podpora sady Live SDK, zajistíte tak, že se tajný klíč aplikace zapíše jenom do úložiště dat Azure AD. Úložiště dat Azure AD zahrnuje předpisy na podnikové úrovni, které jim umožní splnit určité standardy, například FISMA dodržování předpisů. Pokud povolíte podporu sady Live SDK, vaše aplikace nemusí dosáhnout souladu s některými z těchto standardů.

Pokud máte v plánu používat jenom koncový bod verze 2.0, můžete aktivní podporu sady SDK bezpečně zakázat.

