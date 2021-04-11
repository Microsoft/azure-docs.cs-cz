---
title: Přehled Azure Automation Change Tracking a inventáře
description: Tento článek popisuje funkci Change Tracking a inventáře, která vám pomůže identifikovat změny softwaru a služeb Microsoftu ve vašem prostředí.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/22/2021
ms.topic: conceptual
ms.openlocfilehash: ed29def305bfa33a0a947a331775de89275e5f7f
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220862"
---
# <a name="change-tracking-and-inventory-overview"></a>Přehled Change Tracking a inventáře

Tento článek vás seznámí s Change Tracking a inventarizací v Azure Automation. Tato funkce sleduje změny virtuálních počítačů hostovaných v Azure, místním prostředí a dalších cloudových prostředích, které vám pomůžou identifikovat problémy v provozu a v životním prostředí pomocí softwaru spravovaného správcem distribuce balíčků. Položky, které jsou sledovány pomocí Change Tracking a inventáře, zahrnují:

- Software systému Windows
- Software pro Linux (balíčky)
- Soubory Windows a Linux
- Klíče registru Windows
- Služby společnosti Microsoft
- Procesy démon systému Linux

> [!NOTE]
> Pokud chcete sledovat změny vlastností Azure Resource Manager, přečtěte si téma [historie změn](../../governance/resource-graph/how-to/get-resource-changes.md)grafu prostředků Azure.

Change Tracking a inventář využívá [Azure Security Center sledování integrity souborů (FIM)](../../security-center/security-center-file-integrity-monitoring.md) k prohlédnutí souborů operačního systému a aplikací a registru systému Windows. I když FIM monitoruje tyto entity, Change Tracking a inventář nativně sleduje:

- Změny softwaru
- Služby společnosti Microsoft
- Procesy démon systému Linux

Povolení všech funkcí, které jsou součástí Change Tracking a inventáře, může způsobit další poplatky. Než budete pokračovat, přečtěte si téma [ceny služby Automation](https://azure.microsoft.com/pricing/details/automation/) a [ceny Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

Change Tracking a inventář předávají data do protokolů Azure Monitor a Tato shromážděná data se ukládají do Log Analyticsho pracovního prostoru. Funkce monitoring integrity souborů (FIM) je dostupná jenom v případě, že je **Azure Defender pro servery** povolený. Další informace najdete v tématu Azure Security Center [ceny](../../security-center/security-center-pricing.md) . FIM nahraje data do stejného Log Analytics pracovního prostoru jako ten vytvořený pro ukládání dat z Change Tracking a inventáře. Doporučujeme monitorovat svůj propojený pracovní prostor Log Analytics, abyste mohli sledovat přesné využití. Další informace o analýze využití dat protokolů Azure Monitor najdete v tématu [Správa využití a nákladů](../../azure-monitor/logs/manage-cost-storage.md).

Počítače připojené k pracovnímu prostoru Log Analytics používají [agenta Log Analytics](../../azure-monitor/agents/log-analytics-agent.md) ke shromažďování dat o změnách nainstalovaného softwaru, služeb Microsoftu, registru a souborů systému Windows a démonech systému Linux na monitorovaných serverech. Když jsou data k dispozici, agent je pošle do Azure Monitor protokolů pro zpracování. Protokoly Azure Monitor aplikují logiku na přijatá data, zaznamenává je a zpřístupňují pro účely analýzy.

> [!NOTE]
> Change Tracking a inventář vyžaduje propojení pracovního prostoru Log Analytics s vaším účtem Automation. Konečný seznam podporovaných oblastí najdete v tématu [mapování pracovních prostorů Azure](../how-to/region-mappings.md). Mapování oblastí neovlivňují možnost spravovat virtuální počítače v samostatné oblasti z účtu Automation.

Jako poskytovatel služeb můžete mít k [Azure Lighthouse](../../lighthouse/overview.md)k dispozici několik klientů pro zákazníky. Azure Lighthouse umožňuje provádět operace ve velkém měřítku napříč několika klienty Azure Active Directory (Azure AD) najednou a provádět úlohy správy, jako je Change Tracking a inventarizace v rámci těch tenantů, za které zodpovídáte. Change Tracking a inventář může spravovat počítače ve více předplatných ve stejném tenantovi nebo napříč klienty pomocí [Azure delegované správy prostředků](../../lighthouse/concepts/azure-delegated-resource-management.md).

## <a name="current-limitations"></a>Aktuální omezení

Change Tracking a inventář nepodporují nebo mají následující omezení:

- Rekurze pro sledování registru Windows
- Síťové systémy souborů
- Různé metody instalace
- *soubory **. exe** uložené ve Windows
- Sloupec **maximální velikost souboru** a hodnoty jsou v aktuální implementaci nepoužitelné.
- Pokud se pokusíte shromáždit více než 2500 souborů v cyklu shromažďování, Change Tracking a výkon inventáře může být snížený.
- Pokud je síťový provoz vysoký, může zobrazení změn záznamů trvat až šest hodin.
- Pokud konfiguraci upravíte v době, kdy je počítač nebo server vypnutý, může dojít k odeslání změn, které patří do předchozí konfigurace.
- Shromažďování aktualizací hotfix na počítačích s Windows serverem 2016 Core RS3.
- Procesy démon systému Linux mohou zobrazovat změněný stav, i když nedošlo k žádné změně. K tomuto problému dochází kvůli způsobu, jakým `SvcRunLevels` jsou data v tabulce Azure monitor [ConfigurationChange](/azure/azure-monitor/reference/tables/configurationchange) zapsána.

## <a name="supported-operating-systems"></a>Podporované operační systémy

Change Tracking a inventář se podporují ve všech operačních systémech, které splňují požadavky agenta Log Analytics. Seznam verzí operačních systémů Windows a Linux, které jsou aktuálně podporované agentem Log Analytics, najdete v části [podporované operační systémy](../../azure-monitor/agents/agents-overview.md#supported-operating-systems) .

Informace o požadavcích klienta na TLS 1,2 najdete v tématu [vynucení TLS 1,2 pro Azure Automation](../automation-managing-data.md#tls-12-enforcement-for-azure-automation).

### <a name="python-requirement"></a>Požadavek Pythonu

Change Tracking a inventář podporuje jenom Python2. Pokud Váš počítač používá distribuce, který ve výchozím nastavení neobsahuje Python 2, musíte ho nainstalovat. Následující vzorové příkazy instalují Python 2 v různých distribuce.

- Red Hat, CentOS, Oracle: `yum install -y python2`
- Ubuntu, Debian: `apt-get install -y python2`
- SUSE `zypper install -y python2`

Spustitelný soubor python2 musí mít alias na *Python*.

## <a name="network-requirements"></a>Požadavky sítě

Podrobné informace o portech, adresách URL a dalších podrobných informacích o sítích vyžadovaných pro Change Tracking a inventář najdete v [Azure Automation konfiguraci sítě](../automation-network-configuration.md#update-management-and-change-tracking-and-inventory) .

## <a name="enable-change-tracking-and-inventory"></a>Povolení řešení Change Tracking a Inventory

Change Tracking a inventář můžete povolit následujícími způsoby:

- Z [účtu Automation](enable-from-automation-account.md) pro jeden nebo víc počítačů Azure a mimo Azure.

- Ručně pro počítače mimo Azure, včetně počítačů nebo serverů zaregistrovaných u [serverů s podporou ARC Azure](../../azure-arc/servers/overview.md). Pro hybridní počítače doporučujeme nainstalovat agenta Log Analytics pro Windows tím, že nejprve připojíte počítač k [serverům s podporou ARC Azure](../../azure-arc/servers/overview.md)a pak pomocí Azure Policy přiřadíte předdefinované zásady pro [nasazení agenta Log Analytics k počítačům se systémem *Linux* nebo *Windows* Azure ARC](../../governance/policy/samples/built-in-policies.md#monitoring) . Pokud plánujete taky monitorovat počítače s Azure Monitor pro virtuální počítače, použijte raději iniciativu [Enable Azure monitor pro virtuální počítače](../../governance/policy/samples/built-in-initiatives.md#monitoring) .

- Pro jeden virtuální počítač Azure ze [stránky virtuálního počítače](enable-from-vm.md) v Azure Portal. Tento scénář je k dispozici pro virtuální počítače se systémy Linux a Windows.

- Pro [více virtuálních počítačů Azure](enable-from-portal.md) je můžete vybrat ze stránky virtuální počítače v Azure Portal.

## <a name="tracking-file-changes"></a>Sledování změn souborů

Pro sledování změn v souborech v systémech Windows i Linux používá Change Tracking a inventáře hodnoty hash MD5 souborů. Funkce používá hodnoty hash k detekci, zda byly provedeny změny od posledního inventáře.

## <a name="tracking-file-content-changes"></a>Sledování změn obsahu souboru

Change Tracking a inventář vám umožní zobrazit obsah souboru systému Windows nebo Linux. Pro každou změnu souboru Change Tracking a inventář ukládá obsah souboru do [Azure Storage účtu](../../storage/common/storage-account-create.md). Když sledujete soubor, můžete jeho obsah zobrazit před nebo po změně. Obsah souboru lze zobrazit buď vloženě, nebo vedle sebe.

![Zobrazit změny v souboru](./media/overview/view-file-changes.png)

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

- Zástupné znaky můžete použít jenom v posledním segmentu cesty k souboru, například **c:\folder \\ File** _ nebo _ */etc/*. conf * *.

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
|Registr|250|
|Software Windows (nezahrnuje opravy hotfix) |250|
|Balíčky Linux|1250|
|Služby|250|
|Procesy démon|250|

Průměrné využití dat Log Analytics počítači pomocí Change Tracking a inventáře je přibližně 40 MB za měsíc v závislosti na vašem prostředí. Díky funkci využití a odhad nákladů v pracovním prostoru Log Analytics můžete zobrazit data ingestovaná pomocí Change Tracking a inventáře v grafu využití. Toto zobrazení dat slouží k vyhodnocení využití vašich dat a určení toho, jak má na faktuře vliv. Podívejte [se na informace o využití a odhadované náklady](../../azure-monitor/logs/manage-cost-storage.md#understand-your-usage-and-estimate-costs).

### <a name="microsoft-service-data"></a>Data služby společnosti Microsoft

Výchozí četnost shromažďování pro služby společnosti Microsoft je 30 minut. Frekvenci můžete nakonfigurovat pomocí posuvníku na kartě **služby společnosti Microsoft** v části **Upravit nastavení**.

![Posuvník služeb Microsoftu](./media/overview/windowservices.png)

Pro optimalizaci výkonu Log Analytics agent sledovat pouze změny. Nastavením vysoké prahové hodnoty může dojít ke změnám, pokud se služba vrátí do původního stavu. Nastavení četnosti na menší hodnotu umožňuje zachytit změny, které by mohly být chybět jinak.

> [!NOTE]
> I když může agent sledovat změny až do intervalu 10 sekund, data stále trvá několik minut, než se zobrazí v Azure Portal. Změny, ke kterým dojde v době, kdy se má zobrazit na portálu, jsou pořád sledovány a protokolovány.

## <a name="support-for-alerts-on-configuration-state"></a>Podpora pro výstrahy týkající se stavu konfigurace

Klíčovou funkcí Change Tracking a inventáře jsou výstrahy týkající se změn stavu konfigurace hybridního prostředí. K dispozici je mnoho užitečných akcí, které je možné aktivovat v reakci na výstrahy. Například akce v Azure Functions, Runbooky automatizace, webhookech a podobně. Upozornění na změny v souboru **c:\Windows\System32\drivers\etc\hosts** pro počítač je jedním z užitečných výstrah pro Change Tracking a data inventáře. K dispozici je mnoho dalších scénářů pro upozorňování, včetně scénářů dotazu definovaných v následující tabulce.

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

- Pokud chcete povolit z účtu Automation, přečtěte si téma [povolení Change Tracking a inventáře z účtu Automation](enable-from-automation-account.md).

- Pokud chcete povolit z Azure Portal, přečtěte si téma [povolení Change Tracking a inventáře z Azure Portal](enable-from-portal.md).

- Pokud chcete povolit sadu Runbook, přečtěte si téma [povolení Change Tracking a inventáře ze sady Runbook](enable-from-runbook.md).

- Pokud ho chcete povolit z virtuálního počítače Azure, přečtěte si téma [povolení Change Tracking a inventáře z virtuálního počítače Azure](enable-from-vm.md).
