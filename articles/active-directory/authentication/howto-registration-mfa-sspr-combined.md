---
title: Povolit registraci kombinovaných informací o zabezpečení – Azure Active Directory
description: Naučte se, jak zjednodušit činnost koncového uživatele pomocí kombinované Multi-Factor Authentication služby Azure AD a registrace samoobslužného resetování hesla.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 01/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcfaef2f518028762958477a5b0d326acc237d1f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938428"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Povolit registraci kombinovaných informací o zabezpečení v Azure Active Directory

Před kombinovanou registrací uživatelé zaregistrovali metody ověřování pro Azure AD Multi-Factor Authentication a Samoobslužné resetování hesla (SSPR) samostatně. Lidem se zaznamenalo, že se podobné metody používaly pro Azure AD Multi-Factor Authentication a SSPR, ale musely se zaregistrovat pro obě funkce. Teď se při kombinované registraci můžou uživatelé zaregistrovat jednou a získat výhody pro Azure AD Multi-Factor Authentication a SSPR.

> [!NOTE]
> Od 15. srpna 2020 budou všichni noví klienti Azure AD automaticky povoleni pro kombinovanou registraci. 

Aby bylo zajištěno, že před povolením nového prostředí rozumíte funkcím a efektům, přečtěte si téma věnované [principům registrace informací o zabezpečení](concept-registration-mfa-sspr-combined.md).

![Rozšířené možnosti registrace informací o zabezpečení](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Povolení kombinované registrace

Pokud chcete povolit kombinovanou registraci, proveďte tyto kroky:

1. Přihlaste se k Azure Portal jako správce uživatele nebo globální správce.
2. Přejít na **Azure Active Directory**  >  **uživatelská nastavení**  >  **Spravovat nastavení funkce User Preview**.
3. V části **Uživatelé můžou použít kombinované prostředí pro registraci informací o zabezpečení** a vybrat možnost Povolit pro **vybranou** skupinu uživatelů nebo pro **všechny** uživatele.

   ![Povolit pro uživatele kombinované prostředí informací o zabezpečení](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> Po povolení kombinované registrace můžou uživatelé, kteří si zaregistrují nebo potvrzují své telefonní číslo nebo mobilní aplikace, využít nové prostředí pro Azure AD Multi-Factor Authentication a SSPR, pokud jsou tyto metody povolené v zásadách Multi-Factor Authentication a SSPR služby Azure AD.
>
> Pokud pak toto prostředí zakážete, uživatelé, kteří se na předchozí registrační stránce SSPR, `https://aka.ms/ssprsetup` budou muset před tím, než budou moci získat přístup ke stránce, provádět službu Multi-Factor Authentication.

Pokud jste v Internet Exploreru nakonfigurovali *seznam přiřazení lokality k zóně* , musí být tyto lokality ve stejné zóně:

* *[https://login.microsoftonline.com](https://login.microsoftonline.com)*
* *[https://mysignins.microsoft.com](https://mysignins.microsoft.com)*
* *[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)*

## <a name="conditional-access-policies-for-combined-registration"></a>Zásady podmíněného přístupu pro kombinovanou registraci

K zabezpečení, kdy a jak se uživatelé registrují pro Multi-Factor Authentication služby Azure AD a Samoobslužné resetování hesla, můžete použít akce uživatelů v zásadách podmíněného přístupu. Tato funkce může být povolená v organizacích, které chtějí uživatelé zaregistrovat pro Azure AD Multi-Factor Authentication a SSPR z centrálního umístění, jako je důvěryhodné síťové umístění při registraci lidských zdrojů.

> [!NOTE]
> Tato zásada platí pouze v případě, že uživatel přistupuje k kombinované registrační stránce. Tato zásada nevynutila registraci MFA, když uživatel přistupuje k jiným aplikacím.
>
> Zásadu registrace MFA můžete vytvořit pomocí [Azure Identity Protection – nakonfigurujte zásady vícefaktorového ověřování](../identity-protection/howto-identity-protection-configure-mfa-policy.md).

Další informace o vytváření důvěryhodných umístění v podmíněném přístupu najdete v tématu [co je podmínka umístění v Azure Active Directory podmíněný přístup?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Vytvoření zásady, která vyžaduje registraci z důvěryhodného umístění

Provedením následujících kroků vytvoříte zásadu, která bude platit pro všechny vybrané uživatele, kteří se pokusili zaregistrovat pomocí kombinovaného prostředí pro registraci, a zablokovat přístup, pokud se nepřipojí z umístění označeného jako důvěryhodná síť:

1. V **Azure Portal** přejděte na **Azure Active Directory**  >  **zabezpečení**  >  **podmíněný přístup**.
1. Vyberte **+ Nová zásada**.
1. Zadejte název pro tuto zásadu, jako je například *Kombinovaná registrace informací o zabezpečení v důvěryhodných sítích*.
1. V části **Přiřazení** vyberte **Uživatelé a skupiny**. Zvolte uživatele a skupiny, pro které chcete tuto zásadu použít, a potom vyberte **Hotovo**.

   > [!WARNING]
   > Uživatelé musí mít povolenou kombinovanou registraci.

1. V části **cloudové aplikace nebo akce** vyberte **akce uživatele**. Zaškrtněte políčko **zaregistrovat informace o zabezpečení** a potom vyberte **Hotovo**.

    ![Vytvoření zásady podmíněného přístupu pro řízení registrace bezpečnostních údajů](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. V části  >  **umístění** podmínek nakonfigurujte následující možnosti:
   1. Nakonfigurujte **Ano**.
   1. Uveďte **libovolné umístění**.
   1. Vylučte **všechna důvěryhodná umístění**.
1. V okně *umístění* vyberte **Hotovo** a pak v okně *podmínky* vyberte **Hotovo** .
1. V části **řízení přístupu**  >  **udělení** přístupu zvolte **blokovat přístup** a pak **Vyberte**.
1. Nastavte **Povolit zásadu** na **Zapnuté**.
1. Pokud chcete zásadu dokončit, vyberte **vytvořit**.

## <a name="next-steps"></a>Další kroky

Pokud potřebujete pomoct, přečtěte si téma [řešení potíží s registrací do kombinovaného zabezpečení](howto-registration-mfa-sspr-combined-troubleshoot.md) nebo zjistěte [, co je podmínka umístění v rámci podmíněného přístupu Azure AD?](../conditional-access/location-condition.md)

Jakmile budou uživatelé pro kombinovanou registraci povoleni, můžete [Povolit Samoobslužné resetování hesla](tutorial-enable-sspr.md) a [Povolit Multi-Factor Authentication služby Azure AD](tutorial-enable-azure-mfa.md).

V případě potřeby se dozvíte, jak [vynutit, aby uživatelé znovu zaregistrovali metody ověřování](howto-mfa-userdevicesettings.md#manage-user-authentication-options).
