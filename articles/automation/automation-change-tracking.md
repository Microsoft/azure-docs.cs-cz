---
title: Sledování změn s využitím Azure Automation
description: Řešení Change Tracking umožňuje identifikovat software a změny služby Windows, kterým ve vašem prostředí.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 02/19/2019
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 07fd8c41e7817e232513ed9a260c3722a1fdac11
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429260"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Sledování změn ve vašem prostředí pomocí řešení Change Tracking

Tento článek vám pomůže používat řešení Change Tracking snadno identifikovat změny ve vašem prostředí. Řešení sleduje změny softwaru Windows a Linux, Windows a Linuxem soubory, klíče registru Windows, služby Windows a Linuxové procesy démon. Určení změn konfigurace vám může pomoct přesně určit provozní problémy.

Změny nainstalovaného softwaru, služby Windows, Windows registru a souborů a procesy démon Linuxu na monitorovaných serverech odesílají do služby Log Analytics v cloudu pro zpracování. Logika platí pro přijatá data a cloudové službě zaznamenává data. Podle informací uvedených na řídicím panelu řešení Change Tracking, můžete snadno zobrazit změny, které byly provedeny v serverové infrastruktuře.

## <a name="supported-windows-operating-systems"></a>Podporované operační systémy Windows

Pro agenta Windows se oficiálně podporuje následující verze operačního systému Windows:

* Windows Server 2008 R2 nebo novější

## <a name="supported-linux-operating-systems"></a>Podporované operační systémy a Linux

Následující Linuxových distribucích se oficiálně podporuje. Agenta pro Linux může také spustit na jiné distribuce není uvedená. Pokud není uvedeno jinak, jsou podporovány všechny dílčí verze pro všechny hlavní verze uvedené.  

### <a name="64-bit"></a>64 bitů

* CentOS 6 a 7
* Amazon Linux 2017.09
* Oracle Linux 6 a 7
* Red Hat Enterprise Linux Server 6 a 7
* Debian GNU/Linux 8 a 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS a 18.04 LTS
* SUSE Linux Enterprise Server 12

### <a name="32-bit"></a>32 bitů

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 a 9
* Ubuntu Linux 14.04 LTS a 16.04 LTS

## <a name="onboard"></a>Povolení řešení Change Tracking a Inventory

Pokud chcete začít, sledování změn, je potřeba povolit řešení Change Tracking a Inventory. Existuje mnoho způsobů, jak připojit počítače k řešení Change Tracking a Inventory. Následující jsou doporučené a podporované způsoby začlenění řešení.

* [Z virtuálního počítače](automation-onboard-solutions-from-vm.md)
* [Z procházení více počítačů](automation-onboard-solutions-from-browse.md)
* [Ve svém účtu Automation](automation-onboard-solutions-from-automation-account.md)
* [Pomocí runbooku Azure Automation](automation-onboard-solutions.md)

## <a name="configuring-change-tracking-and-inventory"></a>Konfigurace řešení Change Tracking a Inventory

Další informace jak připojit počítače k řešení najdete v tématu: [Řešení pro automatizaci připojování](automation-onboard-solutions-from-automation-account.md). Jakmile budete mít počítač připojování pomocí řešení Change Tracking a Inventory, můžete konfigurovat položky, které chcete sledovat. Když povolíte nový soubor nebo klíč registru pro sledování, je povolen pro řešení Change Tracking a Inventory.

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
|Nahrát obsah souboru pro všechna nastavení| Zapne nebo vypne u sledovaných změn nahrávání obsahu souboru. Dostupné možnosti: **Hodnota TRUE** nebo **False**.|

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
|Zadat cestu     | Cesta, ve které se má soubor hledat, například: c:\temp\\\*.txt.<br>Můžete použít také proměnnou prostředí, například %winDir%\System32\\\*.*.       |
|Rekurze     | Určuje, jestli se při hledání položky, která se má sledovat, používá rekurze.        |
|Nahrát obsah souboru pro všechna nastavení| Zapne nebo vypne u sledovaných změn nahrávání obsahu souboru. Dostupné možnosti: **Hodnota TRUE** nebo **False**.|

## <a name="wildcard-recursion-and-environment-settings"></a>Nastavení zástupný znak, rekurze a prostředí

Rekurze můžete zadat zástupné znaky pro zjednodušení sledování napříč adresářů a proměnných prostředí, aby bylo možné sledovat soubory v rámci prostředí s několika nebo dynamické jednotka názvy. Následující seznam uvádí běžné informace, které byste měli vědět při konfiguraci rekurze:

* Zástupné znaky jsou požadovány pro sledování více souborů
* Pokud použijete zástupné znaky, že jde použít jenom v posledním segmentu cesty. (například `c:\folder\*file*` nebo `/etc/*.conf`)
* Pokud proměnná prostředí obsahuje neplatnou cestu, bude ověření úspěšné, ale tato cesta se nezdaří spuštění inventáře.
* Například vyhnout obecné cesty `c:\*.*` při nastavování cestu, protože výsledkem by příliš mnoho složek se procházet.

## <a name="configure-file-content-tracking"></a>Konfigurovat sledování obsah souboru

Zobrazí se obsah a před a po změně souboru s File Content Change Tracking. To je k dispozici pro soubory Windows a Linuxem, pro jednotlivé změny do souboru, obsah souboru je uložené v účtu úložiště a zobrazuje soubor před a po změnu, vložených nebo vedle sebe. Další informace najdete v tématu [zobrazit obsah souboru sledované](change-tracking-file-contents.md).

![zobrazení změn v souboru](./media/change-tracking-file-contents/view-file-changes.png)

### <a name="configure-windows-registry-keys-to-track"></a>Nakonfigurovat sledování klíčů registru Windows

Pomocí následujících kroků nakonfigurovat sledování klíčů registru v počítačích Windows:

1. Ve vašem účtu Automation vyberte **Change tracking** pod **CONFIGURATION MANAGEMENT**. Klikněte na tlačítko **upravit nastavení** (symbol ozubeného kolečka).
2. Na **řešení Change Tracking** stránce **registru Windows**, pak klikněte na tlačítko **+ přidat** přidat nový klíč registru pro sledování.
3. Na **přidat registr Windows pro Change Tracking**, zadejte informace o klíči ke sledování a klikněte na tlačítko **Uložit**.

|Vlastnost  |Popis  |
|---------|---------|
|Povoleno     | Určuje, pokud je použito nastavení.        |
|Název položky     | Popisný název klíče registru, které se mají sledovat.        |
|Skupina     | Název skupiny pro logické seskupení klíče registru.        |
|Klíč registru systému Windows   | Cesta ke kontrole pro klíč registru. Příklad: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="limitations"></a>Omezení

Řešení Change Tracking v současné době nepodporuje následující položky:

* Rekurze pro sledování registru Windows
* Systémy souborů sítě

Další omezení:

* **Maximální velikost souboru** sloupce a hodnoty, které nejsou používány v aktuální implementace.
* Budete-li shromažďovat více než 2500 soubory v cyklu víkend na 30minutové kolekce, může být snížený výkon řešení.
* Při vysokém zatížení sítě, záznamy řešení change může trvat až 6 hodin k zobrazení.
* Při úpravě konfigurace, zatímco počítač je vypnutý, můžete umístit počítač změny, které patřily do předchozí konfiguraci.

## <a name="known-issues"></a>Známé problémy

Řešení Change Tracking chvíli dochází k následujícím problémům:

* Aktualizace hotfix se neshromažďují počítačích s Windows serverem 2016 Core RS3.

## <a name="change-tracking-data-collection-details"></a>Změnit podrobnosti sledování dat kolekce

V následující tabulce jsou uvedeny frekvence shromažďování dat pro typy změn. Pro každý typ dat snímku aktuálního stavu se také aktualizují nejméně každých 24 hodin:

| **Změnit typ** | **Frekvence** |
| --- | --- |
| Registru Windows | 50 minut |
| Soubor Windows | 30 minut |
| Souborů v Linuxu | 15 minut |
| Služby pro Windows | 10 sekund až 30 minut.</br> Výchozí: 30 minut |
| Procesy démon Linuxu | 5 minut |
| Windows software | 30 minut |
| Softwaru platformy Linux | 5 minut |

Následující tabulka uvádí omezení sledované položky na počítač pro řešení Change Tracking.

| **Prostředek** | **Limit**| **Poznámky** |
|---|---|---|
|File|500||
|Registr|250||
|Windows software|250|Nezahrnuje aktualizací softwaru|
|Balíčky Linux|1250||
|Služby|250||
|Démon|250||

Průměrné využití dat Log Analytics pro počítače s pomocí řešení Change Tracking a Inventory je přibližně 40MB za měsíc. Tato hodnota je pouze přibližný a může se změnit podle vašeho prostředí. Doporučuje se, že monitorovat vaše prostředí a přesně na používání, které máte.

### <a name="windows-service-tracking"></a>Sledování služby Windows

Frekvence shromažďování výchozí služby Windows je 30 minut. Pokud chcete nakonfigurovat četnost, přejděte na **řešení Change Tracking**. V části **upravit nastavení** na **služby Windows** kartu, je ovládací prvek posuvník, který vám umožní změnit četnost shromažďování dat služby Windows z tak rychle jako za 10 sekund k až 30 minut. Přesuňte posuvník, kterou chcete četnost a se automaticky uloží.

![Windows services posuvníku](./media/automation-change-tracking/windowservices.png)

Agent sleduje pouze změny, tím se optimalizuje výkon agenta. Nastavení prahové hodnoty vysoké chybí změn v Pokud služby vrátit do původního stavu. Nastavení četnosti na menší hodnotu umožňuje zachytit změny, které jinak může chybět.

> [!NOTE]
> I když můžete agenta sledování změn na 10 druhý interval, data stále trvá několik minut, který se má zobrazit na portálu. Změny během doby, kdy chcete-li zobrazit na portálu jsou stále sledovány a protokoluje.
  
### <a name="registry-key-change-tracking"></a>Sledování změn klíče registru

Účelem sledování změn klíče registru je ke kotvícímu bodu bodů rozšiřitelnosti, kde můžete aktivovat kód třetích stran a malwarem. Následující seznam obsahuje seznam klíčů registru předem nakonfigurovaná. Tyto klíče jsou nakonfigurovány, ale nejsou povolené. Pokud chcete sledovat tyto klíče registru, je nutné povolit každé z nich.

> [!div class="mx-tdBreakAll"]
> |  |
> |---------|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování běžných automatické spouštění záznamy, které integrovat přímo do Windows Explorer a obvykle spuštění v procesu Explorer.exe.    |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorování skripty, které se spustí při spuštění.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown**    |
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
> |**HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoruje seznam známých nebo běžně používaných systémové knihovny DLL; Tento systém zabraňuje lidé zneužití oprávnění adresáře slabé aplikace přetažením v trojský kůň verze systémové knihovny DLL.|
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoruje seznam balíčků může přijímat oznámení událostí z přihlášení do systému Windows, model podpory interaktivní přihlášení pro operační systém Windows.|

## <a name="network-requirements"></a>Síťové požadavky

Tyto adresy jsou požadovány speciálně pro řešení Change Tracking. Komunikace na tyto adresy se provádí přes port 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

## <a name="use-change-tracking"></a>Pomocí řešení Change Tracking

Po povolení řešení můžete zobrazit souhrn změn pro monitorované počítače tak, že vyberete **řešení Change Tracking** pod **CONFIGURATION MANAGEMENT** ve vašem účtu Automation.

Můžete zobrazit změny pro vaše počítače a potom přejít k podrobnostem podrobnosti pro každou jednotlivou událost. V horní části grafu omezit grafu a podrobné informace v závislosti na změnu typu a čas rozsahy jsou k dispozici rozevírací nabídky. Můžete také kliknout a přetáhněte na graf a vyberte vlastní časový rozsah.

![Obrázek řídicího panelu řešení Change Tracking](./media/automation-change-tracking/change-tracking-dash01.png)

Kliknutím na změnit nebo událostí zobrazí podrobné informace o této změně. Jak je vidět z příkladu, typ spouštění služby se změnil z ručního automaticky.

![Obrázek řešení change tracking podrobnosti](./media/automation-change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Hledat protokoly

Kromě podrobností, které jsou k dispozici na portálu můžete udělat protokoly hledání. S **řešení Change Tracking** otevřít, klikněte na stránce **Log Analytics**, tím se otevře **prohledávání protokolů** stránky.

### <a name="sample-queries"></a>Ukázkové dotazy

V následující tabulce jsou uvedeny ukázky hledání v protokolech pro měnit záznamy shromážděné tímto řešením:

|Dotaz  |Popis  |
|---------|---------|
|ConfigurationData<br>&#124;kde ConfigDataType == "WindowsServices" a SvcStartupType == "Auto"<br>&#124;kde SvcState == "Zastavena"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | Zobrazuje nejnovější záznamy inventáře pro služby Windows, které byly nastavené na automaticky, ale nebyly hlášeny jako zastavení<br>Výsledky jsou omezené na o nejnovější záznam pro tohoto názvu softwaru nebo počítače      |
|ConfigurationChange<br>&#124;kde ConfigChangeType == "Software" a ChangeCategory == "Odebrat"<br>&#124;Řadit podle TimeGenerated desc|Záznamy změn pro odebrání softwaru|

## <a name="alert-on-changes"></a>Upozornění na změny

Klíčová dovednost, pomocí řešení Change Tracking a Inventory je schopnost upozornění na stav konfigurace a všechny změny konfigurace stavu vašeho hybridního prostředí.  

V následujícím příkladu snímek obrazovky ukazuje, že soubor `C:\windows\system32\drivers\etc\hosts` byl změněn na počítači. Tento soubor je důležité, protože soubor hostitelů se používá ve Windows k překladu názvů hostitelů IP adres a má přednost před i DNS, což může způsobit problémy s připojením nebo přesměrování provozu na weby škodlivých aktivit nebo jinak nebezpečné.

![Graf zobrazující hostitele změnu souboru](./media/automation-change-tracking/changes.png)

Tato změna dále analyzovat, přejděte na hledání v protokolu od kliknutí na **Log Analytics**. Jednou v prohledávání protokolu, vyhledejte změny obsahu v souboru hostitelů s dotazem `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`. Tento dotaz hledá změny, které zahrnuté změny obsahu souboru pro soubory, jejichž plně kvalifikovaná cesta obsahuje slovo "hostitelů". Můžete také požádat o konkrétního souboru tak, že změníte jeho plně kvalifikovaný formuláře část cesty (například `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`).

Jakmile dotaz vrací požadovaných výsledků, klikněte na tlačítko **nové pravidlo upozornění** tlačítko v možnosti vyhledávání protokolu a otevřete stránku vytvoření výstrahy. Může také přejít na toto prostředí prostřednictvím **Azure Monitor** na webu Azure Portal. V prostředí pro vytváření výstrahy dotaz znovu zkontrolovat a upravit logika upozornění. V tomto případě chcete výstrahu, kterou chcete aktivovat, pokud je ještě jednu změnu zjistil ve všech počítačích v prostředí.

![Obrázek znázorňující změnit dotaz pro sledování změn do souboru hostitelů](./media/automation-change-tracking/change-query.png)

Po nastavení podmíněné logiky přiřadíte skupiny akcí provádět akce v reakci na upozornění, že dochází k aktivaci. V tomto případě jsem nastavili e-mailů k odeslání a lístek ITSM, který se má vytvořit.  Také je možné provést mnoho dalších užitečné akce jako je aktivace funkce Azure Functions, automatizace sady runbook, webhook nebo aplikaci logiky.

![Obrázek konfigurace skupiny akcí upozornění na změnu](./media/automation-change-tracking/action-groups.png)

Po nastavení jsou všechny parametry a logiku, můžeme použít upozornění v prostředí.

### <a name="alert-suggestions"></a>Upozornění návrhy

Při upozorňování na změny do souboru hostitelů je vhodné použití upozornění na data řešení Change Tracking a Inventory, existuje mnoho více scénářů pro výstrahy, včetně případů definované spolu s jejich příklady dotazů v níže uvedené části.

|Dotaz  |Popis  |
|---------|---------|
|ConfigurationChange <br>&#124;kde ConfigChangeType == "Files" a FileSystemPath obsahuje "c:\\windows\\system32\\ovladače\\"|Užitečné při sledování změn do důležitých systémových souborů|
|ConfigurationChange <br>&#124;kde FieldsChanged obsahuje "FileContentChecksum" a FileSystemPath == "c:\\windows\\system32\\ovladače\\atd\\hostitelů"|Užitečné při sledování změn klíče konfiguračních souborů|
|ConfigurationChange <br>&#124;kde ConfigChangeType == "WindowsServices" a obsahuje SvcName "w3svc" a SvcState == "Stopped"|Užitečné pro sledování změn systému kritických služeb|
|ConfigurationChange <br>&#124;kde ConfigChangeType == "Procesy démon" a obsahuje SvcName "ssh" a SvcState! = "Spuštěna"|Užitečné pro sledování změn systému kritických služeb|
|ConfigurationChange <br>&#124;kde ConfigChangeType == "Software" a ChangeCategory == "Přidání"|Užitečné pro prostředí tuto potřebu uzamčen konfigurace softwaru|
|ConfigurationData <br>&#124;kde SoftwareName obsahuje "Monitoring Agent" a CurrentVersion! = "8.0.11081.0"|Užitečné pro zobrazení, které mají počítače nainstalovanou verzi softwaru zastaralé nebo nedodržují předpisy. Oznámí poslední stav ohlášené konfigurace není změny.|
|ConfigurationChange <br>&#124;Pokud klíč registru == "HKEY_LOCAL_MACHINE\\softwaru\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Užitečné pro sledování změn zásadní antivirový klíčů|
|ConfigurationChange <br>&#124;Pokud klíč registru obsahuje "HKEY_LOCAL_MACHINE\\systému\\CurrentControlSet\\služby\\SharedAccess\\parametry\\FirewallPolicy"| Užitečné pro sledování změn v nastavení brány firewall|

## <a name="next-steps"></a>Další postup

Navštivte tento kurz na další informace o použití řešení sledování změn:

> [!div class="nextstepaction"]
> [Řešení potíží se změnami ve vašem prostředí](automation-tutorial-troubleshoot-changes.md)

* Použití [prohledávání protokolů v Log Analytics](../log-analytics/log-analytics-log-searches.md) zobrazíte podrobné data řešení change tracking.

