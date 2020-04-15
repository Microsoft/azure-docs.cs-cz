---
title: Začínáme s kombinovanou registrací – Azure Active Directory
description: Povolení kombinovaného vícefaktorového ověřování Azure AD a samoobslužné registrace resetování hesla (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e22871114f260417e8a1514546c88ec4541064a4
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309751"
---
# <a name="enable-combined-security-information-registration-preview"></a>Povolit kombinovanou registraci bezpečnostních informací (preview)

Před povolením nového prostředí si přečtěte článek [Kombinovaná registrace informací o zabezpečení (náhled)](concept-registration-mfa-sspr-combined.md) vám zajistí, že porozumíte funkcím a účinkům této funkce.

![Rozšířené možnosti kombinované registrace bezpečnostních informací](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Kombinovaná registrace informací o zabezpečení pro azure multi-factor authentication a samoobslužné resetování hesla Azure Active Directory (Azure AD) je veřejná funkce preview Azure AD. Další informace o verzích Preview najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!NOTE]
> Organizace, které povolily předchozí verzi preview pro registraci a správu bezpečnostních údajů, by měly provést následující kroky, aby bylo možné rozšířit možnosti náhledu. pro organizace, které neprovedou přepínač, 8. 
> 
> Pokud jste nepovolili žádnou verzi náhledu, nebude to mít vliv na vaši organizaci.

## <a name="enable-combined-registration"></a>Povolení kombinované registrace

Chcete-li povolit kombinovanou registraci, proveďte tyto kroky:

1. Přihlaste se k portálu Azure jako správce uživatelů nebo globální správce.
2. Přejděte na**nastavení** > uživatele **služby Azure Active Directory** > **Správa nastavení náhledu uživatelských funkcí**.
3. V části **Uživatelé mohou používat funkce náhledu pro registraci a správu bezpečnostních údajů**, zvolte povolení pro **vybranou** skupinu uživatelů nebo pro **všechny** uživatele.

   ![Povolení kombinovaného náhledu informací o zabezpečení pro všechny uživatele](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!IMPORTANT]
> Od března 2019 nebudou možnosti telefonního hovoru dostupné uživatelům vícefaktorového ověřování a samoobslužné samoobslužné volání do provozu ve volných a zkušebních klientech Azure AD. Sms zprávy nejsou touto změnou ovlivněny. Možnosti telefonního hovoru budou stále dostupné uživatelům v placených klientech Azure AD.

> [!NOTE]
> Po povolení kombinované registrace mohou uživatelé, kteří zaregistrují nebo potvrdí své telefonní číslo nebo mobilní aplikaci prostřednictvím nového prostředí, použít pro vícefaktorové ověřování a samoobslužné ověřování, pokud jsou tyto metody povoleny v zásadách vícefaktorového ověřování a samoobslužného tisku. Pokud pak zakázat toto prostředí, uživatelé, kteří `https://aka.ms/ssprsetup` přejdou na předchozí stránku registrace samoobslužného práva k obsluze bude muset provést vícefaktorové ověřování před tím, než budou mít přístup ke stránce.

Pokud jste v aplikaci Internet Explorer nakonfigurovali seznam přiřazení webu na zónu, musí být ve stejné zóně následující weby:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Zásady podmíněného přístupu pro kombinovanou registraci

Zabezpečení, kdy a jak se uživatelé registrují pro azure vícefaktorové ověřování a samoobslužné resetování hesla je teď možné s akcemi uživatelů v zásadách podmíněného přístupu. Tato funkce náhledu je k dispozici organizacím, které povolily [kombinovaný náhled registrace](../authentication/concept-registration-mfa-sspr-combined.md). Tato funkce může být povolena v organizacích, kde chtějí, aby se uživatelé registrovali pro Azure Multi-Factor Authentication a SSPR z centrálního umístění, jako je důvěryhodné umístění v síti během registrace hr. Další informace o vytváření důvěryhodných umístění v podmíněném přístupu najdete v článku [Jaká je podmínka umístění v podmíněném přístupu služby Azure Active Directory?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Vytvoření zásady, která vyžaduje registraci z důvěryhodného umístění

Následující zásady platí pro všechny vybrané uživatele, kteří se pokoušejí zaregistrovat pomocí kombinovaného registračního prostředí, a zablokují přístup, pokud se nepřipojují z umístění označeného jako důvěryhodná síť.

![Vytvoření zásady certifikační autority pro řízení registrace bezpečnostních údajů](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Na **webu Azure Portal**přejděte na Azure **Active Directory** > **Security** > **Conditional Access** .
1. Vyberte **Nová zásada**.
1. V pojmenujete název této zásady. Například **kombinovaná registrace bezpečnostních informací v důvěryhodných sítích**
1. V části **Přiřazení**klikněte na **Uživatelé a skupiny**a vyberte uživatele a skupiny, na které se má tato zásada vztahovat.

   > [!WARNING]
   > Uživatelé musí být povolena pro [kombinovanou registraci náhled](../authentication/howto-registration-mfa-sspr-combined.md).

1. V části **Cloudové aplikace nebo akce**vyberte **Akce uživatele**, **zaškrtněte políčko Registrovat informace o zabezpečení (preview).**
1. V **podmínkách** > **Umístění**
   1. Konfigurovat **ano**
   1. Zahrnout **libovolné místo**
   1. Vyloučit **všechna důvěryhodná umístění**
   1. Klikněte na **tlačítko Hotovo** v okně Umístění
   1. Klikněte na **tlačítko Hotovo** na okně Podmínky
1. V části **Udělení ovládacích prvků** > **přístupu**
   1. Klikněte na **Blokovat přístup.**
   1. Pak klikněte na **Vybrat**
1. Nastavit **zásadu povolit** na **zapnuto**
1. Pak klikněte na **Vytvořit**

## <a name="next-steps"></a>Další kroky

[Vynucení opětovné registrace metod ověřování uživatelem](howto-mfa-userdevicesettings.md#manage-user-authentication-options)

[Dostupné metody pro vícefaktorové ověřování a samodotykové ověřování](concept-authentication-methods.md)

[Konfigurace samoobslužného resetování hesla](howto-sspr-deployment.md)

[Konfigurace Azure Multi-Factor Authentication](howto-mfa-getstarted.md)

[Poradce při potížích s kombinovanou registrací bezpečnostních údajů](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Jaká je podmínka umístění v podmíněném přístupu služby Azure Active Directory?](../conditional-access/location-condition.md)
