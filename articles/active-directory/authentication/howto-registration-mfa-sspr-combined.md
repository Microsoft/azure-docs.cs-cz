---
title: Začínáme s kombinovanou registrací – Azure Active Directory
description: Povolit kombinované Multi-Factor Authentication služby Azure AD a registraci samoobslužného resetování hesla (Preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2bd3f61ffc07881ed8e502788b11fc0f435735b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847350"
---
# <a name="enable-combined-security-information-registration-preview"></a>Povolit registraci kombinovaných zabezpečení informací (Preview)

Než začnete s novým prostředím, přečtěte si článek [Registrace informací o zabezpečení (Preview)](concept-registration-mfa-sspr-combined.md) a ujistěte se, že rozumíte funkcím a účinkům této funkce.

![Rozšířené možnosti registrace informací o zabezpečení](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Souhrnná registrace informací o zabezpečení pro Azure Multi-Factor Authentication a Azure Active Directory (Azure AD) Samoobslužné resetování hesla je funkce Public Preview služby Azure AD. Další informace o verzích Preview najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!NOTE]
> Organizace, které povolily předchozí verzi Preview pro registraci a správu bezpečnostních údajů, by měly dokončit níže uvedené kroky, aby bylo možné používat vylepšené prostředí Preview. Pro organizace, které tento přepínač nedělají, od 8. října 2019 bude Microsoft přepínat uživatele předchozí verze Preview, aby mohli registrovat a spravovat informace o zabezpečení v rozšířeném prostředí. 
> 
> Pokud jste nepovolili žádnou verzi Preview, nebude mít vliv na vaši organizaci.

## <a name="enable-combined-registration"></a>Povolení kombinované registrace

Pokud chcete povolit kombinovanou registraci, proveďte tyto kroky:

1. Přihlaste se k Azure Portal jako správce uživatele nebo globální správce.
2. Přejděte na **Azure Active Directory** > **nastavení uživatele** > **Spravovat nastavení pro přístupové panely funkce verze Preview**.
3. V části **Uživatelé můžou k registraci a správě informací o zabezpečení používat funkce verze Preview – aktualizovat**, zvolit povolit pro **vybranou** skupinu uživatelů nebo pro **všechny** uživatele.

   ![Povolit prostředí Preview pro všechny uživatele v kombinaci s informacemi o zabezpečení](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> Od března 2019 nebudou možnosti telefonního hovoru k dispozici pro Multi-Factor Authentication a SSPR uživatele v bezplatných nebo zkušebních klientech Azure AD. Tato změna nemá vliv na zprávy SMS. Možnosti telefonního hovoru budou pořád dostupné uživatelům v placených klientech Azure AD.

> [!NOTE]
> Po povolení kombinované registrace si uživatelé, kteří si zaregistrují nebo potvrdí jejich telefonní číslo nebo mobilní aplikaci prostřednictvím nového prostředí, můžou použít pro Multi-Factor Authentication a SSPR, pokud jsou tyto metody povolené v Multi-Factor Authentication a SSPR. konfigurovaný. Pokud pak toto prostředí zakážete, budou se uživatelé, kteří se na `https://aka.ms/ssprsetup` na předchozí registrační stránce SSPR, před tím, než budou moct získat přístup k této stránce, provádět ověřování Multi-Factor Authentication.

Pokud jste v Internet Exploreru nakonfigurovali seznam přiřazení lokality k zóně, musí být tyto lokality ve stejné zóně:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Zásady podmíněného přístupu pro kombinovanou registraci

Zabezpečení, kdy a jak se uživatelé registrují pro Azure Multi-Factor Authentication a Samoobslužné resetování hesla, je teď možné u uživatelských akcí v zásadách podmíněného přístupu. Tato funkce ve verzi Preview je dostupná pro organizace, u kterých je povolená [Kombinovaná registrace ve verzi Preview](../authentication/concept-registration-mfa-sspr-combined.md). Tato funkce může být povolená v organizacích, kde se chtějí uživatelé zaregistrovat pro Azure Multi-Factor Authentication a SSPR z centrálního umístění, jako je důvěryhodné síťové umístění při registraci lidských zdrojů. Další informace o vytváření důvěryhodných umístění v podmíněném přístupu najdete v článku [co je podmínka umístění v Azure Active Directory podmíněný přístup?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Vytvoření zásady, která vyžaduje registraci z důvěryhodného umístění

Následující zásady platí pro všechny vybrané uživatele, kteří se pokoušejí zaregistrovat pomocí kombinovaného prostředí pro registraci, a zablokují přístup, pokud se nepřipojují z umístění označeného jako důvěryhodná síť.

![Vytvoření zásady certifikační autority pro řízení registrace bezpečnostních údajů](media/howto-registration-mfa-sspr-combined/conditional-access-register-security-info.png)

1. V **Azure Portal**přejděte do **Azure Active Directory** > **podmíněný přístup** .
1. Vyberte **Nová zásada**.
1. Do název zadejte název pro tuto zásadu. Například **Kombinovaná registrace informací o zabezpečení v důvěryhodných sítích**
1. V části **přiřazení**klikněte na **Uživatelé a skupiny**a vyberte uživatele a skupiny, pro které chcete tuto zásadu použít.

   > [!WARNING]
   > Uživatelé musí mít povolený [Náhled pro kombinovanou registraci](../authentication/howto-registration-mfa-sspr-combined.md).

1. V části **cloudové aplikace nebo akce**vyberte **akce uživatele**, zaškrtněte políčko **zaregistrovat informace o zabezpečení (Preview)** .
1. V části **podmínky** > **umístění**
   1. Konfigurovat **Ano**
   1. Zahrnout **libovolné umístění**
   1. Vyloučit **všechna důvěryhodná umístění**
   1. V okně umístění klikněte na **Hotovo** .
   1. V okně podmínky klikněte na **Hotovo** .
1. V části **řízení přístupu** > **udělení**
   1. Klikněte na **blokovat přístup** .
   1. Pak klikněte na **Vybrat** .
1. Nastavte **Povolit zásadu** na **zapnuto** .
1. Pak klikněte na **vytvořit** .

## <a name="next-steps"></a>Další kroky

[Vynutí uživatele, aby znovu zaregistrovali metody ověřování.](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[Dostupné metody pro Multi-Factor Authentication a SSPR](concept-authentication-methods.md)

[Konfigurace samoobslužného resetování hesla](howto-sspr-deployment.md)

[Konfigurace Azure Multi-Factor Authentication](howto-mfa-getstarted.md)

[Řešení potíží s kombinovanou registrací informací o zabezpečení](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Jaká je podmínka umístění v Azure Active Directory podmíněný přístup?](../conditional-access/location-condition.md)
