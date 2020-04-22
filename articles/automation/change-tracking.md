---
title: Sledování změn pomocí Azure Automation
description: Řešení sledování změn pomáhá identifikovat změny softwaru a služby Systému Windows, ke kterým dochází ve vašem prostředí.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/28/2019
ms.topic: conceptual
ms.openlocfilehash: d84566c7680081561f60d4825f25a9ce19e02b24
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682980"
---
# <a name="track-environment-changes-with-change-tracking"></a>Sledování změn prostředí pomocí sledování změn

Tento článek vám pomůže použít řešení sledování změn snadno identifikovat změny ve vašem prostředí. Řešení sleduje následující změny konfigurace, které vám pomohou určit provozní problémy:

- Software windows
- Linux software (balíčky)
    >[!NOTE]
    >Sledování změn sleduje pouze software, který je spravován pomocí správce distribučních balíčků.

- Soubory systému Windows a Linux
- Klíče registru systému Windows
- Služby pro Windows
- Linuxové daemony

Po povolení řešení můžete zobrazit souhrn změn pro monitorované počítače výběrem **funkce Sledování změn** v části Správa **konfigurace** v účtu automatizace.

> [!NOTE]
> Azure Automation Change Tracking sleduje změny ve virtuálních počítačích. Pokud chcete sledovat změny vlastností Azure Resource Manager, najdete v tématu [Historie změn](../governance/resource-graph/how-to/get-resource-changes.md)azure resource graph .

Můžete zobrazit změny v počítačích a potom přejít k podrobnostem pro každou událost. Rozevírací nabídky jsou k dispozici v horní části grafu, aby se graf omezil a podrobné informace založené na typu změny a časových rozsahech. Můžete také klepnout a přetáhnout v grafu a vybrat vlastní časový rozsah. **Typ změny** bude jednou z následujících hodnot **Události**, **Daemons**, **Files**, **Registry**, **Software**, **Windows Services**. Kategorie zobrazuje typ změny a lze ji **přidat**, **změnit**nebo **odebrat**.

![Obrázek řídicího panelu Sledování změn](./media/change-tracking/change-tracking-dash01.png)

Kliknutím na změnu nebo událost se zobrazí podrobné informace o této změně. Jak můžete vidět z příkladu, typ spuštění služby byl změněn z ruční na auto.

![Obrázek podrobností o sledování změn](./media/change-tracking/change-tracking-details.png)

Změny nainstalovaného softwaru, služeb Windows, registru a souborů systému Windows a daemonů Linuxu na monitorovaných serverech se posílají službě Azure Monitor v cloudu ke zpracování. Na přijatá data se aplikuje logika a cloudová služba data zaznamená. Pomocí informací na řídicím panelu Sledování změn můžete snadno zobrazit změny, které byly provedeny v infrastruktuře serveru.

## <a name="supported-operating-systems"></a>Podporované operační systémy

### <a name="windows-operating-systems"></a>Operační systémy Windows

Pro agenta windows jsou oficiálně podporovány následující verze operačního systému Windows:

* Windows Server 2008 R2 nebo novější

### <a name="linux-operating-systems"></a>Operační systémy Linux

Následující distribuce Linuxu jsou oficiálně podporovány. Agent Linuxu však může také spustit na jiných distribucích, které nejsou uvedeny. Není-li uvedeno jinak, jsou pro každou hlavní uvedenou verzi podporovány všechny dílčí verze.

#### <a name="64-bit"></a>64bitová

* CentOS 6 a 7
* Amazon Linux 2017.09
* Oracle Linux 6 a 7
* Red Hat Enterprise Linux Server 6 a 7
* Debian GNU/Linux 8 a 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS a 18.04 LTS
* SUSE Linux Enterprise Server 12

#### <a name="32-bit"></a>32bitová

* Centos 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 a 9
* Ubuntu Linux 14.04 LTS a 16.04 LTS

## <a name="limitations"></a>Omezení

Řešení sledování změn v současné době nepodporuje následující položky:

* Rekurze pro sledování registru systému Windows
* Síťové souborové systémy
* Různé způsoby instalace
* *Soubory **EXE** pro systém Windows

Další omezení:

* Sloupec **Maximální velikost souboru** a hodnoty nejsou v aktuální implementaci použity.
* Pokud shromažďujete více než 2500 souborů v 30minutovém cyklu shromažďování, může dojít ke snížení výkonu řešení.
* Pokud je síťový provoz vysoký, může zobrazení záznamů o změně trvat až šest hodin.
* Pokud změníte konfiguraci v době, kdy je počítač vypnutý, může počítač účtovat změny, které patří k předchozí konfiguraci.

## <a name="known-issues"></a>Známé problémy

Řešení sledování změn se v současné době potýká s následujícími problémy:

* Aktualizace opravy hotfix se neshromažďují na počítačích Windows Server 2016 Core RS3.
* Linux ové daemons mohou vykazovat změněný stav, i když nedošlo k žádné změně. To je způsobeno `SvcRunLevels` tím, jak je pole zachyceno.

## <a name="network-requirements"></a>Síťové požadavky

Sledování změn konkrétně vyžaduje následující adresy. Komunikace s těmito adresami používá port 443.

|Veřejný partnerský vztah Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

## <a name="wildcard-recursion-and-environment-settings"></a>Nastavení zástupných symbolů, rekurzí a prostředí

Rekurze umožňuje zadat zástupné znaky pro zjednodušení sledování napříč adresáři a proměnných prostředí, které umožňují sledovat soubory napříč prostředími s více nebo dynamickými názvy jednotek. V následujícím seznamu jsou uvedeny běžné informace, které byste měli znát při konfiguraci rekurze:

* Pro sledování více souborů jsou vyžadovány zástupné znaky.
* Zástupné znaky a slouží pouze v posledním segmentu cesty,\\například c:\folder file* nebo /etc/*.conf.
* Pokud proměnná prostředí má neplatnou cestu, ověření proběhne úspěšně, ale tato cesta se nezdaří při spuštění zásob.
* Při nastavování cesty se vyhněte obecným cestám, protože tento typ nastavení může způsobit procházení příliš mnoha složek.

## <a name="change-tracking-data-collection-details"></a>Podrobnosti o shromažďování dat sledování změn

V následující tabulce je uvedena frekvence shromažďování dat pro typy změn. Pro každý typ je také aktualizován datový snímek aktuálního stavu alespoň každých 24 hodin:

| **Změnit typ** | **Frekvence** |
| --- | --- |
| Registr systému Windows | 50 minut |
| Soubor systému Windows | 30 minut |
| Linuxový soubor | 15 minut |
| Služby pro Windows | 10 sekund až 30 minut</br> Výchozí: 30 minut |
| Linuxové daemony | 5 minut |
| Software windows | 30 minut |
| Linuxsoftware | 5 minut |

V následující tabulce jsou uvedeny limity sledovaných položek pro sledování změn na počítač.

| **Prostředek** | **Limit**| **Poznámky** |
|---|---|---|
|File|500||
|Registr|250||
|Software windows|250|Neobsahuje softwarové opravy hotfix.|
|Balíčky linuxu|1250||
|Služby|250||
|Daemon|250||

Průměrné využití dat Log Analytics pro počítač pomocí sledování změn je přibližně 40 MB za měsíc. Tato hodnota je pouze aproximace a může se změnit na základě vašeho prostředí. Doporučujeme sledovat prostředí, abyste viděli přesné využití, které máte.

### <a name="windows-service-tracking"></a>Sledování služby systému Windows

Výchozí frekvence shromažďování služeb systému Windows je 30 minut. Chcete-li frekvenci nakonfigurovat, přejděte na **možnost Sledování změn**. V části **Upravit nastavení** na kartě **Služby systému Windows** je jezdec, který umožňuje změnit frekvenci shromažďování služeb systému Windows z až 10 sekund na až 30 minut. Přesuňte posuvník na požadovanou frekvenci a automaticky jej uloží.

![Jezdec služeb systému Windows](./media/change-tracking/windowservices.png)

Agent pouze sleduje změny, to optimalizuje výkon agenta. Nastavení vysoké prahové hodnoty může chybět změny, pokud služba vrátí do původního stavu. Nastavení frekvence na menší hodnotu umožňuje zachytit změny, které mohou být vynechány jinak.

> [!NOTE]
> Zatímco agent může sledovat změny až do intervalu 10 sekund, data stále trvá několik minut, které mají být zobrazeny na portálu. Změny během doby zobrazení na portálu jsou stále sledovány a zaznamenány.

### <a name="registry-key-change-tracking"></a>Sledování změn klíče registru

Účelem sledování změn klíčů registru je určit body rozšiřitelnosti, kde může být aktivován kód a malware třetích stran. V následujícím seznamu je uveden seznam předem nakonfigurovaných klíčů registru. Tyto klíče jsou nakonfigurovány, ale nejsou povoleny. Chcete-li sledovat tyto klíče registru, je nutné povolit každý z nich.

> [!div class="mx-tdBreakAll"]
> |Klíč registru | Účel |
> |---------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitoruje běžné položky automatického spuštění, které se zavěsují přímo do průzkumníka Windows a obvykle běží v procesu pomocí programu Explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Monitoruje skripty, které jsou spuštěny při spuštění.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Monitoruje skripty, které běží při vypnutí.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Monitoruje klíče, které jsou načteny před přihlášením uživatele k účtu systému Windows. Klíč se používá pro 32bitové programy spuštěné v 64bitových počítačích.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Monitoruje změny nastavení aplikace.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitoruje běžné položky automatického spuštění, které se zavěsují přímo do průzkumníka Windows a obvykle běží v procesu pomocí programu Explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Monitoruje běžné položky automatického spuštění, které se zavěsují přímo do průzkumníka Windows a obvykle běží v procesu pomocí programu Explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitoruje registraci obslužné rutiny překrytí ikon.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitoruje registraci obslužné rutiny překrytí ikon pro 32bitové programy spuštěné v 64bitových počítačích.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitoruje nové moduly plug-in objektů pomocníka prohlížeče pro aplikaci Internet Explorer. Slouží k přístupu k modelu DOCUMENT Object Model (DOM) aktuální stránky a k řízení navigace.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitoruje nové moduly plug-in objektů pomocníka prohlížeče pro aplikaci Internet Explorer. Slouží k přístupu k modelu DOCUMENT Object Model (DOM) aktuální stránky a k řízení navigace pro 32bitové programy spuštěné v 64bitových počítačích.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Monitoruje nová rozšíření aplikace Internet Explorer, například vlastní nabídky nástrojů a vlastní tlačítka panelu nástrojů.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Monitoruje nová rozšíření aplikace Internet Explorer, například vlastní nabídky nástrojů a vlastní tlačítka panelu nástrojů pro 32bitové programy spuštěné v 64bitových počítačích.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitoruje 32bitové ovladače spojené s wavemapper, wave1 a wave2, msacm.imaadpcm, .msadpcm, .msgsm610 a vidc. Podobně jako v sekci [ovladače] v systému. INI.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitoruje 32bitové ovladače spojené s wavemapper, wave1 a wave2, msacm.imaadpcm, .msadpcm, .msgsm610 a vidc pro 32bitové programy spuštěné na 64bitových počítačích. Podobně jako v sekci [ovladače] v systému. INI.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Monitoruje seznam známých nebo běžně používaných systémových knihoven DLL. Tento systém zabraňuje lidem ve využívání slabých oprávnění adresáře aplikací tím, že upustí ve verzích trojských koní systémových knihoven DLL.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Monitoruje seznam balíčků, které mohou přijímat oznámení o událostech od společnosti Winlogon, což je model podpory interaktivního přihlášení pro operační systém Windows.

## <a name="enable-change-tracking"></a><a name="onboard"></a>Povolit sledování změn

Chcete-li začít sledovat změny, musíte povolit řešení sledování změn. Existuje mnoho způsobů, jak na palubě stroje změnit sledování. Níže jsou uvedeny doporučené a podporované způsoby, jak na palubě řešení.

* [Z virtuálního počítače](automation-onboard-solutions-from-vm.md)
* [Z procházení více strojů](automation-onboard-solutions-from-browse.md)
* [Z vašeho účtu Automation](automation-onboard-solutions-from-automation-account.md)
* [S runbookem Azure Automation](automation-onboard-solutions.md)

## <a name="configure-change-tracking"></a>Konfigurace sledování změn

Informace o tom, jak do řešení zavést počítače, naleznete [v tématu Řešení automatizace přizapisu](automation-onboard-solutions-from-automation-account.md). Jakmile máte připojení počítače pomocí řešení Sledování změn, můžete nakonfigurovat položky ke sledování. Pokud povolíte sledování nového souboru nebo klíče registru, je povoleno pro sledování změn.

Pro sledování změn v souborech v systému Windows i Linuxu se používají hashy MD5 souborů. Tyto stavy hashe se pak používají ke zjištění, zda byla od posledního skladu provedena změna.

## <a name="enable-file-integrity-monitoring-in-azure-security-center"></a>Povolení monitorování integrity souborů v Azure Security Center

Azure Security Center přidal monitorování integrity souborů (FIM), které je postavené na Azure Change Tracking. Zatímco FIM monitoruje pouze soubory a registry, úplné řešení sledování změn také zahrnuje:

- Změny softwaru
- Služby systému Windows
- Linuxové daemony

Pokud jste již povolili FIM a chcete vyzkoušet úplné řešení sledování změn, je třeba provést následující kroky. Nastavení nejsou odebrány tímto procesem.

> [!NOTE]
> Povolení úplného řešení sledování změn může způsobit další poplatky, další informace naleznete v [tématu Automatizace ceny](https://azure.microsoft.com/pricing/details/automation/).

1. Odeberte monitorovací řešení tak, že přejdete do pracovního prostoru a vyhledáte ho v [seznamu nainstalovaných řešení monitorování](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions).
2. Kliknutím na název řešení otevřete jeho stránku souhrnu a poté klikněte na Odstranit, jak je podrobně popsáno v [části Odstranit monitorovací řešení](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).
3. Znovu povolte řešení přejdete na účet automatizace a **vyberete sledování změn** v části **Správa konfigurace**.
4. Potvrďte podrobnosti nastavení pracovního prostoru a klepněte na **tlačítko Povolit**.

## <a name="configure-file-content-change-tracking"></a>Konfigurace sledování změn obsahu souboru

Obsah můžete zobrazit před a po změně souboru pomocí sledování změn obsahu souboru. Tato funkce je k dispozici pro soubory systému Windows a Linux. Pro každou změnu souboru je obsah souboru uložen v účtu úložiště. Soubor je zobrazen před a po změně, vřadit nebo vedle sebe. Další informace najdete [v tématu Zobrazení obsahu sledovaného souboru](change-tracking-file-contents.md).

![zobrazení změn v souboru](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="configure-windows-registry-keys-to-track"></a>Konfigurace klíčů registru systému Windows ke sledování

Pomocí následujících kroků nakonfigurujte sledování klíčů registru v počítačích se systémem Windows:

1. V účtu Automation vyberte v části **Správa konfigurace**možnost **Změnit sledování** . Klikněte na **Upravit nastavení** (symbol ozubeného kola).
2. Na stránce Sledování změn vyberte **registr systému Windows**a pak klepněte na **tlačítko + Přidat** a přidejte nový klíč registru ke sledování.
3. V doplňku **Přidat registr systému Windows pro sledování změn**zadejte informace o klíči ke sledování a klepněte na tlačítko **Uložit**.

|Vlastnost  |Popis  |
|---------|---------|
|Povoleno     | Určuje, zda je nastavení použito.        |
|Název položky     | Popisný název klíče registru, který má být sledován.        |
|Skupina     | Název skupiny pro logické seskupení klíčů registru.        |
|Klíč registru systému Windows   | Cesta ke kontrole klíče registru. Příklad: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="configure-file-tracking-on-windows"></a>Konfigurace sledování souborů v systému Windows

Ke konfiguraci sledování souborů v počítačích se systémem Windows postupujte takto:

1. V účtu Automation vyberte v části **Správa konfigurace**možnost **Změnit sledování** . Klikněte na **Upravit nastavení** (symbol ozubeného kola).
2. Na stránce Sledování změn vyberte **Soubory systému Windows**a kliknutím na **+ Přidat** přidejte nový soubor ke sledování.
3. V nabídce **Přidat soubor systému Windows pro sledování změn**zadejte informace o souboru, který chcete sledovat, a klepněte na tlačítko **Uložit**.

|Vlastnost  |Popis  |
|---------|---------|
|Povoleno     | True, pokud je použito nastavení a False jinak.        |
|Název položky     | Popisný název souboru, který má být sledován.        |
|Skupina     | Název skupiny pro logické seskupení souborů.        |
|Zadat cestu     | Cesta ke kontrole souboru, například **c:\temp\\\*.txt**<br>Můžete také použít proměnné prostředí, `%winDir%\System32\\\*.*`například .       |
|Rekurze     | True, pokud rekurze se používá při hledání položky, které mají být sledovány a False jinak.        |
|Nahrát obsah souboru pro všechna nastavení| True nahrát obsah souboru na sledované změny, a False jinak.|

## <a name="configure-file-tracking-on-linux"></a>Konfigurace sledování souborů na Linuxu

Ke konfiguraci sledování souborů v počítačích s Linuxem použijte následující kroky:

1. V účtu Automation vyberte v části **Správa konfigurace**možnost **Změnit sledování** . Klikněte na **Upravit nastavení** (symbol ozubeného kola).
2. Na stránce Sledování změn vyberte **Linux Files**, pak kliknutím na **+ Přidat** přidejte nový soubor ke sledování.
3. V nabídce **Přidat linuxový soubor pro sledování změn**zadejte informace o souboru nebo adresáři, které chcete sledovat, a klepněte na tlačítko **Uložit**.

|Vlastnost  |Popis  |
|---------|---------|
|Povoleno     | Určuje, zda je nastavení použito.        |
|Název položky     | Popisný název souboru, který má být sledován.        |
|Skupina     | Název skupiny pro logické seskupení souborů.        |
|Zadat cestu     | Cesta ke kontrole souboru. Například: "/etc/*.conf"       |
|Typ cesty     | Typ položky, která má být sledována, možné hodnoty jsou Soubor a adresář.        |
|Rekurze     | Určuje, jestli se při hledání položky, která se má sledovat, používá rekurze.        |
|Použít sudo     | Toto nastavení určuje, jestli se při kontrole položky používá sudo.         |
|Odkazy     | Toto nastavení určuje, jak se při procházení adresářů zpracovávají symbolické odkazy.<br> **Ignorovat** - Ignoruje symbolické odkazy a neobsahuje nazývané soubory/adresáře.<br>**Postupujte** - Sleduje symbolické odkazy během rekurze a také obsahuje soubory / adresáře odkazuje.<br>**Správa** - Sleduje symbolické odkazy a umožňuje měnit vrácený obsah.     |
|Nahrát obsah souboru pro všechna nastavení| Zapne nebo vypne u sledovaných změn nahrávání obsahu souboru. Dostupné možnosti: **True** nebo **False**.|

> [!NOTE]
> Možnost Spravovat se nedoporučuje. Načítání obsahu souborů se nepodporuje.

## <a name="search-logs"></a>Vyhledávací protokoly

Můžete provést různá vyhledávání v protokolech pro záznamy změn. Po otevření stránky Sledování změn klikněte na **Log Analytics**, otevře se stránka Protokoly. Následující tabulka obsahuje ukázkové hledání záznamů změn shromážděných tímto řešením:

|Dotaz  |Popis  |
|---------|---------|
|ConfigurationData<br>&#124; kde configDataType == "WindowsServices" a SvcStartupType == "Auto"<br>&#124; kde SvcState == "Zastaveno"<br>&#124; shrnout arg_max(TimeGenerated, *) podle SoftwareName, Počítač         | Zobrazuje nejnovější záznamy inventáře služeb systému Windows, které byly nastaveny na automaticky, ale byly vykázány jako zastavené.<br>Výsledky jsou omezeny na nejnovější záznam pro tento SoftwareName a Computer      |
|ConfigurationChange<br>&#124; kde ConfigChangeType == "Software" a ChangeCategory == "Odstraněno"<br>&#124; pořadí podle TimeGenerated desc|Zobrazuje záznamy změn odebraný software.|

## <a name="alert-on-changes"></a>Upozornění na změny

Klíčovou funkcí sledování změn je upozornění na stav konfigurace a všechny změny stavu konfigurace hybridního prostředí. Následující příklad ukazuje, že soubor **C:\windows\system32\drivers\etc\hosts** byl v počítači změněn. Tento soubor je důležitý, protože systém Windows jej používá k překladu názvů hostitelů na adresy IP. Tato operace má přednost před službou DNS a může způsobit problémy s připojením nebo přesměrování provozu na škodlivé nebo jinak nebezpečné weby.

![Graf zobrazující změnu souboru hosts](./media/change-tracking/changes.png)

Chcete-li tuto změnu dále analyzovat, přejděte na položku Log search from clicking **Log Analytics**. Po přihlášení k hledání v protokolu vyhledejte změny `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`obsahu v souboru Hosts pomocí dotazu . Tento dotaz vyhledá změny, které zahrnovaly změnu obsahu souboru pro soubory, jejichž plně kvalifikovaná cesta obsahuje slovo "hosts". Můžete také požádat o konkrétní soubor změnou části cesty na `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`plně kvalifikovaný formulář (například ).

Jakmile dotaz vrátí požadované výsledky, otevřete stránku pro vytvoření výstrahy klepnutím na **tlačítko Nové pravidlo výstrahy** ve vyhledávání protokolu. Můžete také přejít na toto prostředí prostřednictvím **Azure Monitor** na webu Azure Portal. 

Zkontrolujte dotaz znovu a upravte logiku výstrah. V takovém případě chcete, aby se výstraha aktivovala, pokud je zjištěna i jedna změna ve všech počítačích v prostředí.

![Obrázek zobrazující dotaz na změnu pro sledování změn v souboru hosts](./media/change-tracking/change-query.png)

Po nastavení logiky stavu přiřaďte skupiny akcí k provádění akcí v reakci na aktivaci výstrahy. V tomto případě jsem nastavil e-maily, které mají být odeslány a lístek ITSM, který má být vytvořen.  Mnoho dalších užitečných akcí lze také přijmout, jako je například spuštění funkce Azure, automatizace runbook, webhook nebo aplikace logiky.

![Obrázek, který konfiguruje skupinu akcí tak, aby upozorňovala na změnu](./media/change-tracking/action-groups.png)

Po nastavení všech parametrů a logiky můžeme výstrahu použít pro prostředí.

### <a name="alert-suggestions"></a>Návrhy výstrah

Zatímco upozornění na změny v souboru hosts je jedna dobrá aplikace výstrah pro sledování změn nebo data zásob, existuje mnoho dalších scénářů pro upozorňování, včetně případů definovaných spolu s jejich ukázkovými dotazy v následující části.

|Dotaz  |Popis  |
|---------|---------|
|ConfigurationChange <br>&#124;, kde ConfigChangeType == "Files" a\\FileSystemPath obsahuje " c: windows\\system32\\ovladače\\"|Užitečné pro sledování změn systémových kritických souborů|
|ConfigurationChange <br>&#124;, kde FieldsChanged obsahuje "FileContentChecksum" a FileSystemPath ==\\\\"c:\\windows\\system32\\ovladače atd hostitelé"|Užitečné pro sledování změn konfiguračních souborů klíčů|
|ConfigurationChange <br>&#124; kde ConfigChangeType == "WindowsServices" a SvcName obsahuje "w3svc" a SvcState == "Zastaveno"|Užitečné pro sledování změn systémových kritických služeb|
|ConfigurationChange <br>&#124; kde ConfigChangeType == "Daemons" a SvcName obsahuje "ssh" a SvcState != "Spuštěno"|Užitečné pro sledování změn systémových kritických služeb|
|ConfigurationChange <br>&#124; kde ConfigChangeType == "Software" a ChangeCategory == "Přidáno"|Užitečné pro prostředí, která potřebují uzamčenou konfiguraci softwaru|
|ConfigurationData <br>&#124; kde SoftwareName obsahuje "Monitorovací agent" a CurrentVersion != "8.0.11081.0"|Užitečné pro zobrazení počítačů, které mají nainstalovanou zastaralou nebo nekompatibilní verzi softwaru. Hlásí poslední hlášený stav konfigurace, nikoli změny.|
|ConfigurationChange <br>&#124; kde RegistryKey ==\\\\@"HKEY_LOCAL_MACHINE SOFTWARE Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Užitečné pro sledování změn klíčových antivirových klíčů|
|ConfigurationChange <br>&#124;, kde registrobsahuje\\@"HKEY_LOCAL_MACHINE\\SYSTEM\\\\CurrentControlSet\\Services SharedAccess\\Parameters Firewall"| Užitečné pro sledování změn nastavení brány firewall|

## <a name="next-steps"></a>Další kroky

Další informace o používání řešení najdete v kurzu o sledování změn:

> [!div class="nextstepaction"]
> [Řešení potíží se změnami ve vašem prostředí](automation-tutorial-troubleshoot-changes.md)

* Pomocí [hledání protokolů v protokolech Azure Monitor](../log-analytics/log-analytics-log-searches.md) uobrazte podrobná data sledování změn.
