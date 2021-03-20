---
title: Import hesel do aplikace Microsoft Authenticator – Azure AD
description: Jak importovat hesla do aplikace Microsoftu pro ověřování z oblíbených správců hesel.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/28/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: ecc6580148dfba92077336a26ff9160fbe88eb2c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99806151"
---
# <a name="import-passwords-into-the-microsoft-authenticator-app"></a>Import hesel do aplikace Microsoft Authenticator

Microsoft Authenticator podporuje import hesel z Google Chrome, Firefox, LastPass, Bitwarden a RoboForm. Pokud Microsoft v současné době nepodporuje stávající správce hesel, můžete [přihlašovací údaje ručně zadat do šablony CSV](https://go.microsoft.com/fwlink/?linkid=2134938). Pokud chcete importovat stávající hesla a spravovat je v ověřovací aplikaci, stačí exportovat hesla ze stávajícího správce hesel do našeho formátu hodnot oddělených čárkami (CSV). Pak naimportujte exportovaný soubor CSV do ověřovatele v našem rozšíření prohlížeče Chrome nebo přímo do aplikace ověřovatel (Android a iOS).

## <a name="import-from-google-chrome-or-android-smart-lock"></a>Import z Google Chrome nebo Android Smart Lock

Hesla můžete importovat z Google Chrome nebo z Androidu Smart Lock do ověřovatele na svém počítači smartphone nebo stolním počítači. Další možnosti:

- [Import z Chrome v Androidu a iOS](#import-from-chrome-on-android-and-ios)
- [Import z desktopového prohlížeče Chrome](#import-from-chrome-desktop-browser)

### <a name="import-from-chrome-on-android-and-ios"></a>Import z Chrome v Androidu a iOS

Uživatelé Google Chrome na telefonech s Androidem a Apple můžou svoje hesla naimportovat přímo z telefonu. stačí pár jednoduchých kroků.

1. Nainstalujte ověřovací aplikaci do telefonu a otevřete kartu **hesla** .

1. Přihlaste se k Google Chrome na telefonu.

1. Klepněte na ![ nabídku se třemi tečkami Google ](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png) v pravém horním rohu pro telefony s Androidem nebo v pravém dolním rohu pro zařízení s iOS a potom klepněte na **nastavení.**

   Platforma | Odkaz
   ---------- | --------
   Android | ![Umístění nabídky nastavení Google Chrome](./media/user-help-authenticator-app-import-passwords/android-settings-menu.png)
   iOS | ![Ikona nabídky nastavení Google Chrome](./media/user-help-authenticator-app-import-passwords/apple-settings-menu.png)

1. V **Nastavení** otevřete **hesla**.

   Platforma | Odkaz
   ---------- | --------
   Android | ![Umístění příkazu pro hesla andoid Chrome](./media/user-help-authenticator-app-import-passwords/android-passwords-location.png)
   iOS | ![Heslo pro Apple Chrome – umístění příkazu](./media/user-help-authenticator-app-import-passwords/apple-passwords-location.png)

1. V zařízeních s Androidem klepněte na ![ nabídku se třemi tečkami Google ](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png) v pravém horním rohu pro telefony s Androidem nebo dole vpravo pro zařízení s iOS a potom klepněte na **exportovat hesla**.

   Platforma | Odkaz
   ---------- | --------
   Android | ![Umístění hesel pro export pro Android Chrome](./media/user-help-authenticator-app-import-passwords/android-export-passwords-location.png)
   iOS | ![Umístění pro export hesel Apple Chrome](./media/user-help-authenticator-app-import-passwords/apple-export-passwords-location.png)

   Musíte zadat PIN, otisk prstu nebo rozpoznávání obličeje. Potvrďte svoji identitu a začněte export klepnutím na **exportovat hesla** znovu.

1. Po exportu hesel se v Chrome zobrazí výzva k výběru aplikace, do které provádíte import. Pokud chcete začít importovat hesla, vyberte **ověřovací data** . Až to bude hotové, budete informováni o stavu importu.

   Platforma | Odkaz
   ---------- | --------
   Android | ![Umístění pro import hesel pro Android Chrome](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
   iOS | ![Umístění pro import hesel Apple Chrome](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

### <a name="import-from-chrome-desktop-browser"></a>Import z desktopového prohlížeče Chrome

Než začnete, musíte nainstalovat a přihlásit se k [rozšíření automatického vyplňování Microsoft](https://chrome.google.com/webstore/detail/microsoft-autofill/fiedbfgcleddlbcmgdigjgdfcggjcion) v prohlížeči Chrome.

1. Otevřete [správce hesel Google](https://passwords.google.com) v jakémkoli prohlížeči. Pokud jste to ještě neudělali, přihlaste se ke svému účtu Google.

1. Vyberte ikonu ozubeného kolečka. ![Ikona ozubeného kolečka pro správce hesel](./media/user-help-authenticator-app-import-passwords/desktop-password-manager-gear.png) a otevřete stránku nastavení hesla.

1. Vyberte **exportovat** a potom na další stránce znovu vyberte **exportovat** , abyste mohli začít exportovat hesla. Po zobrazení výzvy k potvrzení vaší identity zadejte heslo Google. Až to bude hotové, budete informováni o stavu importu.

   ![Umístění příkazu pro export hesel v prohlížeči Chrome Desktop](./media/user-help-authenticator-app-import-passwords/desktop-chrome-export-passwords-location.png)

1. Otevřete rozšíření Chrome pro automatické vyplňování a vyberte **Nastavení**.

   ![Umístění nastavení rozšíření automatického vyplňování prohlížeče Chrome desktopu](./media/user-help-authenticator-app-import-passwords/desktop-chrome-autofill-settings.png)

1. Výběrem **importovat data** otevřete dialogové okno. Pak vyberte **zvolit soubor** a vyhledejte a IMPORTUJTE soubor CSV.

   ![Prohlížeč Chrome desktopu import dat umístění sdíleného svazku clusteru](./media/user-help-authenticator-app-import-passwords/desktop-chrome-import-csv.png)

## <a name="import-from-firefox"></a>Import z prohlížeče Firefox

Firefox umožňuje exportovat hesla jenom z desktopového prohlížeče, takže před importem hesel z Firefoxu se ujistěte, že máte přístup k prohlížeči desktopového prohlížeče Firefox.

1. Přihlaste se k nejnovější verzi prohlížeče Firefox na ploše a vyberte ![Nabídka Firefox "hamburgerovou" "](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) v nabídce vpravo nahoře na obrazovce.

1. Vyberte **přihlašovací jména a hesla**.

   ![Přihlašovací údaje a umístění hesel v prohlížeči Desktop Firefox](./media/user-help-authenticator-app-import-passwords/desktop-firefox-passwords-location.png)

1. Na stránce Firefox Lockwise vyberte ![ nabídku se třemi tečkami Firefox ](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) , vyberte **exportovat přihlášení** a potvrďte svůj záměr výběrem možnosti **exportovat**. Zobrazí se výzva k identifikaci sami zadáním kódu PIN, hesla zařízení nebo skenováním otisků prstů. Po úspěšné identifikaci bude Firefox exportovat vaše hesla ve formátu CSV do vybraného umístění.

   ![Desktop Firefox – export hesel – umístění v prohlížeči](./media/user-help-authenticator-app-import-passwords/desktop-firefox-export-passwords-location.png)

1. Hesla můžete importovat do ověřovatele z desktopového prohlížeče nebo na telefonech s iOS nebo Androidem. Import do aplikace ověřovatele na telefonu:

      1. Exportovaný soubor CSV můžete přenést na telefon s Androidem nebo iOS pomocí preferovaného a bezpečného způsobu a pak ho stáhnout. Potom nasdílejte soubor CSV s aplikací Authenticator a spusťte import.

         Platforma | Odkaz
         ---------- | --------
         Android | ![Umístění pro import hesel pro Android Chrome](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Umístění pro import hesel Apple Chrome](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Po úspěšném importování hesla do ověřovatele odstraňte soubor CSV z počítače nebo mobilního telefonu.

## <a name="import-from-lastpass"></a>Import z LastPass

LastPass podporuje export hesel jenom z desktopového prohlížeče, takže před zahájením importu hesel se ujistěte, že máte přístup k desktopovým prohlížečům.

1. Přihlaste se k webu [LastPass](https://lastpass.com) a vyberte **Upřesnit možnosti** a pak vyberte **exportovat**.

   ![Umístění pro export hesel desktopových LastPass](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-export-passwords-location.png)

1. Po zobrazení výzvy zadáním hlavního hesla se identifikujte. Potom uvidíte exportovaná hesla na webové stránce.

1. Zkopírujte obsah webové stránky.

1. Otevřete Poznámkový blok (nebo svůj oblíbený textový editor) a vložte zkopírovaný obsah.

1. Uložte tento soubor poznámkového bloku tak, že vyberete **soubor** &gt; **Uložit jako**. Zadejte název, který končí příponou. CSV (například LastPass.csv) v bezpečném umístění na ploše.

   ![Desktop LastPass uložit soubor CSV](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-save-import-file.png)

1. Hesla můžete importovat do ověřovatele v desktopovém prohlížeči nebo na telefonech s iOS nebo Androidem. Import do aplikace ověřovatele na telefonu:

      1. Přeneste exportovaný soubor CSV na telefon Smartphone pomocí preferovaného a bezpečného způsobu a pak ho stáhněte. Pak nasdílejte soubor CSV s ověřovací aplikací a spusťte import.

         Platforma | Odkaz
         ---------- | --------
         Android | ![Umístění pro import hesel pro Android LastPass](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Umístění pro import hesel Apple LastPass](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Po úspěšném importování hesla do ověřovatele odstraňte soubor CSV z počítače nebo mobilního telefonu.

## <a name="import-from-bitwarden"></a>Import z Bitwarden

Bitwarden podporuje export hesel jenom z desktopového prohlížeče, takže před zahájením importu hesel se ujistěte, že máte přístup k desktopovým prohlížečům.

1. Přihlaste https://vault.bitwarden.com/ se a vyberte **nástroje** &gt; **exportovat trezor**. Zvolte formát souboru ve formátu CSV, zadejte heslo k hlavnímu serveru a pak vyberte **exportovat trezor** a začněte exportovat.

   ![Umístění trezoru pro export Bitwarden](./media/user-help-authenticator-app-import-passwords/desktop-bitwarden-export-command-location.png)

1. Hesla můžete importovat do ověřovatele v desktopovém prohlížeči nebo na telefonech s iOS nebo Androidem. Import do aplikace ověřovatele na telefonu:

      1. Přeneste exportovaný soubor CSV na telefon Smartphone pomocí preferovaného a bezpečného způsobu a pak ho stáhněte. Pak nasdílejte soubor CSV s ověřovací aplikací a spusťte import.

         Platforma | Odkaz
         ---------- | --------
         Android | ![Umístění pro import hesel pro Android Bitwarden](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Umístění pro import hesel Apple Bitwarden](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Po úspěšném importování hesla do ověřovatele odstraňte soubor CSV z počítače nebo mobilního telefonu.

## <a name="import-from-roboform"></a>Import z RoboForm

RoboForm umožňuje exportovat hesla jenom z desktopové aplikace, takže před zahájením importu zajistěte, abyste měli přístup k aplikaci RoboForm na ploše.

1. Spusťte RoboForm z klienta desktopu a přihlaste se ke svému účtu.

1. V nabídce **RoboForm** vyberte **Možnosti** .

   ![Nabídka možností RoboForm pro stolní počítače](./media/user-help-authenticator-app-import-passwords/desktop-roboform-options.png)

1. Vyberte **účet &** &gt; **exportovat** data.

   ![Umístění příkazu pro export desktopových RoboForm](./media/user-help-authenticator-app-import-passwords/desktop-roboform-accounts-data.png)

1. Vyberte bezpečné umístění pro uložení exportovaného souboru. Jako **datový** typ vyberte **přihlášení** a jako formát vyberte soubor CSV a pak vyberte **exportovat**.

   ![Dialogové okno Export desktopových RoboForm](./media/user-help-authenticator-app-import-passwords/desktop-roboform-export-dialog.png)

1. Potvrďte svůj záměr a soubor CSV se pak vyexportuje do vybraného umístění.

   ![Dialogové okno pro potvrzení exportu desktopu RoboForm](./media/user-help-authenticator-app-import-passwords/desktop-roboform-confirmation.png)

1. Hesla můžete importovat do ověřovatele v desktopovém prohlížeči nebo na telefonech s iOS nebo Androidem. Import do aplikace ověřovatele na telefonu:

      1. Přeneste exportovaný soubor CSV na telefon Smartphone pomocí preferovaného a bezpečného způsobu a pak ho stáhněte. Pak nasdílejte soubor CSV s ověřovací aplikací a spusťte import.

         Platforma | Odkaz
         ---------- | --------
         Android | ![Umístění pro import hesel pro Android RoboForm](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Umístění pro import hesel Apple RoboForm](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Po úspěšném importování hesla do ověřovatele odstraňte soubor CSV z počítače nebo mobilního telefonu.

## <a name="import-by-creating-a-csv"></a>Import pomocí vytvoření sdíleného svazku clusteru

Pokud postup importu hesel ze Správce hesel není uvedený v tomto článku, můžete vytvořit sdílený svazek clusteru, který můžete použít k importu hesel do ověřovatele. Společnost Microsoft doporučuje, abyste při snadném formátování postupovali podle těchto kroků na ploše.

1. Na ploše [Stáhněte a otevřete naši šablonu importu](https://go.microsoft.com/fwlink/?linkid=2134938). Pokud jste uživatel Apple iPhone, Safari a řetězce klíčů, můžete teď přejít ke kroku 4.

1. Exportujte hesla ze stávajícího správce hesel v nešifrovaném souboru CSV.

1. Zkopírujte příslušné sloupce z exportovaného sdíleného svazku clusteru do souboru CSV šablony a pak ho uložte.

1. Pokud nemáte exportovaný sdílený svazek clusteru (CSV), můžete každé přihlášení zkopírovat ze stávajícího správce hesel do souboru CSV šablony. Neodstraňujte ani neměňte řádek záhlaví. Po dokončení ověřte integritu dat před zahájením dalšího kroku.

1. Hesla můžete importovat do ověřovatele v desktopovém prohlížeči nebo na telefonech s iOS nebo Androidem. Import do aplikace ověřovatele na telefonu:

      1. Přeneste exportovaný soubor CSV na telefon Smartphone pomocí preferovaného a bezpečného způsobu a pak ho stáhněte. Pak nasdílejte soubor CSV s ověřovací aplikací a spusťte import.

         Platforma | Odkaz
         ---------- | --------
         Android | ![Umístění pro import hesel ve formátu CSV pro Android](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Umístění pro import hesel Apple CSV](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Po úspěšném importování hesla do ověřovatele odstraňte soubor CSV z počítače nebo mobilního telefonu.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží

Nejběžnější příčinou neúspěšných importů je nesprávné formátování v souboru CSV. Chcete-li problém vyřešit, můžete vyzkoušet následující postup.

- V tomto článku zjistíte, jestli už v rámci vašeho aktuálního správce hesel podporuje import hesel. Pokud to máme, možná budete chtít import opakovat podle kroků uvedených pro příslušného poskytovatele.

- Pokud momentálně nepodporujeme import formátu vašeho správce hesel, můžete to zkusit znovu [vytvořením souboru CSV ručně](#import-by-creating-a-csv).

- Integritu dat sdíleného svazku clusteru můžete ověřit pomocí následujících návrhů:

  - První řádek musí obsahovat záhlaví se třemi sloupci: **Adresa URL**, **uživatelské jméno** a **heslo**.

  - Každý řádek musí obsahovat hodnotu ve sloupcích **Adresa URL** a **heslo** .

- Sdílený svazek clusteru můžete znovu vytvořit tak, že do [souboru šablony CSV](https://go.microsoft.com/fwlink/?linkid=2134938)vložíte svůj obsah.

- Pokud nic jiného nefunguje, nahlaste prosím svůj problém pomocí odkazu pro **odeslání zpětné vazby** z nastavení aplikace ověřovatele.
