---
title: Začínáme s kombinovanou registrací – Azure Active Directory
description: Povolení kombinovaného vícefaktorového ověřování Azure AD a samoobslužné registrace resetování hesla
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
ms.openlocfilehash: 466b063253ee49ab58c2685f359b4bb8a4079532
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639674"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Povolení kombinované registrace informací o zabezpečení ve službě Azure Active Directory

Před kombinovanou registrací uživatelé zaregistrovali metody ověřování pro Azure Multi-Factor Authentication a samoobslužné resetování hesla (SSPR) samostatně. Lidé byli zmateni, že podobné metody byly použity pro multi-factor ověřování a SSPR, ale museli se zaregistrovat pro obě funkce. Nyní, s kombinovanou registrací, se uživatelé mohou zaregistrovat jednou a získat výhody multifaktorového ověřování a samodotykového ověřování.

Před povolením nového prostředí si přečtěte článek [Kombinovaná registrace informací o zabezpečení,](concept-registration-mfa-sspr-combined.md) abyste měli jistotu, že rozumíte funkcím a účinkům této funkce.

![Rozšířené možnosti kombinované registrace bezpečnostních informací](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Povolení kombinované registrace

Chcete-li povolit kombinovanou registraci, proveďte tyto kroky:

1. Přihlaste se k portálu Azure jako správce uživatelů nebo globální správce.
2. Přejděte na**nastavení** > uživatele **služby Azure Active Directory** > **Správa nastavení náhledu uživatelských funkcí**.
3. V části **Uživatelé mohou používat funkce náhledu pro registraci a správu bezpečnostních údajů**, zvolte povolení pro **vybranou** skupinu uživatelů nebo pro **všechny** uživatele.

   ![Povolení kombinovaného náhledu informací o zabezpečení pro všechny uživatele](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!NOTE]
> Po povolení kombinované registrace mohou uživatelé, kteří zaregistrují nebo potvrdí své telefonní číslo nebo mobilní aplikaci prostřednictvím nového prostředí, použít pro vícefaktorové ověřování a samoobslužné ověřování, pokud jsou tyto metody povoleny v zásadách vícefaktorového ověřování a samoobslužného tisku. Pokud pak zakázat toto prostředí, uživatelé, kteří `https://aka.ms/ssprsetup` přejdou na předchozí stránku registrace samoobslužného práva k obsluze bude muset provést vícefaktorové ověřování před tím, než budou mít přístup ke stránce.

Pokud jste v aplikaci Internet Explorer nakonfigurovali seznam přiřazení webu na zónu, musí být ve stejné zóně následující weby:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Zásady podmíněného přístupu pro kombinovanou registraci

Zabezpečení, kdy a jak se uživatelé registrují pro azure vícefaktorové ověřování a samoobslužné resetování hesla je teď možné s akcemi uživatelů v zásadách podmíněného přístupu. Tato funkce je k dispozici organizacím, které povolily [kombinovanou funkci registrace](../authentication/concept-registration-mfa-sspr-combined.md). Tato funkce může být povolena v organizacích, kde chtějí, aby se uživatelé registrovali pro Azure Multi-Factor Authentication a SSPR z centrálního umístění, jako je důvěryhodné umístění v síti během registrace hr.

Další informace o vytváření důvěryhodných umístění v podmíněném přístupu najdete v článku [Jaká je podmínka umístění v podmíněném přístupu služby Azure Active Directory?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Vytvoření zásady, která vyžaduje registraci z důvěryhodného umístění

Následující zásady platí pro všechny vybrané uživatele, kteří se pokoušejí zaregistrovat pomocí kombinovaného registračního prostředí, a blokuje přístup, pokud se nepřipojují z umístění označeného jako důvěryhodná síť.

1. Na **webu Azure Portal**přejděte na Azure **Active Directory** > **Security** > **Conditional Access** .
1. Vybrat **+ Nové zásady**
1. Zadejte název této zásady, *například Kombinovanou registraci informací o zabezpečení v důvěryhodných sítích*.
1. V části **Přiřazení** vyberte **Uživatelé a skupiny**. Vyberte uživatele a skupiny, na které se má tato zásada vztahovat, a vyberte **Hotovo**.

   > [!WARNING]
   > Uživatelé musí mít povolenou kombinovanou registraci.

1. V části **Cloudové aplikace nebo akce**vyberte **Akce uživatele**. Zkontrolujte **informace o zabezpečení registru**a vyberte možnost **Hotovo**.

    ![Vytvoření zásad podmíněného přístupu pro řízení registrace bezpečnostních údajů](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. V **části Umístění podmínek** > **Locations**nakonfigurujte následující možnosti:
   1. Konfigurovat **ano**
   1. Zahrnout **libovolné místo**
   1. Vyloučit **všechna důvěryhodná umístění**
1. V okně *Umístění* vyberte **Hotovo** a pak v okně *Podmínky* vyberte **Hotovo.**
1. V části **Access controls** > **Grant**zvolte **Block access**, pak **Vyberte**
1. Nastavit **zásadu povolit** na **zapnuto**
1. Chcete-li zásadu dokončit, vyberte **Vytvořit**

## <a name="next-steps"></a>Další kroky

Pokud potřebujete pomoc, přečtěte si, jak [řešit potíže s kombinovanou registrací bezpečnostních údajů](howto-registration-mfa-sspr-combined-troubleshoot.md) nebo se dozvíte, jaká je podmínka umístění v [podmíněném přístupu služby Azure Active Directory?](../conditional-access/location-condition.md)

Pokud chcete povolit funkce ve vašem tenantovi Azure AD, najdete v kurzech, které [umožňují samoobslužné resetování hesla](tutorial-enable-sspr.md) a [povolují azure multifaktorové ověřování](tutorial-enable-azure-mfa.md).

Zjistěte, jak [povolit kombinovanou registraci ve vašem tenantovi](howto-registration-mfa-sspr-combined.md) nebo [vynutit uživatelům znovu zaregistrovat metody ověřování](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

Můžete také zkontrolovat [dostupné metody pro Azure Multi-Factor Authentication a SSPR](concept-authentication-methods.md).
