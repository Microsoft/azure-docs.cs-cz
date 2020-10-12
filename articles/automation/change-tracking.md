---
title: Přehled Azure Automation Change Tracking a inventáře
description: Tento článek popisuje funkci Change Tracking a inventáře, která vám pomůže identifikovat změny softwaru a služeb Microsoftu ve vašem prostředí.
services: automation
ms.subservice: change-inventory-management
ms.date: 08/17/2020
ms.topic: conceptual
ms.openlocfilehash: 2fe92942e263cf53b9827ccbcb13a2d7bafc367c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88511044"
---
# <a name="change-tracking-and-inventory-overview"></a>Přehled Change Tracking a inventáře

Tento článek vás seznámí s Change Tracking a inventarizací v Azure Automation. Tato funkce sleduje změny ve virtuálních počítačích a serverové infrastruktuře, které vám pomůžou identifikovat problémy v provozu a v okolním prostředí pomocí softwaru spravovaného správcem distribuce balíčků. Položky, které jsou sledovány pomocí Change Tracking a inventáře, zahrnují: 

- Software systému Windows
- Software pro Linux (balíčky)
- Soubory Windows a Linux
- Klíče registru Windows
- Služby společnosti Microsoft
- Procesy démon systému Linux

> [!NOTE]
> Pokud chcete sledovat změny vlastností Azure Resource Manager, přečtěte si téma [historie změn](../governance/resource-graph/how-to/get-resource-changes.md)grafu prostředků Azure.

Change Tracking a inventář získává data z Azure Monitor. Virtuální počítače připojené k pracovním prostorům Log Analytics používají agenta Log Analytics ke shromažďování dat o změnách nainstalovaného softwaru, služeb Microsoftu, registru a souborů systému Windows a démonech systému Linux na monitorovaných serverech. Když jsou data k dispozici, agent je pošle Azure Monitor ke zpracování. Azure Monitor používá logiku pro přijatá data, zaznamenává je a zpřístupňuje je.

> [!NOTE]
> Change Tracking a inventář vyžaduje propojení pracovního prostoru Log Analytics s vaším účtem Automation. Konečný seznam podporovaných oblastí najdete v tématu [mapování pracovních prostorů Azure](./how-to/region-mappings.md). Mapování oblastí neovlivňují možnost spravovat virtuální počítače v samostatné oblasti z účtu Automation.

Change Tracking a inventář aktuálně nepodporují následující položky:

- Rekurze pro sledování registru Windows
- Síťové systémy souborů
- Různé metody instalace
- *soubory **. exe** pro Windows

Další omezení:

- Sloupec **maximální velikost souboru** a hodnoty jsou v aktuální implementaci nepoužitelné.
- Pokud shromáždíte více než 2500 souborů v cyklu shromažďování, Change Tracking a výkon inventáře může být snížený.
- Pokud je síťový provoz vysoký, může zobrazení změn záznamů trvat až šest hodin.
- Pokud upravíte konfiguraci v době, kdy je počítač vypnutý, může počítač publikovat změny patřící do předchozí konfigurace.

Change Tracking a inventarizace v současné době dochází k následujícím problémům:

- Aktualizace oprav hotfix nejsou shromažďovány na počítačích s Windows serverem 2016 Core RS3.

- Procesy démon systému Linux mohou zobrazovat změněný stav, i když nedošlo k žádné změně. K tomuto problému dochází kvůli způsobu, jakým `SvcRunLevels` jsou zachycena data v protokolu Azure monitor [ConfigurationChange](/azure/azure-monitor/reference/tables/configurationchange) .

## <a name="supported-operating-systems"></a>Podporované operační systémy

Change Tracking a inventář se podporují ve všech operačních systémech, které splňují požadavky agenta Log Analytics. Oficiální verze operačního systému jsou Windows Server 2008 SP1 nebo novější a Windows 7 SP1 nebo novější. Tato funkce je také podporována v řadě operačních systémů Linux. Seznam podporovaných operačních systémů najdete v tématu [Přehled agenta Log Analytics](../azure-monitor/platform/log-analytics-agent.md).

Informace o požadavcích klienta na TLS 1,2 najdete v tématu [vynucení TLS 1,2 pro Azure Automation](automation-managing-data.md#tls-12-enforcement-for-azure-automation).

## <a name="network-requirements"></a>Požadavky sítě

Change Tracking a inventář konkrétně vyžadují síťové adresy uvedené v další tabulce. Komunikace s těmito adresami používá port 443.

|Veřejný partnerský vztah Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net | *. blob.core.usgovcloudapi.net|
|*.azure-automation.net | *. azure-automation.us|

## <a name="change-tracking-and-inventory-user-interface"></a>Change Tracking a uživatelské rozhraní inventáře

Pomocí Change Tracking a inventáře v Azure Portal můžete zobrazit souhrnné informace o změnách monitorovaných počítačů. Tato funkce je k dispozici tak, že vyberete jednu z možností pro přidání virtuálních počítačů pro **změnu sledování** nebo **inventarizaci** ve **správě konfigurace** v účtu Automation.  

![Řídicí panel Change Tracking](./media/change-tracking/change-tracking-dash01.png)

Rozevírací seznamy jsou v horní části řídicího panelu k dispozici k omezení grafu sledování změn a podrobné informace na základě typu změny a rozsahů času. Můžete také kliknout na graf a přetáhnout ho a vybrat vlastní časový rozsah. 

Kliknutím na změnu nebo událost můžete zobrazit podrobnosti. Dostupné typy změn jsou:

- Události
- Procesy démon
- Soubory
- Registry
- Software
- Služby společnosti Microsoft

Jednotlivé změny můžete přidat, upravit nebo odebrat. Následující příklad ukazuje změnu typu spuštění služby z ruční na auto.

![Podrobnosti o Change Tracking a inventáři](./media/change-tracking/change-tracking-details.png)

## <a name="fim-support-in-azure-security-center"></a>Podpora FIM v Azure Security Center

Change Tracking a inventář využívá [Azure Security Center sledování integrity souborů (FIM)](../security-center/security-center-file-integrity-monitoring.md). I když FIM monitoruje jenom soubory a registry, zahrnuje kompletní funkce Change Tracking a inventáře také sledování pro:

- Změny softwaru
- Služby společnosti Microsoft
- Procesy démon systému Linux

> [!NOTE]
> Povolení úplných funkcí Change Tracking a inventáře může způsobit další poplatky. Podívejte se na téma [ceny služby Automation](https://azure.microsoft.com/pricing/details/automation/). Je možné odstranit FIM ze [seznamu nainstalovaných řešení monitorování](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions) dostupných v Azure Portal. Viz [Odebrání řešení monitorování](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).

## <a name="tracking-of-file-changes"></a>Sledování změn souborů

Pro sledování změn v souborech v systémech Windows i Linux používá Change Tracking a inventáře hodnoty hash MD5 souborů. Funkce používá hodnoty hash k detekci, zda byly provedeny změny od posledního inventáře.

## <a name="tracking-of-file-content-changes"></a>Sledování změn obsahu souborů

Change Tracking a inventář vám umožní zobrazit obsah souboru systému Windows nebo Linux. Pro každou změnu souboru Change Tracking a inventář ukládá obsah souboru do [Azure Storage účtu](../storage/common/storage-account-create.md). Když sledujete soubor, můžete jeho obsah zobrazit před nebo po změně. Obsah souboru lze zobrazit buď vloženě, nebo vedle sebe. 

![Zobrazit změny v souboru](./media/change-tracking/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>Sledování klíčů registru

Change Tracking a inventář umožňuje sledovat změny klíčů registru Windows. Monitorování vám umožní určit body rozšiřitelnosti, které mohou aktivovat kód a malware třetí strany. V následující tabulce jsou uvedeny předem nakonfigurované klíče registru (ale ne povolené). Chcete-li sledovat tyto klíče, je nutné povolit každou z nich.

> [!div class="mx-tdBreakAll"]
> |Klíč registru | Účel |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Monitoruje skripty spouštěné při spuštění.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Monitoruje skripty, které se spouštějí při vypnutí.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Sleduje klíče, které jsou načteny před přihlášením uživatele k účtu systému Windows. Klíč se používá pro 32 aplikace běžící na 64 počítačích.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Monitoruje změny nastavení aplikace.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitoruje obslužné rutiny místní nabídky, které se připojovat přímo do Průzkumníka Windows, a obvykle spouštějí v procesu **explorer.exe**.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Monitoruje obslužné rutiny kopírovacího zavěšení, které se připojovat přímo do Průzkumníka Windows, a obvykle spouštějí v procesu **explorer.exe**.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitory pro registraci obslužné rutiny překrytí ikon
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitory pro registraci obslužné rutiny překrytí ikon pro 32 aplikace běžící na 64 počítačích.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitory pro nové objekty pomocníka pro prohlížeč pro Internet Explorer. Slouží k přístupu k model DOM (Document Object Model) (DOM) aktuální stránky a k ovládání navigace.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitory pro nové objekty pomocníka pro prohlížeč pro Internet Explorer. Slouží k přístupu k model DOM (Document Object Model) (DOM) aktuální stránky a k řízení navigace pro 32 aplikace běžící na 64 počítačích.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Sleduje nové rozšíření aplikace Internet Explorer, například nabídky vlastních nástrojů a vlastní tlačítka panelu nástrojů.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Sleduje nové rozšíření aplikace Internet Explorer, například nabídky vlastních nástrojů a vlastní tlačítka panelu nástrojů pro 32 aplikace běžící na 64 počítačích.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitoruje 32 ovladačů přidružených k wavemapper, wave1 a wave2, MSACM. imaadpcm,. msadpcm,. msgsm610 a vidc. Podobně jako oddíl [Drivers] v souboru **system.ini** .
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitoruje 32 ovladačů přidružených k wavemapper, wave1 a wave2, MSACM. imaadpcm,. msadpcm,. msgsm610 a vidc pro 32 aplikace spuštěné v počítačích s 64--bit. Podobně jako oddíl [Drivers] v souboru **system.ini** .
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Monitoruje seznam známých nebo běžně používaných systémových knihoven DLL. Monitorování brání lidem v zneužití slabých oprávnění adresáře aplikace tím, že vyřadí verze systémových knihoven DLL pro trojského koně.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Monitoruje seznam balíčků, které mohou přijímat oznámení o událostech z **winlogon.exe**, což je model podpory interaktivního přihlašování pro systém Windows.

## <a name="recursion-support"></a>Podpora rekurze

Change Tracking a inventář podporuje rekurzi, která umožňuje určit zástupné znaky, aby bylo možné zjednodušit sledování v adresářích. Rekurze také poskytuje proměnné prostředí, které umožňují sledovat soubory v různých prostředích s více nebo dynamickými názvy jednotek. Následující seznam obsahuje běžné informace, které byste měli znát při konfiguraci rekurze:

- Pro sledování více souborů jsou vyžadovány zástupné znaky.

- Zástupné znaky můžete použít jenom v posledním segmentu cesty k souboru, například **c:\folder \\ File*** nebo **/etc/*. conf**.

- Pokud proměnná prostředí má neplatnou cestu, ověřování je úspěšné, ale cesta během provádění selže.

- Při nastavování cesty byste se měli vyhnout obecným názvům cest, protože tento typ nastavení může způsobit procházení příliš velkého počtu složek.

## <a name="change-tracking-and-inventory-data-collection"></a>Change Tracking a shromažďování dat inventáře

Následující tabulka zobrazuje četnost shromažďování dat pro typy změn, které podporuje Change Tracking a inventář. Pro každý typ se datový snímek aktuálního stavu aktualizuje také alespoň každých 24 hodin.

| **Změnit typ** | **Frekvence** |
| --- | --- |
| Registr systému Windows | 50 minut |
| Soubor Windows | 30 minut |
| Soubor Linux | 15 minut |
| Služby společnosti Microsoft | 10 sekund až 30 minut</br> Výchozí: 30 minut |
| Procesy démon systému Linux | 5 minut |
| Software systému Windows | 30 minut |
| Software pro Linux | 5 minut |

V následující tabulce jsou uvedené limity sledovaných položek na počítač pro Change Tracking a inventář.

| **Prostředek** | **Počtu** |
|---|---|---|
|Soubor|500|
|Registry|250|
|Software Windows (nezahrnuje opravy hotfix) |250|
|Balíčky Linux|1250|
|Služby|250|
|Procesy démon|250|

Průměrné využití dat Log Analytics počítači pomocí Change Tracking a inventáře je přibližně 40 MB za měsíc v závislosti na vašem prostředí. Díky funkci využití a odhad nákladů v pracovním prostoru Log Analytics můžete zobrazit data ingestovaná pomocí Change Tracking a inventáře v grafu využití. Toto zobrazení dat slouží k vyhodnocení využití vašich dat a určení toho, jak má na faktuře vliv. Podívejte [se na informace o využití a odhadované náklady](../azure-monitor/platform/manage-cost-storage.md#understand-your-usage-and-estimate-costs).

### <a name="microsoft-service-data"></a>Data služby společnosti Microsoft

Výchozí četnost shromažďování pro služby společnosti Microsoft je 30 minut. Frekvenci můžete nakonfigurovat pomocí posuvníku na kartě **služby společnosti Microsoft** v části **Upravit nastavení**.

![Posuvník služeb Microsoftu](./media/change-tracking/windowservices.png)

Pro optimalizaci výkonu Log Analytics agent sledovat pouze změny. Nastavením vysoké prahové hodnoty může dojít ke změnám, pokud se služba vrátí do původního stavu. Nastavení četnosti na menší hodnotu umožňuje zachytit změny, které by mohly být chybět jinak.

> [!NOTE]
> I když může agent sledovat změny až do intervalu 10 sekund, data stále trvá několik minut, než se zobrazí v Azure Portal. Změny, ke kterým dojde v době, kdy se má zobrazit na portálu, jsou pořád sledovány a protokolovány.

## <a name="support-for-alerts-on-configuration-state"></a>Podpora pro výstrahy týkající se stavu konfigurace

Klíčovou funkcí Change Tracking a inventáře jsou výstrahy týkající se změn stavu konfigurace hybridního prostředí. K dispozici je mnoho užitečných akcí, které je možné aktivovat v reakci na výstrahy, například akce v Azure Functions, Runbooky Automation, webhookech a podobně. Upozornění na změny v souboru **c:\Windows\System32\drivers\etc\hosts** pro počítač je jedním z užitečných výstrah pro Change Tracking a data inventáře. K dispozici je mnoho dalších scénářů pro upozorňování, včetně scénářů dotazu definovaných v následující tabulce.

|Dotaz  |Description  |
|---------|---------|
|ConfigurationChange <br>&#124;, kde ConfigChangeType = = "Files" a FileSystemPath obsahuje "c \\ : \\ ovladače Windows system32 \\ \\ "|Hodí se ke sledování změn souborů důležitých pro systém.|
|ConfigurationChange <br>&#124;, kde FieldsChanged obsahuje "FileContentChecksum" a FileSystemPath = = "c \\ : \\ ovladače systému Windows system32 atd. \\ \\ \\ "|Hodí se ke sledování úprav konfiguračních souborů klíčů.|
|ConfigurationChange <br>&#124;, kde ConfigChangeType = = "WindowsServices" a SvcName obsahuje "W3SVC" a SvcState = = "zastaveno"|Hodí se ke sledování změn pro důležité systémové služby.|
|ConfigurationChange <br>&#124;, kde ConfigChangeType = = "démoni" a SvcName obsahuje "SSH" a SvcState! = "Running"|Hodí se ke sledování změn pro důležité systémové služby.|
|ConfigurationChange <br>&#124; WHERE ConfigChangeType = = "software" a ChangeCategory = = "přidáno"|Užitečné pro prostředí, která potřebují uzamčené softwarové konfigurace.|
|ConfigurationData <br>&#124;, kde software obsahuje "agent monitorování" a CurrentVersion! = "8.0.11081.0"|Užitečné pro zobrazení, které počítače mají nainstalovanou zastaralou nebo nekompatibilní verzi softwaru. Tento dotaz oznamuje poslední nahlášený stav konfigurace, ale neoznamuje změny.|
|ConfigurationChange <br>&#124; WHERE RegistryKey = = @ "HKEY_LOCAL_MACHINE \\ software \\ Microsoft \\ Windows \\ CurrentVersion \\ QualityCompat"| Hodí se ke sledování změn v důležitých antivirových klíčích.|
|ConfigurationChange <br>&#124;, kde RegistryKey obsahuje @ "HKEY_LOCAL_MACHINE \\ System \\ CurrentControlSet \\ Services \\ SharedAccess \\ Parameters \\ FirewallPolicy"| Hodí se ke sledování změn nastavení brány firewall.|

## <a name="next-steps"></a>Další kroky

- Pokud chcete tuto funkci povolit z účtu Automation, přečtěte si téma [povolení Change Tracking a inventáře z účtu Automation](automation-enable-changes-from-auto-acct.md).

- Pokud chcete funkci povolit procházením Azure Portal, přečtěte si téma [povolení Change Tracking a inventáře z Azure Portal](automation-enable-changes-from-browse.md).

- Pokud chcete tuto funkci povolit ze sady Runbook, přečtěte si téma [povolení Change Tracking a inventáře ze sady Runbook](automation-enable-changes-from-runbook.md).

- Pokud chcete tuto funkci povolit z virtuálního počítače Azure, přečtěte si téma [povolení Change Tracking a inventáře z virtuálního počítače Azure](automation-enable-changes-from-vm.md).
