---
title: Začínáme s kombinovaná registrace pro samoobslužné resetování HESLA Azure AD a Vícefaktorové ověřování (preview) – Azure Active Directory
description: Povolit kombinované ověřování Azure Multi-Factor Authentication AD a resetování hesla pomocí samoobslužné služby registrace (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: cafe81a1b5ab6d26e71eff05e355d2ed79c4d5e7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66119383"
---
# <a name="enable-combined-security-information-registration-preview"></a>Povolit kombinovat zabezpečení informace o registraci (preview)

Než povolíte nové prostředí, přečtěte si článek [registrační informace o zabezpečení (preview) v kombinaci](concept-registration-mfa-sspr-combined.md) zajistit pochopit funkce a efekty této funkce.

![Možnosti registrace rozšířené informace o kombinované zabezpečení](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Kombinované zabezpečení registrační informace pro ověřování Azure Multi-Factor Authentication a resetování hesla pomocí samoobslužné služby Azure Active Directory (Azure AD) je funkce ve verzi public preview služby Azure AD. Další informace o verzích Preview najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="enable-combined-registration"></a>Povolení kombinované registrace

Dokončete tyto kroky pro povolení kombinovaná registrace:

1. Přihlaste se k webu Azure portal jako globální správce nebo Správce uživatelů.
2. Přejděte na **Azure Active Directory** > **uživatelská nastavení** > **umožňuje spravovat nastavení přístupu funkce ve verzi preview panel**.
3. V části **uživatelé můžou používat funkce pro registraci a správu bezpečnostní údaje ve verzi preview – aktualizovat**, můžete povolit pro **vybrané** skupiny uživatelů nebo pro **všechny** uživatelů.

   ![Povolit prostředí ve verzi preview kombinované bezpečnostní údaje pro všechny uživatele](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> Od března 2019 se nebudou k dispozici pro Vícefaktorové ověřování a samoobslužné resetování HESLA uživatelů v tenantech bezplatné a zkušební verze Azure AD možnosti telefonního hovoru. Zprávy SMS, nejsou touto změnou ovlivněny. Možnosti telefonního hovoru bude stále k dispozici uživatelům v placené tenantů Azure AD.

> [!NOTE]
> Po povolení kombinovaná registrace, uživatelé, kteří registrace nebo potvrzení telefonního čísla nebo mobilních aplikací prostřednictvím nového prostředí můžete využít pro Vícefaktorové ověřování a samoobslužné resetování HESLA, pokud tyto metody jsou povolené Vícefaktorové ověřování a samoobslužné resetování HESLA zásady. Pokud zakážete pak toto prostředí, na stránce uživatelé přejít na předchozí registrace samoobslužného resetování HESLA `https://aka.ms/ssprsetup` budou muset provádět ověřování službou Multi-Factor Authentication před přístupem na stránce.

Pokud jste nakonfigurovali web k zóně Assignment List v Internet Exploreru, musí být ve stejné zóně následující weby:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Zásady podmíněného přístupu pro kombinované registrace

Zabezpečení a způsob registrace uživatele pro ověřování Azure Multi-Factor Authentication a samoobslužné resetování hesla je nyní možné s akcemi uživatelů v zásadách podmíněného přístupu. Tato funkce ve verzi preview je dostupné pro organizace, kteří mají povolené [kombinovat registraci ve verzi preview](../authentication/concept-registration-mfa-sspr-combined.md). Tato funkce může být povoleno v organizacích, kde chtějí uživatelé zaregistrovali k ověřování Azure Multi-Factor Authentication a samoobslužné resetování HESLA z centrálního umístění, jako je například umístění důvěryhodné sítě během připojování HR. Další informace o vytváření důvěryhodných umístění v podmíněný přístup, najdete v článku [co je podmínka umístění podmíněného přístupu Azure Active Directory?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Vytvořit zásadu, která vyžaduje registrace z důvěryhodného umístění

Tyto zásady platí pro všechny vybrané uživatele, kteří se pokusí zaregistrovat pomocí prostředí kombinovaná registrace a blokuje přístup, pokud se připojujete z místa označen jako důvěryhodné síti.

![Vytvoření zásad podmíněného přístupu pro řízení zabezpečení informací o registraci](media/howto-registration-mfa-sspr-combined/conditional-access-register-security-info.png)

1. V **webu Azure portal**, přejděte do **Azure Active Directory** > **podmíněného přístupu**
1. Vyberte **Nová zásada**.
1. Do pole Název zadejte název pro tuto zásadu. Například **kombinovat registrační informace o zabezpečení v důvěryhodných sítích**
1. V části **přiřazení**, klikněte na tlačítko **uživatelů a skupin**a vyberte uživatele a skupiny, které chcete tuto zásadu použít

   > [!WARNING]
   > Uživatelé musí být povolené pro [kombinovat registraci ve verzi preview](../authentication/howto-registration-mfa-sspr-combined.md).

1. V části **cloudové aplikace nebo akce**vyberte **akcí uživatele**, zkontrolujte **zaregistrovat informace o zabezpečení (preview)**
1. V části **podmínky** > **umístění**
   1. Konfigurace **Ano**
   1. Zahrnout **libovolného umístění**
   1. Vyloučit **všechna důvěryhodná umístění**
   1. Klikněte na tlačítko **provádí** v okně umístění
   1. Klikněte na tlačítko **provádí** v okně podmínky
1. V části **ovládací prvky přístupu** > **udělení**
   1. Klikněte na tlačítko **blokovat přístup**
   1. Pak klikněte na tlačítko **vyberte**
1. Nastavte **povolit zásady** k **na**
1. Pak klikněte na tlačítko **Create**

## <a name="next-steps"></a>Další postup

[Dostupné metody pro Vícefaktorové ověřování a samoobslužné resetování HESLA](concept-authentication-methods.md)

[Konfigurace samoobslužného resetování hesla](howto-sspr-deployment.md)

[Konfigurovat ověřování Azure Multi-Factor Authentication](howto-mfa-getstarted.md)

[Řešení potíží s kombinovat registrační informace o zabezpečení](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Co je podmínka umístění podmíněného přístupu Azure Active Directory?](../conditional-access/location-condition.md)
