---
title: Základní zásady ochrany koncového uživatele (preview) – Azure Active Directory
description: Zásady podmíněného přístupu na vyžadovat vícefaktorové ověřování pro uživatele
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
ms.openlocfilehash: 35cd52fb82e5e4cce759be5dfdd8872f64802459
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003158"
---
# <a name="baseline-policy-end-user-protection-preview"></a>Základní zásady: Ochrana koncového uživatele (preview)

Často Představujeme myslíte, že účty správců jsou to jediné účty, které potřebují ochranu pomocí služby Multi-Factor authentication (MFA). Správci mají široký přístup k citlivým informacím a můžete provádět změny nastavení na úrovni předplatného. Nesprávnými účastníky však mají tendenci cíl koncovým uživatelům. Po získání přístupu, tyto nesprávnými účastníky můžete požádat o přístup k privilegovaným informace jménem původního vlastníka účtu nebo stáhnout celý adresář k provedení útoku phishing v celé organizaci. Jeden běžnou metodu ke zlepšení ochrany pro všechny uživatele je tak, aby vyžadovala silnější formu ověření účtu, jako je například vícefaktorové ověřování (MFA).

K dosažení přiměřené rovnováhu mezi zabezpečení a použitelnost, by neměla uživatelé vyzváni pokaždé, když jeden přihlašuje. Požadavky na ověření, které odpovídají chování běžného uživatele, jako je například přihlášení ze stejného zařízení ze stejného umístění, mají nízké riziko ohrožení zabezpečení. Pouze přihlášení, která se považují za riziková a zobrazit vlastnosti objektu actor chybný měla zobrazit výzva s MFA výzvy.

![Vyžadovat vícefaktorové ověřování pro uživatele](./media/howto-baseline-protect-end-users/baseline-policy-end-user-protection.png)

Ochrana koncového uživatele je MFA na základě rizik [základní zásady](concept-baseline-protection.md) všichni uživatelé v adresáři, včetně všech rolí správce, které chrání. Když se tyto zásady vyžaduje všichni uživatelé k registraci pro vícefaktorové ověřování pomocí ověřovací aplikace. Uživatelé můžou ignorovat řádku registrace MFA za 14 dní, po jejichž uplynutí bude se mu zablokovat přihlášení, dokud se registrace pro vícefaktorové ověřování. Po registraci pro vícefaktorové ověřování, budou uživatelé vyzváni pro vícefaktorové ověřování pouze během rizikové pokusů o přihlášení. Ohrožení uživatelských účtů jsou blokovány, dokud resetovat své heslo a zamítnutou rizikové události.

> [!NOTE]
> Tyto zásady platí pro všechny uživatele včetně účtů typu Host a vyhodnotí při přihlašování do všech aplikací.

## <a name="recovering-compromised-accounts"></a>Obnovení dojde k ohrožení bezpečnosti účty

K ochraně našich zákazníků, vyhledá uniklými přihlašovacími údaji služby páry veřejně dostupné uživatelského jména a hesla. Pokud se shodují, jedním z našich uživatelů, pomáháme zabezpečit tento účet okamžitě. Uživatelé s uniklými přihlašovacími údaji potvrzují, dojde k ohrožení bezpečnosti. Tyto uživatele bude blokovat přihlásit, dokud resetovat své heslo.

Uživatelé přiřazenou licenci Azure AD Premium můžete obnovit přístup prostřednictvím samoobslužného resetování hesla (SSPR), pokud je zapnutá možnost v jejich adresáře. Uživatelé bez licence premium, které budou blokovány musí kontaktovat správce provést obnovení ruční hesla a zavřít uživatel označený příznakem rizikové události.

### <a name="steps-to-unblock-a-user"></a>Postup pro odblokování uživatele

Potvrďte, že uživatel je blokován zásadami zkoumáním protokolů přihlášení uživatele.

1. Správce musí přihlásit k **webu Azure portal** a přejděte do **Azure Active Directory** > **uživatelé** > klikněte na jméno uživatele a přejděte k přihlášení.
1. K zahájení resetování hesla na blokovaný uživatel, musí správce přejít na **Azure Active Directory** > **uživatelé označení příznakem rizika**
1. Kliknutím na uživatele, jehož účet je zablokovaný, chcete-li zobrazit informace o uživatele nedávných aktivit přihlašování.
1. Klikněte na tlačítko resetovat heslo pro přiřazení dočasné heslo, které se musí změnit při dalším přihlášení.
1. Klikněte na tlačítko Zavřít všechny události resetovat skóre rizika uživatele.

Uživatel teď může přihlásit, obnovit své heslo a přístup k aplikaci.

## <a name="deployment-considerations"></a>Aspekty nasazování

Vzhledem k tomu, **ochrany koncového uživatele** zásady platí pro všechny uživatele ve vašem adresáři, třeba mít na paměti, které jsou potřeba provést zajistit hladký průběh nasazení. Mezi tyto aspekty patří identifikace uživatelů a zásad služby Azure AD, který nelze nebo by neměly provádět vícefaktorové ověřování, jakož i aplikacím a klientům ve své organizaci, které nepodporují moderní ověřování.

### <a name="legacy-protocols"></a>Starší protokoly

Poštovní klienti k podání žádostí o ověření používají starší verze ověřovací protokoly (IMAP, SMTP, POP3, atd.). Tyto protokoly nepodporuje vícefaktorové ověřování.  Většina ohrožení účet viděli microsoftem jsou způsobeny nesprávnými účastníky provádění útoky na starších verzí protokolů pokus o obejití vícefaktorové ověřování. Zajistíte, že se vyžaduje vícefaktorové ověřování při přihlašování účtu a nesprávnými účastníky nedokáží obejít MFA, tato zásada blokuje provedené účty správců od starších verzí protokolů všechny požadavky na ověření.

> [!WARNING]
> Předtím, než tuto zásadu povolit, ujistěte se, že vaši uživatelé nepoužívají starší ověřovací protokoly. Přečtěte si článek [jak: Blok starší verze ověřování do služby Azure AD s podmíněným přístupem](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) Další informace.

### <a name="user-exclusions"></a>Vyloučení uživatele

Tyto zásady na směrný plán poskytuje možnost vyloučit uživatele. Než povolíte zásady pro vašeho tenanta, doporučujeme, abyste kromě následující účty:

* **Nouzový přístup** nebo **skleněné** účty, aby se zabránilo uzamčení účtu celého tenanta. V nepravděpodobném scénáři, které jsou všichni správci zamknutí mimo vašeho tenanta je možné přihlásit tenanta kroky zkuste obnovit přístup k účtu pro správu přístupu nouze.
   * Další informace najdete v článku, [spravovat účty pro nouzový přístup ve službě Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Účty služeb** a **služby Principy**, jako je například účet Azure AD Connect Sync. Účty služeb jsou neinteraktivní účty, které nejsou vázané na konkrétního uživatele. Tyto jsou obvykle používány back endové služby a povolit programový přístup k aplikacím. Účty služby by měl vyloučen, protože nelze dokončit vícefaktorové ověřování, prostřednictvím kódu programu.
   * Pokud má vaše organizace tyto účty používané v skripty a kód, zvažte nahrazení pomocí [spravovaných identit](../managed-identities-azure-resources/overview.md). Jako dočasné řešení můžete vyloučit tyto konkrétní účty ze základní zásady.
* Uživatelé, kteří nemají nebo nebudete moci používat Smartphone.
   * Tato zásada vyžaduje, aby uživatelé registrace pro vícefaktorové ověřování pomocí aplikace Microsoft Authenticator.

## <a name="enable-the-baseline-policy"></a>Povolit zásady směrný plán

Zásady **směrný plán zásad: Ochrana koncového uživatele (preview)** vybavená předem nakonfigurovaným a se zobrazí v horní části, když přejdete do okna podmíněného přístupu na webu Azure portal.

Tuto zásadu povolit a chránit vaše správce:

1. Přihlaste se k **webu Azure portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte do **Azure Active Directory** > **podmíněného přístupu**.
1. V seznamu zásad, vyberte **směrný plán zásad: Ochrana koncového uživatele (preview)**.
1. Nastavte **povolit zásady** k **použít zásady okamžitě**.
1. Kliknutím na Přidat všechny uživatele vyloučení **uživatelé** > **vybrat vyloučené uživatele** a výběru uživatelů, které je třeba vyloučit. Klikněte na tlačítko **vyberte** pak **provádí**.
1. Klikněte na tlačítko **Uložit**.

## <a name="next-steps"></a>Další postup

Další informace naleznete v tématu:

* [Zásady podmíněného přístupu směrný plán ochrany](concept-baseline-protection.md)
* [Zabezpečení vaší infrastruktury identit v pěti krocích](../../security/azure-ad-secure-steps.md)
* [Co je podmíněný přístup v Azure Active Directory?](overview.md)
