---
title: Nástroj pro modelování hrozeb uvolní – modelování nástroj Microsoft Threat – Azure | Dokumentace Microsoftu
description: Dokumentace v poznámkách k verzi nástroje Modelování ohrožení
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2019
ms.author: jegeib
ms.openlocfilehash: 8d3d2acb28a82f50f0255701fe6079ad444ce03f
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360245"
---
# <a name="threat-modeling-tool-ga-release-71509112---9122018"></a>GA nástroj pro modelování hrozeb release 7.1.50911.2 - 12/9/2018

S potěšením oznamujeme, že nástroj pro modelování hrozeb v Microsoftu je nyní k dispozici ke stažení jako podporovaných všeobecně dostupná (GA) verzi. Tato verze obsahuje důležité o ochraně osobních údajů a aktualizace zabezpečení a opravy, aktualizace funkcí a vylepšení stability. Stávající uživatelé 2017 Preview verze vyzváni k aktualizaci na nejnovější verzi pomocí technologie ClickOnce při otevření klienta. Pro nové uživatele nástroje [kliknutím sem stáhnete klienta](https://aka.ms/threatmodelingtool).

V této verzi jsme se končí podpora 2017 ve verzi Preview a doporučujeme všem uživatelům aktualizaci ve verzi Preview na všeobecně dostupné verze. Na nebo za 15. října 2018 nastavíme na minimální požadovanou verzi ClickOnce pro nástroj pro modelování hrozeb a všichni klienti ve verzi Preview je třeba upgradovat.

Nástroje Microsoft Threat modelování nástroj 2016, která je dostupná na [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=49168), zůstává dál podporována až do 1. října 2019 jenom opravy zabezpečení.

## <a name="feature-changes"></a>Funkce změny

### <a name="azure-stencil-updates"></a>Aktualizace Azure vzorníku

Další Azure předlohy a jejich přidružené hrozeb a jejich zmírnění byly přidány do vzorníku nastavit přenosů v této vydané verzi. Byly provedeny významné změny v konkrétní oblasti "Azure App Service", "Nabídky databáze Azure" a "V úložišti Azure."

![Aktualizace Azure vzorníku](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_azure_stencil_update-300x70.png)

### <a name="onedrive-integration-feature-removed"></a>Odebrat funkci integrace Onedrivu

Funkce "Uložit na OneDrive", "Otevřít z Onedrivu" a "Sdílení odkazu" verze Preview byly odstraněny. Uživatelé onedrivu můžou používat od Microsoftu [OneDrive pro Windows](https://onedrive.live.com/about/en-us/download/) klienta pro přístup k jejich soubory uložené na OneDrive přes standardní soubor uložit a otevřít dialog.

## <a name="notable-fixed-bugs-reported-by-customers"></a>Zajímavé opravy chyb hlášených zákazníky

### <a name="in-tmt-preview-the-tool-crashes-when-using-the-standard-template"></a>Ve verzi Preview TMT nástroj, dojde k chybě při použití standardní šablony

- Pokud obecná vzorníku (například "obecný tok dat") se přidá do na návrhovém povrchu a generuje hrozby, může dojít k chybě nástroje. Tento problém byl vyřešen.

### <a name="in-tmt-preview-when-i-save-a-report-or-copy-the-threats-the-risk-levels-are-incorrect"></a>Ve verzi Preview TMT při uložení sestavy nebo zkopírujte hrozeb a úrovně rizik jsou nesprávné

- Pokud uživatel změní úroveň rizika konkrétních ohrožení a pak uloží sestavu nebo zkopíruje rizika, úroveň rizika může vrátit zpět na hodnotu "Vysoká". Tento problém byl vyřešen.

## <a name="known-issues-and-faq"></a>Známé problémy a nejčastější dotazy

### <a name="users-of-high-resolution-screens-may-experience-small-text-in-the-threat-properties"></a>Uživatelé ve vysokém rozlišení obrazovky pozorovat malý textový ve vlastnostech před internetovými útoky

#### <a name="issue"></a>Problém

V zobrazení analýzy nástroj Pokud má uživatel ve vysokém rozlišení obrazovky, který je ve výchozím nastavení zvětšit pro lepší čitelnost ve Windows, v části "Možné Mitigation(s)" hrozbu může se zobrazit malé textové.

![Známý problém s obrazovky s vysokým rozlišením](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_screen_resolution-300x153.png)

#### <a name="workaround"></a>Alternativní řešení

Uživatel můžete kliknout na omezení rizik text a použijte standardní ovládací prvek Lupa Windows (Crtl myši kolečka nahoru) zvětšení tohoto oddílu.

### <a name="files-in-the-recently-opened-models-section-of-the-main-window-may-fail-to-open"></a>Soubory v části "Nedávno otevřít modely" hlavní okno se pravděpodobně nezdaří spustit

#### <a name="issue"></a>Problém

Funkce "Otevřít z Onedrivu" verze Preview se odebrala. Uživatelé s "Nedávno otevřít modely", které byly uloženy na OneDrive se zobrazí následující chyba.

![Odebrat funkce OneDrive](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_save_error-300x131.png)

#### <a name="workaround"></a>Alternativní řešení

Uživatelé onedrivu můžou používat od Microsoftu [OneDrive pro Windows](https://onedrive.live.com/about/en-us/download/) klientovi přístup k jejich soubory uložené na OneDrive přes "Otevření modelu" dialogové okno a standard.

![Odebrat funkce OneDrive](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_save_onedrive-300x149.png)

### <a name="my-organization-uses-the-2016-version-of-the-tool-can-i-use-the-azure-stencil-set"></a>Naše organizace bude využívat nástroj ve verzi 2016, můžete použít sadu vzorníku Azure?

Ano, je to možné! [Sadu vzorníku Azure je dostupný na githubu](https://github.com/Microsoft/threat-modeling-templates/)a můžete ho načíst ve verzi 2016 nástroje. Chcete-li vytvořit nový model s sadu vzorníku Azure, pomocí dialogového okna "Šablonu pro nové modely" na obrazovce hlavní nabídky. TMT 2016 nelze vykreslit odkazy v polích "Možná zmírnění rizik" sadu vzorníku Azure, proto se může zobrazit odkazy zobrazí jako značky HTML.

![Aktualizace Azure vzorníku v klientovi 2016](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_azure_stencils-300x212.png)

## <a name="system-requirements"></a>Systémové požadavky

- Podporované operační systémy
  - Microsoft Windows 10
- Požadovaná verze rozhraní .NET
  - .Net 3.5.2
- Další požadavky
  - Připojení k Internetu je potřebný k přijetí aktualizací nástroje, jakož i šablon.

## <a name="documentation-and-feedback"></a>Dokumentace ke službě a zpětná vazba

- Dokumentace pro nástroj pro modelování hrozeb se nachází na [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)a obsahuje informace o [o použití nástroje](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Další postup

Stáhněte si nejnovější verzi [nástroj pro modelování hrozeb Microsoftu](https://aka.ms/threatmodelingtool).