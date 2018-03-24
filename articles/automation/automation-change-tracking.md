---
title: Sledování změn s Azure Automation.
description: Sledování změn řešení pomáhá identifikovat software a služby systému Windows změny ve vašem prostředí.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 91a093a44106ad861449b6defb140532698fa668
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Sledovat změny v prostředí s řešením sledování změn

Tento článek vám pomůže používat řešení sledování změn snadno identifikovat změny ve vašem prostředí. Řešení sleduje změny softwaru Windows a Linux, Windows a Linux soubory, klíčů registru Windows, služby systému Windows a Linux démoni. Identifikace změny konfigurace vám mohou pomoci přesně určit provozní problémy.

Změny nainstalovaného softwaru, služby systému Windows, registru systému Windows a soubory a démoni Linux na monitorované servery jsou odesílány ke službě Analýza protokolů v cloudu pro zpracování. Logika se použije pro přijatá data a cloudové služby zaznamenává data. Podle informací uvedených na řídicím panelu sledování změn, uvidíte snadno změny, které byly provedeny v serverové infrastruktuře.

## <a name="enable-change-tracking-and-inventory"></a>Povolení řešení Change Tracking a Inventory


Chcete-li zahájit sledování změn, povolit řešení inventáře a sledování změn pro svůj účet Automation.

1. Na portálu Azure přejděte na svůj účet Automation.
1. Vyberte **sledování změn** pod **konfigurace**.
2. Vyberte existující pracovní prostor analýzy protokolů nebo **vytvořit nový pracovní prostor** a klikněte na tlačítko **povolit**.

To umožňuje řešení pro svůj účet automation. Řešení může trvat až 15 minut povolit. Modré banner vás upozorní, pokud je povoleno řešení. Přejděte zpět **sledování změn** můžete spravovat řešení.

## <a name="configuring-change-tracking-and-inventory"></a>Konfigurace sledování změn a inventáře

Další informace jak připojit počítače k řešení najdete: [řešení automatizace registrace](automation-onboard-solutions-from-automation-account.md). Když povolíte nový soubor nebo klíč registru pro sledování, je povoleno sledování změn a inventáře.

### <a name="configure-linux-files-to-track"></a>Konfigurace souborů Linux ke sledování

Použijte následující postup ke konfiguraci souboru sledování počítačů se systémem Linux:

1. V účtu Automation vyberte **sledování změn** pod **CONFIGURATION MANAGEMENT**. Klikněte na tlačítko **upravit nastavení** (symbol ozubené kolečko).
2. Na **sledování změn** vyberte **Linux soubory**, pak klikněte na tlačítko **+ přidat** přidat nový soubor ke sledování.
3. Na **přidat Linux souboru pro sledování změn**, zadejte informace pro tento soubor nebo adresář ke sledování a klikněte na tlačítko **Uložit**.

|Vlastnost  |Popis  |
|---------|---------|
|Povoleno     | Určuje, pokud je použito nastavení.        |
|Název položky     | Popisný název souboru, který se mají sledovat.        |
|Skupina     | Název skupiny pro logicky seskupování soubory.        |
|Zadat cestu     | Cesta ke kontrole souboru. Například: "/etc/*.conf"       |
|Typ cesty     | Typ položky být sledovaných, možné hodnoty jsou souborů a adresářů.        |
|Rekurze     | Určuje, jestli se při hledání položky, která se má sledovat, používá rekurze.        |
|Použít sudo     | Toto nastavení určuje, jestli se při kontrole položky používá sudo.         |
|Odkazy     | Toto nastavení určuje, jak se při procházení adresářů zpracovávají symbolické odkazy.<br> **Ignorovat** – ignoruje symbolické odkazy a nezahrnuje soubory nebo adresáře odkazuje.<br>**Postupujte podle** – během rekurze postupuje podle symbolické odkazy a také zahrnuje soubory nebo adresáře odkazuje.<br>**Správa** – postupuje podle symbolické odkazy a umožňuje změnu vrácený obsahu.     |

> [!NOTE]
> Možnost Spravovat se nedoporučuje. Načítání obsahu souborů se nepodporuje.

### <a name="configure-windows-files-to-track"></a>Konfigurovat soubory systému Windows ke sledování

Pomocí následujících kroků nakonfigurujte soubory sledování v počítačích se systémem Windows:

1. V účtu Automation vyberte **sledování změn** pod **CONFIGURATION MANAGEMENT**. Klikněte na tlačítko **upravit nastavení** (symbol ozubené kolečko).
2. Na **sledování změn** vyberte **soubory Windows**, pak klikněte na tlačítko **+ přidat** přidat nový soubor ke sledování.
3. Na **přidat soubor systému Windows pro sledování změn**, zadejte informace pro soubor, který chcete sledovat a klikněte na tlačítko **Uložit**.

|Vlastnost  |Popis  |
|---------|---------|
|Povoleno     | Určuje, pokud je použito nastavení.        |
|Název položky     | Popisný název souboru, který se mají sledovat.        |
|Skupina     | Název skupiny pro logicky seskupování soubory.        |
|Zadat cestu     | Cesta, ve které se má soubor hledat. Například: c:\temp\myfile.txt       |

### <a name="configure-windows-registry-keys-to-track"></a>Nakonfigurovat klíče registru Windows ke sledování

Použijte následující postup ke konfiguraci sledování klíče registru na počítače se systémem Windows:

1. V účtu Automation vyberte **sledování změn** pod **CONFIGURATION MANAGEMENT**. Klikněte na tlačítko **upravit nastavení** (symbol ozubené kolečko).
2. Na **sledování změn** vyberte **registru systému Windows**, pak klikněte na tlačítko **+ přidat** přidat nový klíč registru pro sledování.
3. Na **přidat registru systému Windows pro sledování změn**, zadejte informace pro klíč k sledování a klikněte na tlačítko **Uložit**.

|Vlastnost  |Popis  |
|---------|---------|
|Povoleno     | Určuje, pokud je použito nastavení.        |
|Název položky     | Popisný název souboru, který se mají sledovat.        |
|Skupina     | Název skupiny pro logicky seskupování soubory.        |
|Klíč registru systému Windows   | Cesta ke kontrole souboru. Například: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User prostředí Folders\Common spuštění"      |

## <a name="limitations"></a>Omezení

Řešení sledování změn v současné době nepodporuje následující položky:

* Sledování souborů složek (adresářů) pro Windows
* Rekurze pro sledování souboru systému Windows
* Sledování souborů zástupné znaky pro Windows
* Proměnné cest
* Systémy souborů síť
* Obsah souboru

Jiná omezení:

* **Maximální velikost souboru** sloupec a hodnoty se nepoužívá v aktuální implementace.
* Pokud jste v cyklu 30 minut kolekce shromažďovat více než 2 500 soubory, může docházet ke snížení výkonu řešení.
* Když je síťový provoz vysoké, záznamy změn může trvat až šest hodin k zobrazení.
* Pokud upravíte konfiguraci, zatímco počítač je vypnutý, můžete umístit počítač změny, které byly součástí předchozí konfiguraci.

## <a name="known-issues"></a>Známé problémy

Řešení sledování změn je aktuálně má následující problémy:
* Aktualizace hotfix nejsou shromážděny v pro Windows 10 Creators Update a Windows Server 2016 základní RS3 počítače.

## <a name="change-tracking-data-collection-details"></a>Změňte podrobnosti pro kolekce dat sledování

Následující tabulka uvádí četnost shromažďování dat pro typy změn. Pro každý typ dat snímek aktuálního stavu je také aktualizovat minimálně každých 24 hodin:

| **Změnit typ** | **Frekvence** |
| --- | --- |
| Registru systému Windows | 50 minut | 
| Soubor systému Windows | 30 minut | 
| Soubor Linux | 15 minut | 
| Služby pro Windows | 30 minut | 
| Démoni Linux | 5 minut |
| Windows software | 30 minut | 
| Softwaru platformy Linux | 5 minut | 

### <a name="registry-key-change-tracking"></a>Sledování změn klíče registru

Účelem monitorování změn klíče registru je ke kotvícímu bodu body rozšiřitelnosti, kde můžete aktivovat kód třetích stran a malwarem. Následující seznam obsahuje seznam klíčů registru předem nakonfigurovaná. Tyto klíče jsou nakonfigurovány, ale není povolen. Pokud chcete sledovat tyto klíče registru, je nutné povolit každé z nich.

> [!div class="mx-tdBreakAll"]
> |  |
> |---------|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování běžných automatické spuštění položky, které připojit přímo do Průzkumníka Windows a obvykle spuštění v rámci procesu Explorer.exe.    |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování skripty, které spustí při spuštění.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown**    |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování skripty, které běží při vypnutí.     |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoruje klíče, které jsou načteny před uživatel přihlásí k účtu systému Windows. Používá se pro 32bitové aplikace spuštěné na 64bitových počítačích.    |
> |**Nastavení HKEY\_místní\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed součásti**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Sleduje změny nastavení aplikace.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování běžných automatické spuštění položky, které připojit přímo do Průzkumníka Windows a obvykle spuštění v rámci procesu Explorer.exe.|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování běžných automatické spuštění položky, které připojit přímo do Průzkumníka Windows a obvykle spuštění v rámci procesu Explorer.exe.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování pro ikonu překrytí registraci obslužné rutiny.|
|**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování pro ikonu překrytí registrace obslužné rutiny pro 32bitové aplikace spuštěné na 64bitových počítačích.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování pro moduly nový prohlížeč Pomocník objekt plug-in pro Internet Explorer. Použít pro přístup k modelu objektu dokumentu (DOM) aktuální stránky a k řízení navigace.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování pro moduly nový prohlížeč Pomocník objekt plug-in pro Internet Explorer. Použít pro přístup k modelu objektu dokumentu (DOM) aktuální stránky a k řízení navigace pro 32bitové aplikace spuštěné na 64bitových počítačích.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování pro nové rozšíření, Internet Explorer, jako je například vlastní nástroj nabídky a tlačítka panelu nástrojů vlastní.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování pro nové rozšíření, Internet Explorer, jako jsou nabídky vlastního nástroje a vlastní panel nástrojů tlačítka pro 32bitové aplikace spuštěné na 64bitových počítačích.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoruje 32bitové ovladače přidružené wavemapper, wave1 a wave2, msacm.imaadpcm, .msadpcm, .msgsm610 a čtyřznakového. Podobně jako v oddílu [ovladače] v systému. Soubor INI.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování 32bitové ovladače přidružené wavemapper, wave1 a wave2, msacm.imaadpcm, .msadpcm, .msgsm610 a čtyřznakového pro 32bitové aplikace spuštěné na 64bitových počítačích. Podobně jako v oddílu [ovladače] v systému. Soubor INI.|
> |**HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoruje seznam známých nebo běžně používané systémové knihovny DLL; Tento systém brání osobám v zneužitím slabé aplikace directory oprávnění podle vyřazením trojský kůň verze systémové knihovny DLL.|
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoruje seznam balíčků schopný přijímat oznámení událostí z přihlášení do systému Windows, model podporu interaktivní přihlášení pro operační systém Windows.|

## <a name="use-change-tracking"></a>Pomocí sledování změn

Po povolení řešení můžete zobrazit souhrnné informace o změnách pro monitorované počítače tak, že vyberete **sledování změn** pod **CONFIGURATION MANAGEMENT** ve vašem účtu Automation.

Můžete zobrazit změny pro vaše počítače a potom přejít k podrobnostem podrobnosti pro každou jednotlivou událost. Rozevírací nabídky jsou k dispozici v horní části grafu omezit graf a podrobné informace, které jsou založené na rozsahy změnit typ a čas. Můžete také klikněte na tlačítko a přetáhněte ji na graf a vyberte vlastní časový rozsah.

![Obrázek sledování změn řídicí panel](./media/automation-change-tracking/change-tracking-dash01.png)

Kliknutím na změnu nebo událost se vyvolá podrobné informace o této změně. Jak je vidět z příkladu typ spouštění služby byl změněn z ruční na automaticky.

![Obrázek podrobnosti sledování změn](./media/automation-change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Protokoly vyhledávání

Kromě podrobností, které jsou k dispozici na portálu můžete provést hledání protokoly. S **sledování změn** otevřený, klikněte na stránce **analýzy protokolů**, otevře se **hledání protokolů** stránky.

### <a name="sample-queries"></a>Ukázkové dotazy

Následující tabulka obsahuje ukázkový protokol hledání pro měnit záznamy shromažďují tohoto řešení:

|Dotaz  |Popis  |
|---------|---------|
|ConfigurationData<br>&#124;kde ConfigDataType == "WindowsServices" a SvcStartupType == "Automatické"<br>&#124;kde SvcState == "Stopped"<br>&#124;shrnout arg_max(TimeGenerated, *) podle SoftwareName počítače         | Zobrazuje nejnovější záznamů inventáře pro služby systému Windows, které byly nastavené na hodnotu Auto, ale ohlášeny jako zastavenou<br>Výsledky jsou omezeny na poslední záznam pro tento SoftwareName a počítač      |
|Změnakonfigurace<br>&#124;kde ConfigChangeType == "Software" a ChangeCategory == "Odebrat"<br>&#124;Řadit podle TimeGenerated desc|Ukazuje změnu záznamy pro odebrané softwaru|

## <a name="next-steps"></a>Další postup

Tento kurz, navštivte na další informace o používání řešení sledování změn:

> [!div class="nextstepaction"]
> [Řešení potíží s změn ve vašem prostředí](automation-tutorial-troubleshoot-changes.md)

* Použití [přihlásit analýzy protokolů hledání](../log-analytics/log-analytics-log-searches.md) zobrazíte podrobné sledování dat změn.
