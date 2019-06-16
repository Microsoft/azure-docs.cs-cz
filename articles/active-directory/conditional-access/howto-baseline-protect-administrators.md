---
title: Základní zásady vyžadovat vícefaktorové ověřování pro správce – Azure Active Directory
description: Zásady podmíněného přístupu tak, aby vyžadovala ověření službou Multi-Factor Authentication pro správce
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
ms.openlocfilehash: 4f8b7f281ad5ed8424110696544ffdb49e50ce59
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112427"
---
# <a name="baseline-policy-require-mfa-for-admins"></a>Základní zásady: Vyžadování MFA pro správce

Uživatelé s přístupem k privilegovaným účtům mají neomezený přístup k prostředí. Kvůli výkonu, které nemají tyto účty by měly zpracovávat s zvláštní pozornost. Běžným způsobem zlepšit ochranu privilegovaných účtů je tak, aby vyžadovala silnější formu ověření účtu, když se používají k registraci. Ve službě Azure Active Directory získáte silnější ověření účtu tak, že vyžaduje vícefaktorové ověřování (MFA).

**Vyžadovat vícefaktorové ověřování pro správce** je [základní zásady](concept-baseline-protection.md) , která vyžaduje vícefaktorové ověřování pokaždé, když se jeden z následujících správce privilegovaných rolí přihlášení:

* Globální správce
* Správce SharePointu
* Správce Exchange
* Správce podmíněného přístupu
* Správce zabezpečení
* Správce technické podpory nebo správce hesel
* Správce fakturace
* Správce uživatelů

Při zapnout vícefaktorové ověřování vyžadovat pro správce zásady, výše devět správce rolí je třeba registrace pro vícefaktorové ověřování pomocí ověřovací aplikace. Po dokončení registrace MFA musí správci provádět vícefaktorové ověřování pokaždé, když jeden přihlašuje.

![Vyžadovat vícefaktorové ověřování pro admins základní zásady](./media/howto-baseline-protect-administrators/baseline-policy-require-mfa-for-admins.png)

## <a name="deployment-considerations"></a>Aspekty nasazování

Vzhledem k tomu, **vyžadovat vícefaktorové ověřování pro správce** zásady platí pro všechny kritické správce, třeba mít na paměti, které jsou potřeba provést zajistit hladký průběh nasazení. Mezi tyto aspekty patří identifikace uživatelů a zásad služby Azure AD, který nelze nebo by neměly provádět vícefaktorové ověřování, jakož i aplikacím a klientům ve své organizaci, které nepodporují moderní ověřování.

### <a name="legacy-protocols"></a>Starší protokoly

Poštovní klienti k podání žádostí o ověření používají starší verze ověřovací protokoly (IMAP, SMTP, POP3, atd.). Tyto protokoly nepodporuje vícefaktorové ověřování. Většina ohrožení účet viděli microsoftem jsou způsobeny nesprávnými účastníky provádění útoky na starších verzí protokolů pokus o obejití vícefaktorové ověřování. Zajistíte, že se vyžaduje vícefaktorové ověřování při přihlašování k účtu správce a nesprávnými účastníky nedokáží obejít MFA, tato zásada blokuje provedené účty správců od starších verzí protokolů všechny požadavky na ověření.

> [!WARNING]
> Předtím, než tuto zásadu povolit, ujistěte se, že vaši správci nejsou používá starší verzi ověřovacích protokolů. Přečtěte si článek [jak: Blok starší verze ověřování do služby Azure AD s podmíněným přístupem](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) Další informace.

### <a name="user-exclusions"></a>Vyloučení uživatele

Tyto zásady na směrný plán poskytuje možnost vyloučit uživatele. Než povolíte zásady pro vašeho tenanta, doporučujeme, abyste kromě následující účty:

* **Nouzový přístup** nebo **skleněné** účty, aby se zabránilo uzamčení účtu celého tenanta. V nepravděpodobném scénáři, které jsou všichni správci zamknutí mimo vašeho tenanta je možné přihlásit tenanta kroky zkuste obnovit přístup k účtu pro správu přístupu nouze.
   * Další informace najdete v článku, [spravovat účty pro nouzový přístup ve službě Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Účty služeb** a **služby Principy**, jako je například účet Azure AD Connect Sync. Účty služeb jsou neinteraktivní účty, které nejsou vázané na konkrétního uživatele. Tyto jsou obvykle používány back endové služby a povolit programový přístup k aplikacím. Účty služby by měl vyloučen, protože nelze dokončit vícefaktorové ověřování, prostřednictvím kódu programu.
   * Pokud má vaše organizace tyto účty používané v skripty a kód, zvažte nahrazení pomocí [spravovaných identit](../managed-identities-azure-resources/overview.md). Jako dočasné řešení můžete vyloučit tyto konkrétní účty ze základní zásady.
* Uživatelé, kteří nemají nebo nebudete moci používat Smartphone.
   * Tato zásada vyžaduje správci registrace pro vícefaktorové ověřování pomocí aplikace Microsoft Authenticator.

## <a name="enable-the-baseline-policy"></a>Povolit zásady směrný plán

Zásady **směrný plán zásad: Vyžadovat vícefaktorové ověřování pro správce** vybavená předem nakonfigurovaným a se zobrazí v horní části, když přejdete do okna podmíněného přístupu na webu Azure portal.

Tuto zásadu povolit a chránit vaše správce:

1. Přihlaste se k **webu Azure portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte do **Azure Active Directory** > **podmíněného přístupu**.
1. V seznamu zásad, vyberte **směrný plán zásad: Vyžadovat vícefaktorové ověřování pro správce**.
1. Nastavte **povolit zásady** k **použít zásady okamžitě**.
1. Kliknutím na Přidat všechny uživatele vyloučení **uživatelé** > **vybrat vyloučené uživatele** a výběru uživatelů, které je třeba vyloučit. Klikněte na tlačítko **vyberte** pak **provádí**.
1. Klikněte na tlačítko **Uložit**.

## <a name="next-steps"></a>Další postup

Další informace naleznete v tématu:

* [Zásady ochrany směrného plánu podmíněného přístupu](concept-baseline-protection.md)
* [Zabezpečení vaší infrastruktury identit v pěti krocích](../../security/azure-ad-secure-steps.md)
* [Co je podmíněný přístup v Azure Active Directory?](overview.md)
