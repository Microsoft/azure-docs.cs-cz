---
title: Sledování změn pomocí Azure Automation
description: Řešení Change Tracking vám pomůže identifikovat změny softwaru a služeb systému Windows, ke kterým dochází ve vašem prostředí.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: bobbytreed
ms.author: robreed
ms.date: 04/29/2019
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8a1395c89b047bb120c7f7e2d2d9bb9b4d2b0c50
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959964"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Sledování změn ve vašem prostředí pomocí Change Tracking řešení

Tento článek vám pomůže s použitím řešení Change Tracking ke snadné identifikaci změn ve vašem prostředí. Řešení sleduje změny v softwaru Windows a Linux, souborech Windows a Linux, klíčích registru Windows, službách Windows a démonech systému Linux. Identifikace změn konfigurace vám může pomáhat určit provozní problémy.

Změny nainstalovaného softwaru, služeb systému Windows, registru a souborů systému Windows a démonů systému Linux na monitorovaných serverech se odesílají do služby Azure Monitor v cloudu ke zpracování. Logika se aplikuje na přijatá data a cloudová služba data zaznamená. Pomocí informací na řídicím panelu Change Tracking můžete snadno zobrazit změny provedené v serverové infrastruktuře.

> [!NOTE]
> Azure Automation Change Tracking sleduje změny ve virtuálních počítačích. Pokud chcete sledovat změny vlastností Azure Resource Manager, přečtěte si téma [historie změn](../governance/resource-graph/how-to/get-resource-changes.md)v grafu prostředků Azure.

## <a name="supported-windows-operating-systems"></a>Podporované operační systémy Windows

Pro agenta Windows jsou oficiálně podporované následující verze operačního systému Windows:

* Windows Server 2008 R2 nebo novější

## <a name="supported-linux-operating-systems"></a>Podporované operační systémy Linux

Následující distribuce systému Linux jsou oficiálně podporována. Agent pro Linux se ale může spustit i v jiných distribucích, které nejsou uvedené. Pokud není uvedeno jinak, všechny dílčí verze jsou podporovány pro každou hlavní verzi uvedenou v seznamu.

### <a name="64-bit"></a>64bitová

* CentOS 6 a 7
* Amazon Linux 2017,09
* Oracle Linux 6 a 7
* Red Hat Enterprise Linux Server 6 a 7
* Debian GNU/Linux 8 a 9
* Ubuntu Linux 14,04 LTS, 16,04 LTS a 18,04 LTS
* SUSE Linux Enterprise Server 12

### <a name="32-bit"></a>32bitová

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 a 9
* Ubuntu Linux 14,04 LTS a 16,04 LTS

## <a name="onboard"></a>Povolit Change Tracking a inventář

Chcete-li začít sledovat změny, je nutné povolit řešení Change Tracking a inventáře. Existuje mnoho způsobů, jak připojit počítače do Change Tracking a inventáře. Níže jsou uvedené doporučené a podporované způsoby připojení řešení.

* [Z virtuálního počítače](automation-onboard-solutions-from-vm.md)
* [Z prohlížení více počítačů](automation-onboard-solutions-from-browse.md)
* [Z účtu Automation](automation-onboard-solutions-from-automation-account.md)
* [S Azure Automation sadou Runbook](automation-onboard-solutions.md)

## <a name="configuring-change-tracking-and-inventory"></a>Konfigurace Change Tracking a inventáře

Informace o tom, jak připojit počítače k řešení, najdete v tématu věnovaném [řešení pro automatizaci](automation-onboard-solutions-from-automation-account.md). Po zprovoznění počítače pomocí řešení Change Tracking a inventáře můžete nakonfigurovat položky, které se mají sledovat. Když zapnete nový soubor nebo klíč registru, který se má sledovat, je povolený pro Change Tracking i pro inventář.

Pro sledování změn v souborech v systémech Windows i Linux se používají hodnoty hash MD5 souborů. Hodnoty hash následujících se pak používají ke zjištění, zda byla od posledního inventáře provedena změna.

### <a name="file-integrity-monitoring-in-azure-security-center"></a>Monitorování integrity souborů v Azure Security Center

Azure Security Center se přidalo monitorování integrity souborů (FIM) založené na Azure Change Tracking. I když FIM monitoruje jenom soubory a registry, kompletní řešení Change Tracking zahrnuje taky:

- Změny softwaru
- Služby systému Windows
- Procesy démon systému Linux

Pokud jste již vypnuli FIM a chcete vyzkoušet úplné Change Tracking řešení, je nutné provést následující kroky. Nastavení nejsou tímto procesem odebrána.

> [!NOTE]
> Povolení úplného řešení Change Tracking může způsobit další poplatky. Další informace najdete v tématu [ceny služby Automation](https://azure.microsoft.com/en-us/pricing/details/automation/).

1. Odstraňte řešení monitorování tak, že přejdete do pracovního prostoru a vyhledáte ho v [seznamu nainstalovaných řešení monitorování](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions).
2. Kliknutím na název řešení otevřete jeho stránku Shrnutí a pak klikněte na odstranit, jak je popsáno v části [Odebrání řešení monitorování](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).
3. Řešení znovu povolte tak, že přejdete do účtu Automation a vyberete **Change Tracking** z nabídky prostředků v části **Správa konfigurace**.
4. Potvrďte podrobnosti o nastavení pracovního prostoru a klikněte na **Povolit**.

### <a name="configure-linux-files-to-track"></a>Konfigurace souborů systému Linux ke sledování

Ke konfiguraci sledování souborů na počítačích se systémem Linux použijte následující postup:

1. Ve svém účtu Automation v části **Správa konfigurace**vyberte **Change Tracking (sledování změn** ). Klikněte na **Upravit nastavení** (symbol ozubeného kolečka).
2. Na stránce **Change Tracking** vyberte soubory pro **Linux**a potom kliknutím na **+ Přidat** přidejte nový soubor, který chcete sledovat.
3. Do pole **Přidat soubor pro Linux pro Change Tracking**zadejte informace o souboru nebo adresáři, který se má sledovat, a klikněte na **Uložit**.

|Vlastnost  |Popis  |
|---------|---------|
|Povoleno     | Určuje, zda je nastavení použito.        |
|Název položky     | Popisný název souboru, který se má sledovat        |
|Skupina     | Název skupiny pro logicky seskupené soubory.        |
|Zadat cestu     | Cesta pro kontrolu souboru. Příklad: "/etc/*. conf"       |
|Typ cesty     | Typ položky, která se má sledovat. možné hodnoty jsou File a Directory.        |
|Rekurze     | Určuje, zda se při hledání položky, která má být sledována, používá rekurze.        |
|Použití sudo     | Toto nastavení určuje, zda se při kontrole položky používá sudo.         |
|Odkazy     | Toto nastavení určuje, jak se při procházení adresářů zabývá symbolické odkazy.<br> **Ignore** -ignoruje symbolické odkazy a neobsahuje odkazované soubory/adresáře.<br>**Sledovat** – sleduje symbolické odkazy během rekurze a také obsahuje odkazované soubory/adresáře.<br>**Spravovat** – sleduje symbolické odkazy a umožňuje změnu vráceného obsahu.     |
|Nahrát obsah souboru pro všechna nastavení| Zapne nebo vypne nahrávání obsahu souboru při sledovaných změnách. Dostupné možnosti: **true** nebo **false**.|

> [!NOTE]
> Možnost spravovat odkazy se nedoporučuje. Načítání obsahu souborů není podporováno.

### <a name="configure-windows-files-to-track"></a>Konfigurace souborů Windows ke sledování

Ke konfiguraci sledování souborů na počítačích se systémem Windows použijte následující postup:

1. Ve svém účtu Automation v části **Správa konfigurace**vyberte **Change Tracking (sledování změn** ). Klikněte na **Upravit nastavení** (symbol ozubeného kolečka).
2. Na stránce **Change Tracking** vyberte **soubory Windows**a potom kliknutím na **+ Přidat** přidejte nový soubor, který chcete sledovat.
3. Do pole **Přidat soubor Windows pro Change Tracking**zadejte informace o souboru, který se má sledovat, a klikněte na **Uložit**.

|Vlastnost  |Popis  |
|---------|---------|
|Povoleno     | Určuje, zda je nastavení použito.        |
|Název položky     | Popisný název souboru, který se má sledovat        |
|Skupina     | Název skupiny pro logicky seskupené soubory.        |
|Zadat cestu     | Cesta pro kontrolu souboru, například "c:\Temp @ no__t-0\*.txt".<br>Můžete také použít proměnné prostředí, například "%winDir%\System32 @ no__t-0 @ no__t-1. *"       |
|Rekurze     | Určuje, zda se při hledání položky, která má být sledována, používá rekurze.        |
|Nahrát obsah souboru pro všechna nastavení| Zapne nebo vypne nahrávání obsahu souboru při sledovaných změnách. Dostupné možnosti: **true** nebo **false**.|

## <a name="wildcard-recursion-and-environment-settings"></a>Zástupné znaky, rekurze a nastavení prostředí

Rekurze umožňuje zadat zástupné znaky, které zjednodušují sledování v adresářích, a proměnné prostředí, které umožňují sledovat soubory napříč prostředími s více nebo dynamickými názvy jednotek. V následujícím seznamu jsou uvedeny běžné informace, které byste měli znát při konfiguraci rekurze:

* Pro sledování více souborů se vyžadují zástupné znaky.
* Pokud používáte zástupné znaky, dají se použít jenom v posledním segmentu cesty. (například `c:\folder\*file*` nebo `/etc/*.conf`)
* Pokud proměnná prostředí má neplatnou cestu, ověření proběhne úspěšně, ale tato cesta selže při spuštění inventáře.
* Vyhněte se obecným cestám, jako je například `c:\*.*` při nastavení cesty, protože by to vedlo k procházení příliš velkého počtu složek.

## <a name="configure-file-content-tracking"></a>Konfigurace sledování obsahu souborů

Obsah můžete zobrazit před a po změně souboru s obsahem souboru Change Tracking. To je k dispozici pro soubory Windows a Linux, pro každou změnu v souboru, obsah souboru je uložen v účtu úložiště a tento soubor se zobrazí před a po změně, vložení nebo zobrazení vedle sebe. Další informace najdete v tématu [zobrazení obsahu sledovaného souboru](change-tracking-file-contents.md).

![Zobrazit změny v souboru](./media/change-tracking-file-contents/view-file-changes.png)

### <a name="configure-windows-registry-keys-to-track"></a>Konfigurace klíčů registru Windows ke sledování

Ke konfiguraci sledování klíčů registru v počítačích se systémem Windows použijte následující postup:

1. Ve svém účtu Automation v části **Správa konfigurace**vyberte **Change Tracking (sledování změn** ). Klikněte na **Upravit nastavení** (symbol ozubeného kolečka).
2. Na stránce **Change Tracking** vyberte možnost **registr systému Windows**a potom kliknutím na **+ Přidat** přidejte nový klíč registru, který chcete sledovat.
3. Do pole **Přidat registr systému Windows pro Change Tracking**zadejte informace pro klíč, který chcete sledovat, a klikněte na **Uložit**.

|Vlastnost  |Popis  |
|---------|---------|
|Povoleno     | Určuje, zda je nastavení použito.        |
|Název položky     | Popisný název klíče registru, který se má sledovat        |
|Skupina     | Název skupiny pro logicky seskupené klíče registru.        |
|Klíč registru Windows   | Cesta pro kontrolu klíče registru. Příklad: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="limitations"></a>Omezení

Řešení Change Tracking v současné době nepodporuje následující položky:

* Rekurze pro sledování registru Windows
* Síťové systémy souborů

Další omezení:

* Sloupec **maximální velikost souboru** a hodnoty jsou v aktuální implementaci nepoužitelné.
* Pokud shromažďujete více než 2500 souborů v cyklu shromažďování v průběhu 30 minut, může být snížen výkon řešení.
* Pokud je síťový provoz vysoký, může zobrazení změn záznamů trvat až šest hodin.
* Pokud konfiguraci upravíte v době, kdy je počítač vypnutý, může počítač publikovat změny, které patřily do předchozí konfigurace.

## <a name="known-issues"></a>Známé problémy

Řešení Change Tracking v současnosti dochází k následujícím problémům:

* Aktualizace oprav hotfix nejsou shromažďovány na počítačích s Windows serverem 2016 Core RS3.
* Procesy démon systému Linux mohou zobrazovat změněný stav, i když došlo ke změně. Důvodem je, že je zachyceno pole `SvcRunLevels`.

## <a name="change-tracking-data-collection-details"></a>Podrobnosti o Change Tracking shromažďování dat

V následující tabulce je uvedena frekvence sběru dat pro typy změn. U každého typu se aktualizuje taky datový snímek aktuálního stavu aspoň každých 24 hodin:

| **Změnit typ** | **Opakování** |
| --- | --- |
| Registr systému Windows | 50 minut |
| Soubor Windows | 30 minut |
| Soubor Linux | 15 minut |
| služby systému Windows | 10 sekund až 30 minut</br> Výchozí: 30 minut |
| Procesy démon systému Linux | 5 minut |
| Software systému Windows | 30 minut |
| Software pro Linux | 5 minut |

V následující tabulce jsou uvedené limity sledovaných položek na počítač pro Change Tracking.

| **Partner** | **Počtu**| **Poznámky** |
|---|---|---|
|Soubor|500||
|Rejstříku|250||
|Software systému Windows|250|Neobsahuje opravy hotfix softwaru|
|Balíčky Linux|1250||
|Služby|250||
|Proces|250||

Průměrné využití dat Log Analytics počítači pomocí Change Tracking a inventáře je přibližně 40MB měsíčně. Tato hodnota je pouze aproximace a podléhá změnám v závislosti na vašem prostředí. Doporučujeme vám monitorovat prostředí, abyste viděli přesné využití, které máte.

### <a name="windows-service-tracking"></a>Sledování služby systému Windows

Výchozí četnost shromažďování pro služby systému Windows je 30 minut. Chcete-li nakonfigurovat četnost, použijte příkaz **Change Tracking**. V části **Upravit nastavení** na kartě **služby systému Windows** je k dispozici posuvník, který umožňuje změnit četnost shromažďování pro služby systému Windows, a to až 10 sekund až po dobu 30 minut. Přesuňte posuvník do požadované frekvence a automaticky ho uloží.

![Posuvník služeb systému Windows](./media/change-tracking/windowservices.png)

Agent sleduje pouze změny, optimalizuje výkon agenta. Nastavení vysoké prahové hodnoty může přijít o změny, pokud se služba vrátí do původního stavu. Nastavení četnosti na menší hodnotu umožňuje zachytit změny, které mohou být chybět jinak.

> [!NOTE]
> I když může agent sledovat změny až do 10 sekund, na portálu pořád trvá několik minut, než se data zobrazí. Změny v době zobrazení na portálu jsou stále sledovány a protokolovány.

### <a name="registry-key-change-tracking"></a>Sledování změn klíčů registru

Účelem sledování změn klíčů registru je určit body rozšiřitelnosti, které mohou aktivovat kód a malware třetí strany. Následující seznam obsahuje seznam předem nakonfigurovaných klíčů registru. Tyto klíče jsou nakonfigurovány, ale nejsou povoleny. Chcete-li sledovat tyto klíče registru, je nutné povolit každou z nich.

> [!div class="mx-tdBreakAll"]
> |Klíč registru | Účel |
> |---------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitoruje běžné položky automatického spuštění, které se připojovat přímo do Průzkumníka Windows, a obvykle spouští rutiny Explorer. exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Monitoruje skripty spouštěné při spuštění.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Monitoruje skripty, které se spouštějí při vypnutí.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Sleduje klíče načtené před přihlášením uživatele ke svému účtu systému Windows. Klíč se používá pro 32 programy běžící na 64 počítačích.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Monitoruje změny nastavení aplikace.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitoruje běžné položky automatického spuštění, které se připojovat přímo do Průzkumníka Windows, a obvykle spouští rutiny Explorer. exe.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Monitoruje běžné položky automatického spuštění, které se připojovat přímo do Průzkumníka Windows, a obvykle spouští rutiny Explorer. exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitory pro registraci obslužné rutiny překrytí ikon
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitory pro registraci obslužné rutiny překrytí ikon pro 32 programy běžící na 64 počítačích.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitory pro nové objekty pomocníka pro prohlížeč pro Internet Explorer. Slouží k přístupu k model DOM (Document Object Model) (DOM) aktuální stránky a k ovládání navigace.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitory pro nové objekty pomocníka pro prohlížeč pro Internet Explorer. Slouží k přístupu k model DOM (Document Object Model) (DOM) aktuální stránky a k řízení navigace pro 32 programy běžící na 64 počítačích.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Sleduje nové rozšíření aplikace Internet Explorer, například nabídky vlastních nástrojů a vlastní tlačítka panelu nástrojů.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Sleduje nové rozšíření aplikace Internet Explorer, například nabídky vlastních nástrojů a vlastní tlačítka panelu nástrojů pro 32 programy běžící na 64 počítačích.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitoruje 32 ovladačů přidružených k wavemapper, wave1 a wave2, MSACM. imaadpcm,. msadpcm,. msgsm610 a vidc. Podobně jako v části [ovladače] v systému. Soubor INI.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitoruje 32 ovladačů přidružených k wavemapper, wave1 a wave2, MSACM. imaadpcm,. msadpcm,. msgsm610 a vidc pro 32 programy běžící na počítačích s 64-bit. Podobně jako v části [ovladače] v systému. Soubor INI.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Monitoruje seznam známých nebo běžně používaných systémových knihoven DLL; Tento systém brání lidem v zneužití slabých oprávnění adresáře aplikace tím, že vyřadí verze systémových knihoven DLL pro trojského koně.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Monitoruje seznam balíčků schopných přijímat oznámení o událostech z procesu Winlogon, což je model podpory interaktivního přihlašování pro operační systém Windows.

## <a name="network-requirements"></a>Požadavky na síť

Následující adresy jsou vyžadovány konkrétně pro Change Tracking. Komunikace s těmito adresami se provádí přes port 443.

|Veřejné Azure  |Azure Government  |
|---------|---------|
|*. ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*. oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*. blob.core.windows.net|*. blob.core.usgovcloudapi.net|
|*. azure-automation.net|*. azure-automation.us|

## <a name="use-change-tracking"></a>Použít Change Tracking

Po povolení řešení můžete zobrazit souhrn změn pro monitorované počítače výběrem možnosti **Change Tracking** v části **Správa konfigurace** v účtu Automation.

Můžete zobrazit změny v počítačích a pak přejít k podrobnostem pro každou událost. Rozevírací nabídky jsou v horní části grafu k dispozici pro omezení grafu a podrobné informace na základě typu změny a rozsahů času. Můžete také kliknout na graf a přetáhnout ho a vybrat vlastní časový rozsah. **Typ změny** bude jedna z následujících hodnot **událostí**, **démonů**, **souborů**, **registrů**, **softwaru**a **služeb systému Windows**. Kategorie zobrazuje typ změny a lze ji **Přidat**, **Upravit**nebo **Odebrat**.

![Obrázek řídicího panelu Change Tracking](./media/change-tracking/change-tracking-dash01.png)

Kliknutím na změnu nebo událost zobrazíte podrobné informace o této změně. Jak vidíte z tohoto příkladu, typ spuštění služby byl změněn z ruční na auto.

![Obrázek podrobností sledování změn](./media/change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Hledat protokoly

Kromě podrobností, které jsou k dispozici na portálu, lze hledání provádět v protokolech. Po otevření stránky **Change Tracking** klikněte na **Log Analytics**a otevře se stránka **protokoly** .

### <a name="sample-queries"></a>Ukázkové dotazy

V následující tabulce jsou uvedeny ukázky hledání v protokolech pro záznamy změn shromážděné tímto řešením:

|Dotazy  |Popis  |
|---------|---------|
|ConfigurationData<br>&#124;kde ConfigDataType = = "WindowsServices" and SvcStartupType = = "auto"<br>&#124;kde SvcState = = "zastaveno"<br>&#124;sumarizace arg_max (TimeGenerated, *) podle software ComputerName, Computer         | Zobrazuje nejaktuálnější záznamy inventáře pro služby systému Windows, které byly nastaveny na hodnotu automaticky, ale byly hlášeny jako zastavené.<br>Výsledky jsou omezené na nejnovější záznam pro daný softwarový název a počítač.      |
|ConfigurationChange<br>&#124;kde ConfigChangeType = = "software" a ChangeCategory = = "odebráno"<br>&#124;ORDER by TimeGenerated DESC|Zobrazuje záznamy změn pro odebraný software.|

## <a name="alert-on-changes"></a>Výstraha při změnách

Klíčovou funkcí Change Tracking a inventáře je schopnost upozornit na stav konfigurace a všechny změny stavu konfigurace hybridního prostředí.

V následujícím příkladu snímek obrazovky ukazuje, že soubor `C:\windows\system32\drivers\etc\hosts` byl na počítači upraven. Tento soubor je důležitý, protože soubor hostitelů používá systém Windows k překladu názvů hostitelů na IP adresy a má přednost před i DNS, což by mohlo způsobit problémy s připojením nebo přesměrování provozu na škodlivé nebo jinak nebezpečné weby.

![Graf zobrazující změnu souboru hostitelů](./media/change-tracking/changes.png)

Chcete-li tuto změnu dále analyzovat, přejděte na položku hledání v protokolu kliknutím na příkaz **Log Analytics**. Jednou v hledání v protokolu vyhledejte změny obsahu v souboru Hosts pomocí dotazu `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`. Tento dotaz vyhledá změny, které zahrnovaly změnu obsahu souborů pro soubory, jejichž plně kvalifikovaná cesta obsahuje slovo "hostitelé". Můžete také požádat o konkrétní soubor tak, že změníte část cesty na jeho plně kvalifikovaný tvar (například `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`).

Jakmile dotaz vrátí požadované výsledky, klikněte na tlačítko **nové pravidlo výstrahy** v možnosti prohledávání protokolu a otevřete stránku vytváření výstrah. Můžete také přejít k tomuto prostředí prostřednictvím **Azure monitor** v Azure Portal. V prostředí pro vytváření výstrah znovu ověřte náš dotaz a upravte logiku výstrah. V takovém případě budete chtít aktivovat výstrahu, pokud je ve všech počítačích v prostředí zjištěna i jedna změna.

![Obrázek znázorňující dotaz na změnu pro sledování změn v souboru hostitelů](./media/change-tracking/change-query.png)

Po nastavení logiky podmínky přiřaďte skupiny akcí k provádění akcí v reakci na aktivované upozornění. V tomto případě jsem nastavil e-maily, které se mají odeslat, a vytvoří se lístek ITSM.  Je možné provést i mnoho dalších užitečných akcí, například aktivovat funkci Azure Functions, Runbook služby Automation, Webhook nebo aplikaci logiky.

![Obrázek, který konfiguruje skupinu akcí pro upozornění na změnu](./media/change-tracking/action-groups.png)

Po nastavení všech parametrů a logiky můžeme tuto výstrahu použít pro prostředí.

### <a name="alert-suggestions"></a>Návrhy výstrah

Při upozorňování na změny v souboru hostitelů je jedním z užitečných výstrah pro Change Tracking nebo data inventáře, existuje mnoho dalších scénářů pro upozorňování, včetně případů definovaných spolu s příklady dotazů v níže uvedené části.

|Dotazy  |Popis  |
|---------|---------|
|ConfigurationChange <br>&#124;kde ConfigChangeType = = "Files" a FileSystemPath obsahuje "c: \\Windows @ no__t-2system32 @ no__t-3drivers @ no__t-4"|Užitečné pro sledování změn souborů důležitých pro systém|
|ConfigurationChange <br>&#124;kde FieldsChanged obsahuje "FileContentChecksum" a FileSystemPath = = "c: \\Windows @ no__t-2system32 @ no__t-3drivers @ no__t-4etc @ no__t-5hosts"|Užitečné pro sledování úprav konfiguračních souborů klíčů|
|ConfigurationChange <br>&#124;kde ConfigChangeType = = "WindowsServices" a SvcName obsahuje "W3SVC" a SvcState = = "zastaveno"|Užitečné pro sledování změn důležitých služeb systému|
|ConfigurationChange <br>&#124;kde ConfigChangeType = = "démoni" a SvcName obsahuje "SSH" a SvcState! = "Running"|Užitečné pro sledování změn důležitých služeb systému|
|ConfigurationChange <br>&#124;kde ConfigChangeType = = "software" a ChangeCategory = = "přidáno"|Užitečné pro prostředí, která potřebují uzamčenou konfiguraci softwaru.|
|ConfigurationData <br>&#124;kde software obsahuje "agent monitorování" a CurrentVersion! = "8.0.11081.0"|Užitečné pro zobrazení, které počítače mají nainstalovanou zastaralou nebo nekompatibilní verzi softwaru. Oznamuje poslední nahlášený stav konfigurace, nikoli změny.|
|ConfigurationChange <br>&#124;kde RegistryKey = = @ "HKEY_LOCAL_MACHINE @ no__t-1SOFTWARE @ no__t-2Microsoft @ no__t-3Windows @ no__t-4CurrentVersion @ no__t-5QualityCompat"| Hodí se ke sledování změn důležitých antivirových klíčů.|
|ConfigurationChange <br>&#124;kde RegistryKey obsahuje @ "HKEY_LOCAL_MACHINE @ no__t-1SYSTEM @ no__t-2CurrentControlSet @ no__t-3Services @ no__t-4SharedAccess @ no__t-5Parameters @ no__t-6FirewallPolicy"| Užitečné pro sledování změn nastavení brány firewall|

## <a name="next-steps"></a>Další kroky

Další informace o používání řešení najdete v kurzu o Change Tracking.

> [!div class="nextstepaction"]
> [Řešení potíží se změnami ve vašem prostředí](automation-tutorial-troubleshoot-changes.md)

* K zobrazení podrobných dat sledování změn použijte [prohledávání protokolů v protokolu Azure monitor](../log-analytics/log-analytics-log-searches.md) .
