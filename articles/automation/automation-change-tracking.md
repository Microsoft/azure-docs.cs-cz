---
title: Sledování změn s využitím Azure Automation
description: Řešení Change Tracking umožňuje identifikovat software a změny služby Windows, kterým ve vašem prostředí.
services: automation
ms.service: automation
ms.component: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 79d64a5a7eb339c6904fe026209292202632f640
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37342007"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Sledování změn ve vašem prostředí pomocí řešení Change Tracking

Tento článek vám pomůže používat řešení Change Tracking snadno identifikovat změny ve vašem prostředí. Řešení sleduje změny softwaru Windows a Linux, Windows a Linuxem soubory, klíče registru Windows, služby Windows a Linuxové procesy démon. Určení změn konfigurace vám může pomoct přesně určit provozní problémy.

Změny nainstalovaného softwaru, služby Windows, Windows registru a souborů a procesy démon Linuxu na monitorovaných serverech odesílají do služby Log Analytics v cloudu pro zpracování. Logika platí pro přijatá data a cloudové službě zaznamenává data. Podle informací uvedených na řídicím panelu řešení Change Tracking, můžete snadno zobrazit změny, které byly provedeny v serverové infrastruktuře.

## <a name="enable-change-tracking-and-inventory"></a>Povolení řešení Change Tracking a Inventory

Pokud chcete začít, sledování změn, je potřeba povolit řešení Change Tracking a Inventory pro svůj účet Automation.

1. Na webu Azure Portal přejděte na svůj účet služby Automation
1. Vyberte **Change Tracking** pod **konfigurace**.
1. Vyberte existující pracovní prostor Log analytics nebo **vytvořit nový pracovní prostor** a klikněte na tlačítko **povolit**.

To umožňuje řešení pro svůj účet automation. Řešení může trvat až 15 minut na povolit. Modrý banner vás upozorní, když bude řešení povoleno. Přejděte zpět **řešení Change Tracking** stránky pro správu řešení.

## <a name="configuring-change-tracking-and-inventory"></a>Konfigurace řešení Change Tracking a Inventory

Další informace jak připojit počítače k řešení najdete v tématu: [řešení automatizace registrace](automation-onboard-solutions-from-automation-account.md). Jakmile budete mít počítač připojování pomocí řešení Change Tracking a Inventory můžete konfigurovat položky, které chcete sledovat. Když povolíte nový soubor nebo klíč registru pro sledování, je povolen pro řešení Change Tracking a Inventory.

Při sledování změn souborů na Windows i Linuxem, se používají hodnoty hash MD5 souborů. Tyto hodnoty hash se použije ke zjištění, pokud byla provedena změna od poslední inventarizace.

### <a name="configure-linux-files-to-track"></a>Konfigurace souborů Linux ke sledování

Použijte následující postup ke konfiguraci sledování souborů na počítače s Linuxem:

1. Ve vašem účtu Automation vyberte **Change tracking** pod **CONFIGURATION MANAGEMENT**. Klikněte na tlačítko **upravit nastavení** (symbol ozubeného kolečka).
2. Na **řešení Change Tracking** stránce **soubory Linuxu**, pak klikněte na tlačítko **+ přidat** přidáte nový soubor ke sledování.
3. Na **přidat soubor Linuxu pro řešení Change Tracking**, zadejte informace o souboru nebo adresáře ke sledování a klikněte na tlačítko **Uložit**.

|Vlastnost  |Popis  |
|---------|---------|
|Povoleno     | Určuje, pokud je použito nastavení.        |
|Název položky     | Popisný název souboru, který má být sledovány.        |
|Skupina     | Název skupiny pro logické seskupení souborů.        |
|Zadat cestu     | Cesta ke kontrole souboru. Příklad: "/etc/*.conf"       |
|Typ cesty     | Typ položky být sledované. možné hodnoty jsou soubor a adresář.        |
|Rekurze     | Určuje, jestli se při hledání položky, která se má sledovat, používá rekurze.        |
|Použít sudo     | Toto nastavení určuje, jestli se při kontrole položky používá sudo.         |
|Odkazy     | Toto nastavení určuje, jak se při procházení adresářů zpracovávají symbolické odkazy.<br> **Ignorovat** – ignoruje symbolické odkazy a nezahrnuje odkazované soubory a adresáře.<br>**Postupujte podle** – během rekurze sleduje symbolické odkazy a zahrnuje i odkazované soubory a adresáře.<br>**Spravovat** – sleduje symbolické odkazy a umožňuje změnu vráceného obsahu.     |

> [!NOTE]
> Možnost Spravovat se nedoporučuje. Načítání obsahu souborů se nepodporuje.

### <a name="configure-windows-files-to-track"></a>Konfigurace souborů Windows ke sledování

Použijte následující postup ke konfiguraci soubory sledování na počítačích s Windows:

1. Ve vašem účtu Automation vyberte **Change tracking** pod **CONFIGURATION MANAGEMENT**. Klikněte na tlačítko **upravit nastavení** (symbol ozubeného kolečka).
2. Na **řešení Change Tracking** stránce **soubory Windows**, pak klikněte na tlačítko **+ přidat** přidáte nový soubor ke sledování.
3. Na **přidat soubor Windows pro Change Tracking**, zadejte informace pro soubor ke sledování a klikněte na tlačítko **Uložit**.

|Vlastnost  |Popis  |
|---------|---------|
|Povoleno     | Určuje, pokud je použito nastavení.        |
|Název položky     | Popisný název souboru, který má být sledovány.        |
|Skupina     | Název skupiny pro logické seskupení souborů.        |
|Zadat cestu     | Cesta, ve které se má soubor hledat. Například: c:\temp\myfile.txt       |

### <a name="configure-windows-registry-keys-to-track"></a>Nakonfigurovat sledování klíčů registru Windows

Pomocí následujících kroků nakonfigurovat sledování klíčů registru v počítačích Windows:

1. Ve vašem účtu Automation vyberte **Change tracking** pod **CONFIGURATION MANAGEMENT**. Klikněte na tlačítko **upravit nastavení** (symbol ozubeného kolečka).
2. Na **řešení Change Tracking** stránce **registru Windows**, pak klikněte na tlačítko **+ přidat** přidat nový klíč registru pro sledování.
3. Na **přidat registr Windows pro Change Tracking**, zadejte informace o klíči ke sledování a klikněte na tlačítko **Uložit**.

|Vlastnost  |Popis  |
|---------|---------|
|Povoleno     | Určuje, pokud je použito nastavení.        |
|Název položky     | Popisný název souboru, který má být sledovány.        |
|Skupina     | Název skupiny pro logické seskupení souborů.        |
|Klíč registru systému Windows   | Cesta ke kontrole souboru. Příklad: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common spuštění"      |

## <a name="limitations"></a>Omezení

Řešení Change Tracking v současné době nepodporuje následující položky:

* Sledování souborů složek (adresáře) pro Windows
* Rekurze pro sledování souborů Windows
* Zástupné znaky pro sledování souborů Windows
* Rekurze pro sledování registru Windows
* Proměnné cest
* Systémy souborů sítě
* Obsah souboru

Další omezení:

* **Maximální velikost souboru** sloupce a hodnoty, které nejsou používány v aktuální implementace.
* Budete-li shromažďovat více než 2500 soubory v cyklu víkend na 30minutové kolekce, může být snížený výkon řešení.
* Při vysokém zatížení sítě, záznamy řešení change může trvat až 6 hodin k zobrazení.
* Při úpravě konfigurace, zatímco počítač je vypnutý, můžete umístit počítač změny, které patřily do předchozí konfiguraci.

## <a name="known-issues"></a>Známé problémy

Řešení Change Tracking chvíli dochází k následujícím problémům:

* Aktualizace hotfix se neshromažďují pro počítače s Windows 10 Creators Update a Windows Server 2016 Core RS3.

## <a name="change-tracking-data-collection-details"></a>Změnit podrobnosti sledování dat kolekce

V následující tabulce jsou uvedeny frekvence shromažďování dat pro typy změn. Pro každý typ dat snímku aktuálního stavu se také aktualizují nejméně každých 24 hodin:

| **Změnit typ** | **Frekvence** |
| --- | --- |
| Registru Windows | 50 minut |
| Soubor Windows | 30 minut |
| Souborů v Linuxu | 15 minut |
| Služby pro Windows | 10 sekund až 30 minut.</br> Výchozí hodnota: 30 minut |
| Procesy démon Linuxu | 5 minut |
| Windows software | 30 minut |
| Softwaru platformy Linux | 5 minut |

### <a name="windows-service-tracking"></a>Sledování služby Windows

Frekvence shromažďování výchozí služby Windows je 30 minut. Pokud chcete nakonfigurovat četnost přejděte na **řešení Change Tracking**. V části **upravit nastavení** na **služby Windows** kartu, je ovládací prvek posuvník, který vám umožní změnit četnost shromažďování dat služby Windows z tak rychle jako za 10 sekund k až 30 minut. Přesuňte posuvník, kterou chcete četnost a se automaticky uloží.

![Windows services posuvníku](./media/automation-change-tracking/windowservices.png)

Agent sleduje pouze změny, tím se optimalizuje výkon agenta. Tím, že nastavíte příliš vysokou prahovou hodnotu změny mohou chybět, pokud služba vrátit do původního stavu. Nastavení četnosti na menší hodnotu umožňuje zachytit změny, které jinak může chybět.

> [!NOTE]
> I když můžete agenta sledování změn na 10 druhý interval, data stále trvá několik minut, který se má zobrazit na portálu. Změny během doby, kdy chcete-li zobrazit na portálu jsou stále sledovány a protokoluje.
  
### <a name="registry-key-change-tracking"></a>Sledování změn klíče registru

Účelem sledování změn klíče registru je ke kotvícímu bodu bodů rozšiřitelnosti, kde můžete aktivovat kód třetích stran a malwarem. Následující seznam obsahuje seznam klíčů registru předem nakonfigurovaná. Tyto klíče jsou nakonfigurovány, ale nejsou povolené. Pokud chcete sledovat tyto klíče registru, je nutné povolit každé z nich.

> [!div class="mx-tdBreakAll"]
> |  |
> |---------|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování běžných automatické spouštění záznamy, které integrovat přímo do Windows Explorer a obvykle spuštění v procesu Explorer.exe.    |
> |**Podstrom HKEY\_místní\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování skripty, které se spustí při spuštění.     |
> |**Podstrom HKEY\_místní\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown**    |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování skripty, které běží na vypnutí.     |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoruje klíče, které jsou načteny před uživatel přihlásí ke svému účtu Windows. Klíč je používán pro 32-bit programy spuštěné na 64bitových počítačích.    |
> |**Podstrom HKEY\_místní\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed komponenty**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Sleduje změny nastavení aplikace.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování běžných automatické spouštění záznamy, které integrovat přímo do Windows Explorer a obvykle spuštění v procesu Explorer.exe.|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování běžných automatické spouštění záznamy, které integrovat přímo do Windows Explorer a obvykle spuštění v procesu Explorer.exe.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování pro ikonu překryv registraci obslužné rutiny.|
|**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování pro ikonu překryv registraci obslužné rutiny pro 32-bit programy spuštěné na 64bitových počítačích.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování pro nové pluginy prohlížeče pomocné rutiny objektu pro aplikaci Internet Explorer. Používá pro přístup k Document Object Model (DOM) na aktuální stránce a navigace.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování pro nové pluginy prohlížeče pomocné rutiny objektu pro aplikaci Internet Explorer. Používá pro přístup k Document Object Model (DOM) na aktuální stránce a řídit navigaci pro 32-bit programy spuštěné na 64bitových počítačích.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování pro nová rozšíření aplikace Internet Explorer, jako jsou nabídky vlastní nástroje a vlastní tlačítka.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování pro nová rozšíření aplikace Internet Explorer, jako jsou nabídky vlastní nástroje a vlastní tlačítka pro 32-bit programy spuštěné na 64bitových počítačích.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoruje 32bitové ovladače přidružené wavemapper wave1 a wave2, msacm.imaadpcm, .msadpcm, .msgsm610 a čtyřznakového. Podobně jako v části [ovladače] v systému. Soubor INI.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování 32bitové ovladače přidružené wavemapper, wave1 a wave2, msacm.imaadpcm, .msadpcm, .msgsm610 a čtyřznakového pro 32-bit programy spuštěné na 64bitových počítačích. Podobně jako v části [ovladače] v systému. Soubor INI.|
> |**Podstrom HKEY\_místní\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoruje seznam známých nebo běžně používaných systémové knihovny DLL; Tento systém zabraňuje lidé zneužití oprávnění adresáře slabé aplikace přetažením v trojský kůň verze systémové knihovny DLL.|
> |**Podstrom HKEY\_místní\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoruje seznam balíčků může přijímat oznámení událostí z přihlášení do systému Windows, model podpory interaktivní přihlášení pro operační systém Windows.|

## <a name="use-change-tracking"></a>Pomocí řešení Change Tracking

Po povolení řešení můžete zobrazit souhrn změn pro monitorované počítače tak, že vyberete **řešení Change Tracking** pod **CONFIGURATION MANAGEMENT** ve vašem účtu Automation.

Můžete zobrazit změny pro vaše počítače a potom přejít k podrobnostem podrobnosti pro každou jednotlivou událost. V horní části grafu omezit grafu a podrobné informace v závislosti na změnu typu a čas rozsahy jsou k dispozici rozevírací nabídky. Můžete také kliknout a přetáhněte na graf a vyberte vlastní časový rozsah.

![Obrázek řídicího panelu řešení Change Tracking](./media/automation-change-tracking/change-tracking-dash01.png)

Kliknutím na změnit nebo událostí zobrazí podrobné informace o této změně. Jak je vidět z příkladu, typ spouštění služby se změnil z ručního automaticky.

![Obrázek řešení change tracking podrobnosti](./media/automation-change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Hledání protokolů

Kromě podrobností, které jsou k dispozici na portálu můžete udělat protokoly hledání. S **řešení Change Tracking** otevřít, klikněte na stránce **Log Analytics**, tím se otevře **prohledávání protokolů** stránky.

### <a name="sample-queries"></a>Ukázkové dotazy

V následující tabulce jsou uvedeny ukázky hledání v protokolech pro měnit záznamy shromážděné tímto řešením:

|Dotaz  |Popis  |
|---------|---------|
|Jsou konfigurační data<br>&#124;kde ConfigDataType == "WindowsServices" a SvcStartupType == "Auto"<br>&#124;kde SvcState == "Zastavena"<br>&#124;shrnutí arg_max(TimeGenerated, *) podle názvu softwaru, počítač         | Zobrazuje nejnovější záznamy inventáře pro služby Windows, které byly nastavené na automaticky, ale nebyly hlášeny jako zastavení<br>Výsledky jsou omezené na o nejnovější záznam pro tohoto názvu softwaru nebo počítače      |
|ConfigurationChange<br>&#124;kde ConfigChangeType == "Software" a ChangeCategory == "Odebrat"<br>&#124;Řadit podle TimeGenerated desc|Záznamy změn pro odebrání softwaru|

## <a name="next-steps"></a>Další postup

Navštivte tento kurz na další informace o použití řešení sledování změn:

> [!div class="nextstepaction"]
> [Řešení potíží se změnami ve vašem prostředí](automation-tutorial-troubleshoot-changes.md)

* Použití [prohledávání protokolů v Log Analytics](../log-analytics/log-analytics-log-searches.md) zobrazíte podrobné data řešení change tracking.
