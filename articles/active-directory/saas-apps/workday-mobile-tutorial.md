---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s mobilní aplikací v Workday | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a mobilní aplikací v Workday.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: 256da169761da486d8ac064a2f58a59be43bb5df
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754706"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s mobilní aplikací v Workday

V tomto kurzu se naučíte, jak integrovat Azure Active Directory (Azure AD), podmíněný přístup a Intune s Mobile Apps pracovního dne. Když integrujete Mobile Apps Workday u Microsoftu, můžete:

* Před přihlášením ověřte, že zařízení vyhovují vašim zásadám.
* Přidejte ovládací prvky do aplikace v Workday, abyste uživatelům zajistili zabezpečený přístup k podnikovým datům. 
* Řízení ve službě Azure AD, která má přístup k Workday.
* Umožněte uživatelům, aby se do Workday přihlásili pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Integrace Workday s Azure AD
* Kurz: [Azure Active Directory integraci jednotného přihlašování (SSO) k Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete zásady podmíněného přístupu od Microsoftu a Intune s mobilními aplikacemi v Workday.

* Pro povolení jednotného přihlašování se teď dá pro službu Azure AD nakonfigurovat federované aplikace Workday. Další podrobnosti o tom, jak nakonfigurovat, najdete na [tomto](workday-tutorial.md) odkazu.

> [!NOTE] 
> Pracovní den nepodporuje zásady ochrany aplikací Intune. K využití podmíněného přístupu musíte použít správu mobilních zařízení.


## <a name="ensure-users-have-access-to-the-workday-mobile-app"></a>Ujistěte se, že uživatelé mají přístup k mobilní aplikaci pro pracovní den:

Nakonfigurujte Workday tak, aby povoloval přístup ke svým nabídkám mobilních aplikací. Pro mobilní zařízení budete muset nakonfigurovat tyto zásady:

Můžete je nakonfigurovat pomocí následujících pokynů:

1. Přihlaste se k zásadám zabezpečení domény pro funkční oblast.
2. Vyberte zásady zabezpečení.
    * Mobilní využití – Android
    * Mobilní využití – iPad
    * Mobilní použití – iPhone
3. Klikněte na Upravit oprávnění.
4. Zaškrtnutím políčka Zobrazit nebo upravit udělíte skupinám zabezpečení přístup k položkám, které jsou zabezpečeny pomocí sestavy nebo úlohy.
5. Zaškrtnutím políčka získat nebo vložit udělíte skupinám zabezpečení přístup k akcím integrace a sestavování a sestavování úloh.

Aktivujte nedokončené změny zásad zabezpečení spuštěním úlohy **aktivovat změny zásad zabezpečení na** úkolu.

## <a name="open-workday-login-page-in-mobile-browser"></a>Otevřít přihlašovací stránku Workday v mobilním prohlížeči:

Aby bylo možné použít podmíněný přístup k mobilní aplikaci v Workday, je nutné, aby se aplikace otevřela v externím prohlížeči. To se dá udělat zaškrtnutím políčka **Povolit pro nativní aplikace jednotné přihlašování v mobilním prohlížeči** v části **Upravit nastavení klienta – zabezpečení.** To bude vyžadovat, aby byl v zařízení nainstalovaný prohlížeč schválené Intune pro iOS a v pracovním profilu pro Android.

![Přihlášení k mobilnímu prohlížeči](./media/workday-tutorial/mobile-browser.png)

## <a name="setup-conditional-access-policy"></a>Nastavit zásady podmíněného přístupu:

Tato zásada ovlivní jenom přihlášení do zařízení se systémem iOS nebo Android. Pokud ji chcete zvětšit na všechny platformy, stačí vybrat **libovolné zařízení.** Tato zásada bude vyžadovat, aby zařízení vyhovovalo zásadám a ověřilo ho prostřednictvím Microsoft Intune. Vzhledem k tomu, že Android má pracovní profily, by se mělo blokovat všem uživatelům, aby se přihlásili do Workday (web nebo aplikace), pokud se nepřihlašují přes svůj pracovní profil a nainstalují aplikaci prostřednictvím Portál společnosti Intune. Pro iOS je k dispozici ještě jeden další krok, aby se zajistilo, že bude platit stejná situace. Tady jsou některé snímky obrazovky instalace podmíněného přístupu.

**Workday podporuje následující řízení přístupu:**
* Vyžadovat Multi-Factor Authentication
* Vyžadovat, aby zařízení bylo označené jako vyhovující

**Aplikace Workday nepodporuje následující:**
* Vyžadovat klientskou aplikaci schválenou
* Vyžadovat zásady ochrany aplikací (Preview)

Pokud chcete nastavit pracovní **den** jako **spravované zařízení** , proveďte následující kroky:

![Nastavení zásad podmíněného přístupu](./media/workday-tutorial/managed-devices-only.png)

1. Klikněte na **domovská > Microsoft Intune > podmíněná Access-Policies > jenom spravovaná zařízení** . 

1. Na stránce **jenom spravovaná zařízení** zadejte jako hodnotu pole **název** `Managed Devices Only` a klikněte na **cloudové aplikace nebo akce** .

1. V **cloudových aplikacích nebo akcích** proveďte následující kroky.

    a. Přepínač **Vyberte, na co se tato zásada vztahuje jako na** **cloudové aplikace** .

    b. V případě zahrnutí klikněte na **vybrat aplikace** .

    c. V seznamu Select zvolte **Workday** .

    d. Klikněte na **Hotovo** .

1. Zapněte **zásadu Povolit** .

1. Klikněte na **Uložit** .

Pro **udělení** přístupu proveďte následující kroky:

![Nastavení zásad podmíněného přístupu v Workday](./media/workday-tutorial/managed-devices-only-2.png)

1. Klikněte na **domovská > Microsoft Intune > podmíněná Access-Policies > jenom spravovaná zařízení** . 

1. Na stránce **jenom spravovaná zařízení** zadejte jako hodnotu pole **název** `Managed Devices Only` a klikněte na **řízení přístupu > udělení** .

1. Na stránce **udělení** proveďte následující kroky.

    a. Vyberte ovládací prvky, které se mají vykonat jako **udělení přístupu** .

    b. Zaškrtněte políčko **vyžadovat, aby zařízení bylo označené jako vyhovující** .

    c. Vyberte **vyžadovat jeden z vybraných ovládacích prvků** .

    d. Klikněte na **Vybrat** .

1. Zapněte **zásadu Povolit** .

1. Klikněte na **Uložit** .

## <a name="set-up-device-compliance-policy"></a>Nastavení zásad dodržování předpisů pro zařízení:

Abyste zajistili, že se zařízení se systémem iOS můžou přihlásit jenom prostřednictvím aplikace pro správu mobilních zařízení (MDM), musíte aplikaci App Storu zablokovat přidáním **com. Workday. workdayapp** do seznamu omezených aplikací. Tím se zajistí, že budou mít přístup k Workday jenom zařízení, která mají aplikaci Workday nainstalovanou přes portál společnosti. V případě prohlížeče budou mít přístup k Workday jenom v případě, že je zařízení spravované pomocí Intune a používá spravovaný prohlížeč.

![Zásady dodržování předpisů zařízením v nastavení pracovního dne](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-microsoft-intune-app-configuration-policies"></a>Nastavení Microsoft Intune zásad konfigurace aplikací:

| Scénář | Páry klíč-hodnota |
|----------------------------------------------------------------------------------------   |-----------|
| Automaticky vyplnit pole klient a webová adresa pro:<br>● Workday v Androidu, když povolíte profily Android for Work.<br>● Workday na iPadu a iPhonu.     | Pomocí těchto hodnot můžete nakonfigurovat tenanta: <br>Konfigurační klíč ● = UserGroupCode<br>Typ hodnoty ● = řetězec <br>Hodnota konfigurace ● = název tenanta. Příklad: GMS<br>Pomocí těchto hodnot nakonfigurujte svou webovou adresu:<br>Konfigurační klíč ● = AppServiceHost<br>Typ hodnoty ● = řetězec<br>Hodnota konfigurace ● = základní adresa URL vašeho tenanta. Příklad: https://www.myworkday.com                              |   |
| Zakázat tyto akce pro Workday na iPadu a iPhonu:<br>Vyjmutí, zkopírování a vložení ●<br>● Tisk                       | Nastavením hodnoty (Boolean) na hodnotu false u těchto klíčů zakažte funkci:<br>● AllowCutCopyPaste<br>● AllowPrint  |
| Zakáže snímky obrazovky pro Workday v Androidu. |Chcete-li zakázat funkce, nastavte hodnotu (logická hodnota) na hodnotu false u klíče AllowScreenshots.|
| Zakažte navrhované aktualizace pro vaše uživatele.|Chcete-li zakázat funkce, nastavte hodnotu (logická hodnota) na hodnotu false u klíče AllowSuggestedUpdates.|
|Přizpůsobte adresu URL obchodu s aplikacemi pro přímé mobilní uživatele do obchodu s aplikacemi podle vašeho výběru.|Pomocí těchto hodnot můžete změnit adresu URL obchodu s aplikacemi:<br>Konfigurační klíč ● = AppUpdateURL<br>Typ hodnoty ● = řetězec<br> Hodnota konfigurace ● = adresa URL obchodu App Store|
|       |


## <a name="ios-configuration-policies"></a>Zásady konfigurace pro iOS:

1. Přejít na https://portal.azure.com/ a přihlásit se
2. Vyhledejte **Intune** nebo klikněte na widget ze seznamu.
3. Přejít na **klientské aplikace – > aplikací – zásady konfigurace > aplikací – > a spravovaná zařízení s > přidat**
4. Zadejte název.
5. V části **platforma** vyberte **iOS/iPadOS** .
6. V části **přidružená aplikace** vyberte aplikaci v Workday pro iOS, kterou jste přidali.
7. Klikněte na **nastavení konfigurace** a v části **formát nastavení konfigurace** vyberte **zadat data XML** .
8. Zde je příklad souboru XML. Přidejte konfigurace, které byste chtěli použít. Nahraďte **STRING_VALUE** řetězcem, který chcete použít. Nahraďte `<true />` nebo `<false />` s `<true />` nebo  `<false />` . Pokud konfiguraci nepřidáte, bude fungovat podobně jako v případě, že byla nastavena na hodnotu true.

    ```
    <dict>
    <key>UserGroupCode</key>
    <string>STRING_VALUE</string>
    <key>AppServiceHost</key>
    <string>STRING_VALUE</string>
    <key>AllowCutCopyPaste</key>
    <true /> or <false />
    <key>AllowPrint</key>
    <true /> or <false />
    <key>AllowSuggestedUpdates</key>
    <true /> or <false />
    <key>AppUpdateURL</key>
    <string>STRING_VALUE</string>
    </dict>

    ```
9. Klikněte na tlačítko Přidat.
10. Aktualizujte stránku a klikněte na nově vytvořenou zásadu.
11. Klikněte na přiřazení a vyberte, na koho se má aplikace vztahovat.
12. Klikněte na Uložit.

## <a name="android-configuration-policies"></a>Zásady konfigurace pro Android:

1. Přejděte na adresu `https://portal.azure.com/` a přihlaste se.
2. Vyhledejte **Intune** nebo klikněte na widget ze seznamu.
3. Přejít na **klientské aplikace – > aplikací – zásady konfigurace > aplikací – > a spravovaná zařízení s > přidat**
5. Zadejte název. 
6. V části **platforma** vyberte **Android** .
7. V části **přidružená aplikace** vyberte aplikaci v Workday pro Android, kterou jste přidali.
8. Klikněte na **nastavení konfigurace** a v části **formát nastavení konfigurace** vyberte **zadat data JSON** .

