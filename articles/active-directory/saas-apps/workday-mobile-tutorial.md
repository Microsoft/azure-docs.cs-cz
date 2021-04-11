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
ms.openlocfilehash: 94ab61a5c6335d43842ada48f17572c186af773c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077189"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s mobilní aplikací v Workday

V tomto kurzu se naučíte, jak integrovat Azure Active Directory (Azure AD), podmíněný přístup a Intune s mobilní aplikací v Workday. Při integraci mobilní aplikace v Workday s Microsoftem můžete:

* Před přihlášením ověřte, že zařízení vyhovují vašim zásadám.
* Přidejte ovládací prvky do mobilní aplikace Workday, abyste zajistili, že uživatelé budou mít zabezpečený přístup k podnikovým datům. 
* Řízení ve službě Azure AD, která má přístup k Workday.
* Umožněte uživatelům, aby se automaticky přihlásili k Workday pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

## <a name="prerequisites"></a>Požadavky

Jak začít:

* Integrujte Workday s Azure AD.
* Přečtěte si [Azure Active Directory integraci jednotného přihlašování s Workday](./workday-tutorial.md).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete zásady podmíněného přístupu Azure AD a Intune s mobilní aplikací v Workday.

Pokud chcete povolit jednotné přihlašování (SSO), můžete pro Azure AD nakonfigurovat federované aplikace Workday. Další informace najdete v tématu věnovaném [Azure Active Directory integraci jednotného přihlašování s Workday](./workday-tutorial.md).

> [!NOTE] 
> Pracovní den nepodporuje zásady ochrany aplikací Intune. K použití podmíněného přístupu musíte použít správu mobilních zařízení.


## <a name="ensure-users-have-access-to-workday-mobile-application"></a>Ujistěte se, že uživatelé mají přístup k mobilní aplikaci Workday

Nakonfigurujte Workday tak, aby povoloval přístup ke svým mobilním aplikacím. Pro pracovní den v/v je nutné nakonfigurovat následující zásady:

1. Přihlaste se k zásadám zabezpečení domény pro funkční oblast.
1. Vyberte příslušné zásady zabezpečení:
    * Mobilní využití – Android
    * Mobilní využití – iPad
    * Mobilní použití – iPhone
1. Vyberte **Upravit oprávnění**.
1. Zaškrtnutím políčka **Zobrazit nebo upravit** udělíte skupinám zabezpečení přístup k položkám, které jsou zabezpečeny pomocí sestavy nebo úlohy.
1. Zaškrtnutím políčka **získat nebo vložit** udělíte skupinám zabezpečení přístup k akcím integrace a sestavování a sestavování úloh.

Aktivujte nedokončené změny zásad zabezpečení spuštěním **aktivovat změny zásad zabezpečení, které čekají na vyřízení**.

## <a name="open-workday-sign-in-page-in-workday-mobile-browser"></a>Otevřít přihlašovací stránku pracovního dne v mobilním prohlížeči Workday

Pokud chcete použít podmíněný přístup k mobilní aplikaci Workday, musíte aplikaci otevřít v externím prohlížeči. V **Upravit nastavení klienta – zabezpečení** vyberte **Povolit pro nativní aplikace jednotné přihlašování v mobilním prohlížeči**. K tomu je potřeba, aby byl prohlížeč schválený službou Intune nainstalovaný v zařízení pro iOS a v pracovním profilu pro Android.

![Snímek obrazovky s přihlášením k mobilnímu prohlížeči v Workday](./media/workday-tutorial/mobile-browser.png)

## <a name="set-up-conditional-access-policy"></a>Nastavení zásad podmíněného přístupu

Tato zásada ovlivňuje jenom přihlášení na zařízení s iOS nebo Androidem. Pokud ho chcete zvětšit na všechny platformy, vyberte **libovolné zařízení**. Tato zásada vyžaduje, aby zařízení vyhovovalo zásadám a prověřit ho přes Intune. Vzhledem k tomu, že Android má pracovní profily, zablokuje se uživatelům přihlášení do Workday, pokud se přihlašuje přes svůj pracovní profil a nainstalují aplikaci prostřednictvím portálu společnosti Intune. Pro iOS je k dispozici ještě jeden další krok, aby se zajistilo, že se stejná situace týká.

Workday podporuje následující řízení přístupu:
* Vyžadovat vícefaktorové ověřování
* Vyžadovat, aby zařízení bylo označené jako vyhovující

Aplikace Workday nepodporuje následující:
* Vyžadovat klientskou aplikaci schválenou
* Vyžadovat zásady ochrany aplikací (Preview)

Pokud chcete nastavit Workday jako spravované zařízení, proveďte následující kroky:

![Snímek pouze pro spravovaná zařízení a cloudové aplikace nebo akce.](./media/workday-tutorial/managed-devices-only.png)

1. Vyberte možnost **Domovská**  >  **Microsoft Intune**  >  **podmíněný přístup – zásady**. Pak vyberte **jenom spravovaná zařízení**. 

1. **Jenom na spravovaných zařízeních** v části **název** vyberte **jenom spravovaná zařízení** a pak vyberte **cloudové aplikace nebo akce**.

1. V **cloudových aplikacích nebo akcích**:

    a. Přepínač **Vyberte, pro který tyto zásady platí** pro **cloudové aplikace**.

    b. V případě **zahrnutí** zvolte **možnost vybrat aplikace**.

    c. V seznamu **Vyberte** možnost pracovní **den**.

    d. Vyberte **Hotovo**.

1. Přepněte **možnost povolit zásadu** na **zapnuto**.

1. Vyberte **Uložit**.

Pro **udělení** přístupu proveďte následující kroky:

![Jenom zařízení pro správu a udělení oprávnění.](./media/workday-tutorial/managed-devices-only-2.png)

1. Vyberte možnost **Domovská**  >  **Microsoft Intune**  >  **podmíněný přístup – zásady**. Pak vyberte **jenom spravovaná zařízení**. 

1. Jenom na **spravovaných zařízeních** v části **název** vyberte **jenom spravovaná zařízení**. V části **Ovládací prvky přístupu** zvolte **Udělení**.

1. V **udělení**:

    a. Vyberte ovládací prvky, které se mají vykonat jako **udělení přístupu**.

    b. Vyberte **Vyžadovat, aby zařízení bylo označené jako vyhovující**.

    c. Vyberte **vyžadovat jeden z vybraných ovládacích prvků**.

    d. Zvolte **Vybrat**.

1. Přepněte **možnost povolit zásadu** na **zapnuto**.

1. Vyberte **Uložit**.

## <a name="set-up-device-compliance-policy"></a>Nastavení zásad dodržování předpisů pro zařízení

Abyste zajistili, že se zařízení s iOS můžou přihlásit jenom prostřednictvím správy mobilních zařízení, musíte aplikaci App Storu zablokovat tak, že do seznamu omezených aplikací přidáte **com. Workday. workdayapp** . Tím se zajistí, že budou mít přístup k Workday jenom zařízení, která mají nainstalovaný pracovní portál společnosti. Pro prohlížeč jsou zařízení přístupná jenom v případě, že je zařízení spravované pomocí Intune a používá spravovaný prohlížeč.

![Snímek obrazovky se zásadami dodržování předpisů pro zařízení s iOS](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-intune-app-configuration-policies"></a>Nastavení zásad konfigurace aplikací pro Intune

| Scenario | Páry klíč-hodnota |
|----------------------------------------------------------------------------------------   |-----------|
| Automaticky vyplnit pole klient a webová adresa pro:<br>● Workday v Androidu, když povolíte profily Android for Work.<br>● Workday na iPadu a iPhonu.     | Pomocí těchto hodnot můžete nakonfigurovat tenanta: <br>Konfigurační klíč ● = `UserGroupCode`<br>Typ hodnoty ● = řetězec <br>Hodnota konfigurace ● = název tenanta. Příklad: `gms`<br>Pomocí těchto hodnot nakonfigurujte svou webovou adresu:<br>Konfigurační klíč ● = `AppServiceHost`<br>Typ hodnoty ● = řetězec<br>Hodnota konfigurace ● = základní adresa URL vašeho tenanta. Příklad: `https://www.myworkday.com`                                | 
| Zakázat tyto akce pro Workday na iPadu a iPhonu:<br>Vyjmutí, zkopírování a vložení ●<br>● Tisk                       | Nastavením hodnoty (Boolean) na `False` tyto klíče zakažte funkci:<br>●   `AllowCutCopyPaste`<br>●    `AllowPrint`    |
| Zakáže snímky obrazovky pro Workday v Androidu. |Nastavením hodnoty (Boolean) na `False` `AllowScreenshots` klíč zakažte funkce.|
| Zakažte navrhované aktualizace pro vaše uživatele.|Nastavením hodnoty (Boolean) na `False` `AllowSuggestedUpdates` klíč zakažte funkce.|
|Přizpůsobte adresu URL obchodu s aplikacemi pro přímé mobilní uživatele do obchodu s aplikacemi podle vašeho výběru.|Pomocí těchto hodnot můžete změnit adresu URL obchodu s aplikacemi:<br>Konfigurační klíč ● = `AppUpdateURL`<br>Typ hodnoty ● = řetězec<br> Hodnota konfigurace ● = adresa URL obchodu App Store|

## <a name="ios-configuration-policies"></a>Zásady konfigurace pro iOS

1. Přejděte na web [Azure Portal](https://portal.azure.com/) a přihlaste se.
1. Vyhledejte **Intune** nebo vyberte widget ze seznamu.
1. Přejít na **klientská**  >  **aplikace**  >  **zásady konfigurace aplikace**. Pak vyberte **+ Přidat**  >  **spravovaná zařízení**.
1. Zadejte název.
1. V části **platforma** vyberte **iOS/iPadOS**.
1. V části **přidružená aplikace** vyberte aplikaci v Workday pro iOS, kterou jste přidali.
1. Vyberte **nastavení konfigurace**. V části **formát nastavení konfigurace** vyberte **zadat data XML**.
1. Zde je příklad souboru XML. Přidejte konfigurace, které chcete použít. Nahraďte `STRING_VALUE` řetězcem, který chcete použít. Nahraďte `<true /> or <false />` `<true />` nebo  `<false />` . Pokud konfiguraci nepřidáte, tento příklad funguje jako je nastaveno na `True` .

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
1. Vyberte **Přidat**.
1. Aktualizujte stránku a vyberte nově vytvořenou zásadu.
1. Vyberte **přiřazení** a zvolte, na koho se má aplikace vztahovat.
1. Vyberte **Uložit**.

## <a name="android-configuration-policies"></a>Zásady konfigurace pro Android

1. Přejděte na web [Azure Portal](https://portal.azure.com/) a přihlaste se.
2. Vyhledejte **Intune** nebo vyberte widget ze seznamu.
3. Přejít na **klientská**  >  **aplikace**  >  **zásady konfigurace aplikace**. Pak vyberte **+ Přidat**  >  **spravovaná zařízení**.
5. Zadejte název. 
6. V části **platforma** vyberte **Android**.
7. V části **přidružená aplikace** vyberte aplikaci v Workday pro Android, kterou jste přidali.
8. Vyberte **nastavení konfigurace**. V části **formát nastavení konfigurace** vyberte **zadat data JSON**.
