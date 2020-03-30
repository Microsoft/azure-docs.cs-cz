---
title: Microsoft Threat Modeling Tool vydání 9/12/2018
titleSuffix: Azure
description: Dokumentace poznámek k verzi pro nástroj modelování hrozeb
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/15/2019
ms.openlocfilehash: a921310f14f0d48922165a46d750366e170ee374
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269919"
---
# <a name="threat-modeling-tool-ga-release-71509112---9122018"></a>Nástroj pro modelování hrozeb GA verze 7.1.50911.2 - 9/12/2018

Jsme nadšeni, že oznamujeme, že nástroj Microsoft Threat Modeling Tool je nyní k dispozici ke stažení jako podporovaná obecně dostupná verze (GA). Tato verze obsahuje důležité aktualizace ochrany osobních údajů a zabezpečení, stejně jako opravy chyb, aktualizace funkcí a vylepšení stability. Stávající uživatelé verze 2017 Preview budou vyzváni k aktualizaci na nejnovější verzi pomocí technologie ClickOnce po otevření klienta. Pro nové uživatele nástroje si můžete [stáhnout klienta](https://aka.ms/threatmodelingtool).

S touto verzí ukončujeme podporu pro verzi 2017 Preview a doporučujeme všem uživatelům aktualizace náhledu na verzi GA. Října nebo po říjnu 15 2018 nastavíme minimální požadovanou verzi ClickOnce pro nástroj modelování hrozeb a všichni klienti náhledu budou muset upgradovat.

Nástroj Microsoft Threat Modeling Tool 2016, který je k dispozici na [webu Služby stažení softwaru společnosti Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=49168), zůstává podporován až do 1.

## <a name="feature-changes"></a>Změny funkcí

### <a name="azure-stencil-updates"></a>Aktualizace vzorníku Azure

Další vzorníky Azure a jejich související hrozby a skutečnosti snižující závažnost rizika byly přidány do odesílání sady vzorníků s touto verzí. Významné změny byly provedeny v prioritních oblastech "Azure App Services", "Nabídky databáze Azure" a "Azure Storage.".

![Aktualizace vzorníku Azure](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencil_update-300x70.png)

### <a name="onedrive-integration-feature-removed"></a>Odebraná integrační funkce OneDrivu

Funkce "Uložit na OneDrive", "Otevřít z OneDrivu" a "Sdílet odkaz" v náhledu byly odebrány. Uživatelům OneDrivu se doporučuje, aby pomocí klienta [Microsoft OneDrive pro Windows](https://onedrive.live.com/about/en-us/download/) přistupovali ke svým souborům uloženým na OneDrivu prostřednictvím standardních dialogových oken pro ukládání a otevírání souborů.

## <a name="notable-fixed-bugs-reported-by-customers"></a>Významné opravené chyby hlášené zákazníky

### <a name="in-tmt-preview-the-tool-crashes-when-using-the-standard-template"></a>V TMT Preview se nástroj při použití standardní šablony zhroutí

- Když je do výkresové plochy přidán obecný vzorník (například "Obecný tok dat") a generuje hrozby, může dojít k chybě nástroje. Tento problém byl opraven.

### <a name="in-tmt-preview-when-i-save-a-report-or-copy-the-threats-the-risk-levels-are-incorrect"></a>V TMT Preview, když uložím sestavu nebo zkopíruji hrozby, úrovně rizika jsou nesprávné

- Pokud uživatel upraví úroveň rizika konkrétních hrozeb a potom uloží sestavu nebo zkopíruje rizika, může se úroveň rizika vrátit na "Vysoká". Tento problém byl opraven.

## <a name="known-issues-and-faq"></a>Známé problémy a nejčastější dotazy

### <a name="users-of-high-resolution-screens-may-experience-small-text-in-the-threat-properties"></a>Uživatelé obrazovek s vysokým rozlišením mohou zaznamenat malý text ve vlastnostech hrozeb

#### <a name="issue"></a>Problém

V zobrazení analýzy nástroje, pokud má uživatel obrazovku s vysokým rozlišením, která je ve výchozím nastavení nastavena na zvýšení čitelnosti v systému Windows, může se část "Možné zmírnění", která je ohrožena, zobrazí s malým textem.

![Známý problém s obrazovkami s vysokým rozlišením](./media/threat-modeling-tool-releases-71509112/tmt_screen_resolution-300x153.png)

#### <a name="workaround"></a>Alternativní řešení

Uživatel může kliknout na text zmírnění a použít standardní windows zoom ovládání (Crtl-Mouse Wheel Up) pro zvýšení zvětšení této části.

### <a name="files-in-the-recently-opened-models-section-of-the-main-window-may-fail-to-open"></a>Soubory v části "Nedávno otevřené modely" v hlavním okně se nemusí otevírat

#### <a name="issue"></a>Problém

Funkce Otevřít z OneDrivu ve verzi Preview byla odebrána. Uživatelé s "Nedávno otevřené modely", které byly uloženy na OneDrive, obdrží následující chybu.

![Odebrání funkce OneDrive](./media/threat-modeling-tool-releases-71509112/tmt_save_error-300x131.png)

#### <a name="workaround"></a>Alternativní řešení

Uživatelům OneDrivu se doporučuje, aby ke svým souborům uloženým na OneDrivu prostřednictvím standardního a standardního dialogového okna Otevřít model používali klienta [Microsoft OneDrive pro Windows.](https://onedrive.live.com/about/en-us/download/)

![Odebrání funkce OneDrive](./media/threat-modeling-tool-releases-71509112/tmt_save_onedrive-300x149.png)

### <a name="my-organization-uses-the-2016-version-of-the-tool-can-i-use-the-azure-stencil-set"></a>Moje organizace používá verzi nástroje pro rok 2016, můžu použít sadu vzorníků Azure?

Ano, můžete! [Sada vzorníků Azure je dostupná na GitHubu](https://github.com/Microsoft/threat-modeling-templates/)a dá se načíst ve verzi nástroje pro rok 2016. Chcete-li vytvořit nový model se sadou vzorníků Azure, použijte dialogové okno "Šablona pro nové modely" na obrazovce hlavní nabídky. TMT 2016 nemůže vykreslit odkazy nalezené v polích "Možné skutečnosti snižující závažnost rizika" sady vzorníků Azure, proto se mohou zobrazit odkazy zobrazené jako značky HTML.

![Aktualizace vzorníku Azure v klientovi 2016](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencils-300x212.png)

## <a name="system-requirements"></a>Požadavky na systém

- Podporované operační systémy
  - Microsoft Windows 10
- Je vyžadována verze rozhraní .NET.
  - .NET 3.5.2
- Další požadavky
  - K přijímání aktualizací nástroje i šablon je nutné připojení k Internetu.

## <a name="documentation-and-feedback"></a>Dokumentace a zpětná vazba

- Dokumentace k nástroji pro modelování hrozeb je umístěna na [docs.microsoft.com](threat-modeling-tool.md)a obsahuje informace [o jeho používání](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Další kroky

Stáhněte si nejnovější verzi [nástroje Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
