---
title: Témata nápovědy portálu registrace aplikace | Microsoft Docs
description: Popis různých funkcí v portálu pro registraci aplikace společnosti Microsoft.
services: active-directory
documentationcenter: ''
author: lnalepa
manager: mtillman
editor: ''
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: lenalepa
ms.custom: aaddev
ms.openlocfilehash: dc915facfcd62aaa05c403895631614e1a3381b9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="app-registration-reference"></a>Informace o registraci aplikace
Tento dokument obsahuje kontextu a popisy různé funkce, které se nacházejí v portálu pro registraci aplikace Microsoft [ https://apps.dev.microsoft.com ](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/).

## <a name="my-applications"></a>Moje aplikace
Tento seznam obsahuje všechny aplikace zaregistrovaný pro použití s koncovým bodem v2.0 Azure AD. Tyto aplikace mají možnost přihlášení uživatelů s osobní účty Microsoft i pracovní nebo školní účty ze služby Azure Active Directory. Další informace o koncový bod v2.0 Azure AD, najdete v článku [v2.0 přehled](active-directory-appmodel-v2-overview.md). Tyto aplikace lze také integrovat s Microsoft účet ověřování koncového bodu, `https://login.live.com`.

## <a name="live-sdk-applications"></a>Live SDK aplikace
Tento seznam obsahuje všechny aplikace zaregistrovaný pro použití výhradně pomocí účtu Microsoft. Nejsou povoleny pro použití se službou Azure Active Directory. Toto je, kde můžete najít všechny aplikace, které byly dříve registrován u MSA portál pro vývojáře v `https://account.live.com/developers/applications`. Všechny funkce, které jste provedli dříve v `https://account.live.com/developers/applications` provést nyní tento nový portál `https://apps.dev.microsoft.com`. Pokud máte další dotazy o aplikace účtu Microsoft, kontaktujte nás.

## <a name="application-secrets"></a>Tajné kódy aplikace
Tajné klíče aplikace jsou přihlašovací údaje, které umožňují aplikace provádět spolehlivé [ověření klienta](http://tools.ietf.org/html/rfc6749#section-2.3) s Azure AD. V OAuth a OpenID Connect tajný klíč aplikace se obvykle označuje jako `client_secret`. V protokolu v2.0, všechny aplikace, která přijímá token zabezpečení v umístění adresovatelné webové (pomocí `https` schéma) musíte použít tajný klíč aplikace identifikuje do služby Azure AD při uplatnění tohoto tokenu zabezpečení. Všechny nativního klienta, která přijímá tokeny na zařízení se navíc je zakázané z pomocí tajný klíč aplikace k provedení ověření klienta. To nedoporučuje úložiště tajných klíčů v nezabezpečené prostředí.

Každá aplikace může obsahovat dva tajné klíče platnou aplikaci v daném okamžiku. Udržovat dva tajné klíče, máte možnost provádět pravidelné výměna klíče na celé prostředí vaší aplikace. Jakmile jste migrovali celého aplikace na nový sdílený tajný klíč, můžete odstranit starý sdílený tajný klíč a zřízení nového.

V tomto okamžiku jsou povoleny pouze dva typy tajné klíče aplikace v portálu pro registraci aplikace. Výběr **generovat nové heslo** generuje a uloží sdílený tajný klíč v úložišti příslušných dat, který můžete použít v aplikaci. Výběr **vygenerovat nový pár klíč** vytvoří nový pár veřejného a privátního klíče, je možné stáhnout a použít pro ověřování klientů na Azure AD. Výběr **nahrát veřejný klíč** umožňuje používat vlastní pár veřejného a privátního klíče.
Je nutné odeslat certifikát, který obsahuje veřejný klíč.

## <a name="profile"></a>Profil
Část profilu portálu pro registraci aplikace slouží k přizpůsobení stránce přihlášení pro vaši aplikaci. V tuto chvíli můžete změnit logo aplikace stránky přihlášení, podmínky adresa URL služby a adresa URL prohlášení o ochraně osobních údajů. Logo musí být průhledný obrázek 48×48 nebo 50×50 pixelů v souboru GIF, PNG nebo JPEG o velikosti 15 kB a míň. Zkuste změna hodnoty a zobrazení výsledné přihlašovací stránky!

## <a name="live-sdk-support"></a>Live SDK podpory
Pokud povolíte "podporu SDK Live", žádné aplikace tajné klíče vytvoříte, se zřídí do služby Azure AD a úložišť dat Account Microsoft. To umožňuje integrovat službu Microsoft Account (login.live.com) vaší aplikace. Pokud chcete vytvořit aplikaci pomocí Microsoft Account přímo (na rozdíl od použití koncového bodu v2.0 Azure AD), měli byste si ověřit, jestli že je zapnutá podpora Live SDK.

Zakázání podpory Live SDK zajistí, že tajný klíč aplikace je zapisovat pouze do Azure AD data uložit. Azure AD data úložiště zapadá regulace podnikové úrovni, umožňujících splnit nějaké standardy, jako je například FISMA dodržování předpisů. Pokud povolíte podporu Live SDK, vaše aplikace nemusí zajištění dodržování předpisů pomocí některé z těchto standardů.

Pokud plánujete pouze někdy použít koncového bodu v2.0 Azure AD, můžete bezpečně zakázat podporu Live SDK.

