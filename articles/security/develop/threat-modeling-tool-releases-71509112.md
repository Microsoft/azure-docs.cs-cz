---
title: Microsoft Threat Modeling Tool verze 9/12/2018
titleSuffix: Azure
description: Přečtěte si poznámky k verzi pro Microsoft Threat Modeling Tool vydané v 9/12/2018. Poznámky zahrnují změny funkcí a opravy chyb.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/15/2019
ms.openlocfilehash: 7b0afdd90f2a1413c7f8364fc4518c4d1116454a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92913581"
---
# <a name="threat-modeling-tool-ga-release-71509112---9122018"></a>Threat Modeling Tool GA Release 7.1.50911.2-9/12/2018

S radostí oznamujeme, že Microsoft Threat Modeling Tool je teď k dispozici ke stažení jako podporované všeobecně dostupné verze (GA). Tato verze obsahuje důležité aktualizace ochrany osobních údajů a zabezpečení a také opravy chyb, aktualizace funkcí a vylepšení stability. Stávajícím 2017 uživatelům verze Preview verze Preview bude po otevření klienta vyzváni k aktualizaci na nejnovější verzi prostřednictvím technologie ClickOnce. Pro nové uživatele nástroje si můžete [stáhnout klienta](https://aka.ms/threatmodelingtool).

V této verzi končíme podporu 2017 Preview a doporučujeme všechny uživatele verze Preview aktualizovat na verzi GA. Až do října 15 2018 se nastaví minimální požadovaná verze ClickOnce pro Threat Modeling Tool a všichni klienti verze Preview budou muset upgradovat.

Microsoft Threat Modeling Tool 2016, která je k dispozici na [webu Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=49168), zůstane podporovaná až do října 1 2019 pro kritické opravy zabezpečení.

## <a name="feature-changes"></a>Změny funkcí

### <a name="azure-stencil-updates"></a>Aktualizace vzorníku Azure

Další vzorníky Azure a jejich související hrozby a rizika byly přidány do sady vzorníků v této verzi. V oblastech Focus "Azure App Services", "nabídky Azure Database" a "Azure Storage" byly provedeny významné změny.

![Aktualizace vzorníku Azure](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencil_update-300x70.png)

### <a name="onedrive-integration-feature-removed"></a>Funkce integrace OneDrivu se odebrala.

Odebrali jsme z verze Preview možnosti Uložit na OneDrive, otevřít z OneDrivu a sdílet odkaz. Uživatelům OneDrivu doporučujeme používat klienta Microsoft [OneDrive pro Windows](https://onedrive.live.com/about/en-us/download/) pro přístup k souborům uloženým na OneDrivu přes standardní dialogová okna pro ukládání a otevírání souborů.

## <a name="notable-fixed-bugs-reported-by-customers"></a>Významné opravené chyby hlášené zákazníky

### <a name="in-tmt-preview-the-tool-crashes-when-using-the-standard-template"></a>V TMT ve verzi Preview nástroj při použití standardní šablony selže.

- Když je obecný Vzorník (například "obecný tok dat") přidán do kreslicí plochy a generuje hrozby, může dojít k chybě nástroje. Tento problém byl opraven.

### <a name="in-tmt-preview-when-i-save-a-report-or-copy-the-threats-the-risk-levels-are-incorrect"></a>Když v TMT ve verzi Preview ukládáte sestavu nebo tyto hrozby zkopírujete, úrovně rizika nejsou správné.

- Pokud uživatel změní úroveň rizika konkrétních hrozeb a pak uloží sestavu nebo zkopíruje rizika, úroveň rizika se může vrátit na "vysoké". Tento problém byl opraven.

## <a name="known-issues-and-faq"></a>Známé problémy a nejčastější dotazy

### <a name="users-of-high-resolution-screens-may-experience-small-text-in-the-threat-properties"></a>Uživatelé obrazovek s vysokým rozlišením můžou mít malý text ve vlastnostech hrozby.

#### <a name="issue"></a>Problém

Pokud má uživatel v zobrazení analýza v nástroji analýzu obrazovku s vysokým rozlišením, která je ve výchozím nastavení nastavená na zvětšení čitelnosti v systému Windows, může se v části "možné zmírnění" v hrozbě Zobrazit malý text.

![Známý problém s obrazovkami s vysokým rozlišením](./media/threat-modeling-tool-releases-71509112/tmt_screen_resolution-300x153.png)

#### <a name="workaround"></a>Alternativní řešení

Uživatel může kliknout na text pro zmírnění rizika a použít standardní ovládací prvek přiblížení systému Windows (CRTL kolečkem myši nahoru) a zvětšit tak zvětšení této části.

### <a name="files-in-the-recently-opened-models-section-of-the-main-window-may-fail-to-open"></a>Soubory v části naposledy otevřené modely v hlavním okně se nemusí podařit otevřít.

#### <a name="issue"></a>Problém

Odebrala se funkce "otevřená z OneDrivu" ve verzi Preview. Uživatelům s "nedávno otevřenými modely" uloženými na OneDrive se zobrazí následující chyba.

![Snímky obrazovky zobrazují chybu: Chyba při otevírání odkazu na objekt souboru není nastavena na instanci objektu.](./media/threat-modeling-tool-releases-71509112/tmt_save_error-300x131.png)

#### <a name="workaround"></a>Alternativní řešení

Uživatelům OneDrivu doporučujeme používat klienta Microsoft [OneDrive pro Windows](https://onedrive.live.com/about/en-us/download/) pro přístup k souborům uloženým na OneDrivu přes Standard a dialog Otevřít model.

![Snímek obrazovky s vybraným OneDrivem najdete v dialogovém okně Otevřít model.](./media/threat-modeling-tool-releases-71509112/tmt_save_onedrive-300x149.png)

### <a name="my-organization-uses-the-2016-version-of-the-tool-can-i-use-the-azure-stencil-set"></a>Má vaše organizace používá verzi 2016 tohoto nástroje, můžu použít sadu vzorníků Azure?

Ano, můžete! [Sada vzorníků Azure je dostupná na GitHubu](https://github.com/Microsoft/threat-modeling-templates/)a dá se načíst v 2016. verzi tohoto nástroje. Pokud chcete vytvořit nový model se sadou vzorníku Azure, použijte dialog šablona pro nové modely na obrazovce hlavní nabídky. TMT 2016 nemůže vykreslit odkazy, které se nacházejí v polích "možné zmírnění" v sadě vzorníku Azure, takže se mohou zobrazit odkazy zobrazené jako značky HTML.

![Aktualizace vzorníku Azure v klientovi 2016](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencils-300x212.png)

## <a name="system-requirements"></a>Požadavky na systém

- Podporované operační systémy
  - Microsoft Windows 10
- Vyžaduje se verze .NET.
  - .NET 3.5.2
- Další požadavky
  - K získání aktualizací nástroje a také šablon je vyžadováno připojení k Internetu.

## <a name="documentation-and-feedback"></a>Dokumentace a zpětná vazba

- Dokumentace k Threat Modeling Tool je umístěna na [docs.Microsoft.com](threat-modeling-tool.md)a obsahuje informace [o použití nástroje](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Další kroky

Stáhněte si nejnovější verzi [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
