---
title: Začínáme s kombinovanou registrací – Azure Active Directory
description: Povolit kombinované Multi-Factor Authentication služby Azure AD a registraci samoobslužného resetování hesla
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7feb69b2ea53794b780a983ed8ab4ba5874ac022
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85260844"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Povolit registraci kombinovaných informací o zabezpečení v Azure Active Directory

Před kombinovanou registrací uživatelé zaregistrovali metody ověřování pro Azure Multi-Factor Authentication a Samoobslužné resetování hesla (SSPR) samostatně. Lidé si zaznamenali, že podobné metody byly použity pro Azure Multi-Factor Authentication a SSPR, ale musely se zaregistrovat pro obě funkce. Teď se při kombinované registraci můžou uživatelé zaregistrovat jednou a získat výhody pro Azure Multi-Factor Authentication i SSPR.

Než začnete s novým prostředím, přečtěte si článek [Kombinovaná registrace informací o zabezpečení](concept-registration-mfa-sspr-combined.md) , která vám pomůže pochopit funkčnost této funkce a jejich vliv.

![Rozšířené možnosti registrace informací o zabezpečení](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Povolení kombinované registrace

Pokud chcete povolit kombinovanou registraci, proveďte tyto kroky:

1. Přihlaste se k Azure Portal jako správce uživatele nebo globální správce.
2. Přejít na **Azure Active Directory**  >  **uživatelská nastavení**  >  **Spravovat nastavení funkce User Preview**.
3. V části **Uživatelé můžou použít kombinované prostředí pro registraci informací o zabezpečení**a vybrat možnost Povolit pro **vybranou** skupinu uživatelů nebo pro **všechny** uživatele.

   ![Povolit pro uživatele kombinované prostředí informací o zabezpečení](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> Po povolení kombinované registrace si uživatelé, kteří si zaregistrují nebo potvrdí jejich telefonní číslo nebo mobilní aplikaci prostřednictvím nového prostředí, můžou použít pro Azure Multi-Factor Authentication a SSPR, pokud jsou tyto metody povolené v zásadách Azure Multi-Factor Authentication a SSPR. Pokud pak toto prostředí zakážete, budou se uživatelé, kteří na předchozí registrační stránce SSPR, `https://aka.ms/ssprsetup` napřed k přístupu k této stránce vyzváni, aby provedli službu Multi-Factor Authentication.

Pokud jste v Internet Exploreru nakonfigurovali seznam přiřazení lokality k zóně, musí být tyto lokality ve stejné zóně:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Zásady podmíněného přístupu pro kombinovanou registraci

Zabezpečení, kdy a jak se uživatelé registrují pro Azure Multi-Factor Authentication a Samoobslužné resetování hesla, je teď možné u uživatelských akcí v zásadách podmíněného přístupu. Tato funkce je k dispozici organizacím, u kterých je povolena [funkce kombinovaná registrace](../authentication/concept-registration-mfa-sspr-combined.md). Tato funkce může být povolená v organizacích, kde se chtějí uživatelé zaregistrovat pro Azure Multi-Factor Authentication a SSPR z centrálního umístění, jako je důvěryhodné síťové umístění při registraci lidských zdrojů.

> [!NOTE]
> Tato zásada platí pouze v případě, že uživatel přistupuje k kombinované registrační stránce. Tato zásada nevynutila registraci MFA, když uživatel přistupuje k jiným aplikacím. Zásadu registrace MFA můžete vytvořit pomocí [Azure Identity Protection – nakonfigurujte zásady vícefaktorového ověřování](../identity-protection/howto-identity-protection-configure-mfa-policy.md).

Další informace o vytváření důvěryhodných umístění v podmíněném přístupu najdete v článku [co je podmínka umístění v Azure Active Directory podmíněný přístup?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Vytvoření zásady, která vyžaduje registraci z důvěryhodného umístění

Následující zásady platí pro všechny vybrané uživatele, kteří se pokoušejí zaregistrovat pomocí kombinovaného prostředí pro registraci, a zablokují přístup, pokud se nepřipojují z umístění označeného jako důvěryhodná síť.

1. V **Azure Portal**přejděte na **Azure Active Directory**  >  **zabezpečení**  >  **podmíněný přístup**.
1. Vyberte **+ Nová zásada**.
1. Zadejte název pro tuto zásadu, jako je například *Kombinovaná registrace informací o zabezpečení v důvěryhodných sítích*.
1. V části **Přiřazení** vyberte **Uživatelé a skupiny**. Zvolte uživatele a skupiny, pro které chcete tuto zásadu použít, a potom vyberte **Hotovo**.

   > [!WARNING]
   > Uživatelé musí mít povolenou kombinovanou registraci.

1. V části **cloudové aplikace nebo akce**vyberte **akce uživatele**. Zaškrtněte políčko **zaregistrovat informace o zabezpečení**a potom vyberte **Hotovo**.

    ![Vytvoření zásady podmíněného přístupu pro řízení registrace bezpečnostních údajů](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. V **Conditions**části  >  **umístění**podmínek nakonfigurujte následující možnosti:
   1. Nakonfigurujte **Ano**.
   1. Uveďte **libovolné umístění**.
   1. Vylučte **všechna důvěryhodná umístění**.
1. V okně *umístění* vyberte **Hotovo** a pak v okně *podmínky* vyberte **Hotovo** .
1. V části **řízení přístupu**  >  **udělení**přístupu zvolte **blokovat přístup**a pak **Vyberte**.
1. Nastavte **Povolit zásadu** na **Zapnuté**.
1. Pokud chcete zásadu dokončit, vyberte **vytvořit**.

## <a name="next-steps"></a>Další kroky

Pokud potřebujete pomoct, přečtěte si téma Jak [řešit problémy s registrací do kombinovaného zabezpečení](howto-registration-mfa-sspr-combined-troubleshoot.md) nebo zjistit [, co je podmínka umístění v Azure Active Directory podmíněný přístup?](../conditional-access/location-condition.md)

Pokud chcete povolit funkce ve vašem tenantovi Azure AD, přečtěte si kurzy, jak [Povolit Samoobslužné resetování hesla](tutorial-enable-sspr.md) a [Povolit Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

Naučte se, jak [vynutit, aby uživatelé znovu zaregistrovali metody ověřování](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

Můžete si také projít [dostupné metody pro Azure Multi-Factor Authentication a SSPR](concept-authentication-methods.md).
