---
title: Zásada standardních hodnot – Ochrana koncového uživatele (Preview) – Azure Active Directory
description: Zásada podmíněného přístupu, která vyžaduje vícefaktorové ověřování pro uživatele
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f1a2e0bad39b54edc153416e4120bbc6912578c
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125457"
---
# <a name="baseline-policy-end-user-protection-preview"></a>Základní zásady: Ochrana koncového uživatele (Preview)

Doporučujeme, abyste si myslíte, že účty správců jsou jedinými účty, které potřebují ochranu pomocí vícefaktorového ověřování (MFA). Správci mají rozsáhlý přístup k citlivým informacím a můžou provádět změny nastavení pro celé předplatné. Chybné objekty actor ale mají za cíl koncové uživatele. Po získání přístupu můžou tyto vadné aktéry požádat o přístup k privilegovaným informacím jménem původního držitele účtu nebo stáhnout celý adresář, aby se v celé organizaci prováděl útok na útok phishing. Jednou z běžných metod, jak zlepšit ochranu pro všechny uživatele, je vyžadovat silnější formu ověření účtu, jako je vícefaktorové ověřování (MFA).

Za účelem dosažení přiměřené rovnováhy zabezpečení a použitelnosti by se uživatelům při každém přihlášení neměli zobrazovat výzva. Požadavky na ověření, které odráží normální chování uživatelů, například přihlášení ze stejného zařízení ze stejného umístění, mají nízkou pravděpodobnost napadení. V případě problémů s MFA by se měla zobrazit jenom přihlášení, která se považují za rizikové a ukazují charakteristiky vadného objektu actor.

Ochrana koncového uživatele je [zásada standardních hodnot](concept-baseline-protection.md) MFA založená na riziku, která chrání všechny uživatele v adresáři, včetně všech rolí správce. Když se tyto zásady povolí, vyžaduje se, aby se všichni uživatelé zaregistrovali pro MFA pomocí ověřovací aplikace. Uživatelé můžou ignorovat výzvu k registraci MFA po dobu 14 dnů, po které se budou zablokovat přihlášení, dokud se neregistrují pro MFA. Po registraci pro MFA se uživatelům zobrazí výzva k MFA jenom během pokusů o rizikové přihlašování. Ohrožení uživatelských účtů je zablokované, dokud se neobnoví jejich heslo, a detekce rizik byla zrušena.

> [!NOTE]
> Tato zásada platí pro všechny uživatele včetně účtů hostů a při přihlašování ke všem aplikacím se vyhodnotí.

## <a name="recovering-compromised-accounts"></a>Obnovování ohrožených účtů

Abychom mohli chránit naše zákazníky, služba nevrácených přihlašovacích údajů od Microsoftu vyhledá veřejně dostupné páry uživatelského jména a hesla. Pokud se shodují s jedním z našich uživatelů, pomůžeme tento účet hned zabezpečit. Uživatelé označení jako nevrácené přihlašovací údaje budou ověřeni z hlediska zabezpečení. Těmto uživatelům se zablokuje přihlášení, dokud se neobnoví jejich heslo.

Uživatelům, kteří mají přiřazenou licenci Azure AD Premium, můžete obnovit přístup prostřednictvím samoobslužného resetování hesla (SSPR), pokud je funkce ve svém adresáři povolená. Uživatelé bez licence Premium, která se zablokuje, musí požádat správce, aby provedl ruční resetování hesla, a vypustit detekci rizik uživatelů označených příznakem.

### <a name="steps-to-unblock-a-user"></a>Postup odblokování uživatele

Kontrolou přihlašovacích protokolů uživatele zkontrolujte, jestli je uživatel zablokovaný zásadami.

1. Správce se musí přihlásit k **Azure Portal** a přejít **Azure Active Directory** > **uživatelům** > klikněte na jméno uživatele a přejděte k části přihlášení.
1. Aby bylo možné iniciovat resetování hesla u blokovaného uživatele, musí správce přejít na **Azure Active Directory** > **uživatelů označených příznakem rizika** .
1. Klikněte na uživatele, jehož účet je blokovaný, abyste si zobrazili informace o nedávných aktivitách přihlášení uživatele.
1. Kliknutím na resetovat heslo přiřadíte dočasné heslo, které se musí změnit při dalším přihlášení.
1. Kliknutím na tlačítko Zavřít všechny události resetujete skóre rizika uživatele.

Uživatel se teď může přihlásit, resetovat heslo a získat přístup k aplikaci.

## <a name="deployment-considerations"></a>Aspekty nasazování

Vzhledem k tomu, že zásady **ochrany koncového uživatele** platí pro všechny uživatele ve vašem adresáři, je nutné provést několik důležitých informací, aby bylo zajištěno hladké nasazení. Mezi tyto požadavky patří určení uživatelů a zásad služeb ve službě Azure AD, které nemůžou nebo by neměly provádět MFA, a také aplikace a klienty používané ve vaší organizaci, které nepodporují moderní ověřování.

### <a name="legacy-protocols"></a>Starší protokoly

Starší verze ověřovacích protokolů (IMAP, SMTP, POP3 atd.) jsou používány e-mailové klienty k provádění požadavků na ověření. Tyto protokoly vícefaktorové ověřování nepodporují.  Většina kompromisů, které společnost Microsoft uvidí, je způsobena nesprávnými aktéry, které provádějí útoky proti starším protokolům, které se pokoušejí obejít MFA. Aby bylo zajištěno, že při přihlašování k účtu dojde k ověřování MFA a špatné objekty actor nebudou moci obejít MFA, tato zásada blokuje všechny požadavky na ověření provedené pro účty správců ze starších protokolů.

> [!WARNING]
> Než povolíte tuto zásadu, zajistěte, aby vaši uživatelé nepoužívali starší protokoly pro ověřování. Informace najdete v [článku Postupy: Zablokovat starší ověřování do Azure AD s](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) podmíněným přístupem pro další informace.

## <a name="enable-the-baseline-policy"></a>Povolit základní zásady

Zásady standardních **hodnot zásad: Ochrana koncového uživatele (Preview** ) je předem nakonfigurovaná a při přechodu do okna podmíněný přístup v Azure Portal se zobrazí v horní části.

Chcete-li povolit tuto zásadu a chránit uživatele:

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte na **Azure Active Directory** > **podmíněný přístup**.
1. V seznamu zásad vyberte **základní zásady: Ochrana koncového uživatele (Preview**).
1. Nastavte **Povolit zásadu** pro **okamžité použití zásad**.
1. Klikněte na **Uložit**.

## <a name="next-steps"></a>Další kroky

Další informace naleznete v tématu:

* [Zásady ochrany základní úrovně přístupu pro podmíněný přístup](concept-baseline-protection.md)
* [Pět kroků pro zabezpečení infrastruktury identity](../../security/fundamentals/steps-secure-identity.md)
* [Co je podmíněný přístup v Azure Active Directory?](overview.md)
