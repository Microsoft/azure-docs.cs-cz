---
title: Řešení potíží s vysokým využitím procesoru pro virtuální počítače Azure s Windows
description: .
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: mnanda
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: 9971cc26-916f-4e49-83cd-71eca74804f0
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 9/24/2020
ms.author: mnanda
ms.openlocfilehash: ffac5ac4d1a8143590e1d72aaafc8a02d6ab04ca
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977251"
---
# <a name="troubleshoot-high-cpu-issues-for-azure-windows-virtual-machines"></a>Řešení potíží s vysokým využitím procesoru pro virtuální počítače Azure s Windows

## <a name="summary"></a>Shrnutí

K problémům s výkonem dochází v různých operačních systémech nebo aplikacích a každý problém vyžaduje k řešení potíží jedinečný přístup. Většina těchto problémů se rozchází kolem procesoru, paměti, sítě a vstupu a výstupu (v/v) jako klíčových umístění, kde dochází k problému. Každá z těchto oblastí generuje různé příznaky (někdy současně) a vyžaduje jinou diagnostiku a řešení.

Tento článek popisuje problémy s vysokým využitím procesoru, ke kterým dochází na Azure Virtual Machines (virtuální počítače), na kterých běží operační systém Windows.

### <a name="high-cpu-issues-on-azure-windows-vms"></a>Problémy s vysokým využitím procesoru na virtuálních počítačích Azure s Windows

Kromě potíží s latencí v/v a v síti vyžadují řešení potíží s CPU a pamětí stejné nástroje a kroky jako na místních serverech. Jedním z nástrojů, které Microsoft obvykle podporuje, je PerfInsights (k dispozici pro Windows i Linux). PerfInsights může v uživatelsky přívětivé sestavě poskytnout diagnostiku osvědčených postupů pro virtuální počítače Azure. PerfInsights je také nástroj pro obálky, který může pomáhat shromažďovat data Perfmon, Xperf a Netmon v závislosti na příznacích, které jsou vybrány v nástroji.

Většina vašich stávajících nástrojů pro řešení potíží s výkonem, jako je Perfmon nebo procmon, které se používají pro místní servery, budou fungovat na virtuálních počítačích Azure s Windows. PerfInsights je ale výslovně navržený pro virtuální počítače Azure, aby poskytovaly další přehledy, včetně osvědčených postupů Azure, osvědčených postupů SQL, grafů s vysokým rozlišením, PROCESORů a paměťových karet a tak dále.

Bez ohledu na to, jestli běží jako User-Mode nebo v režimu jádra, musí mít každé vlákno aktivního procesu cykly procesoru ke spuštění kódu, ze kterého je sestavený. Mnohé problémy přímo souvisejí s úlohou. Druh zatížení, které existuje na serverovém disku spotřeba prostředků, včetně CPU.

#### <a name="common-factors"></a>Běžné faktory

V situaci s vysokým využitím procesoru jsou běžné tyto faktory:

- Nedávné změny kódu nebo nasazení, které se většinou vztahují na aplikace jako Internetová informační služba (IIS), Microsoft SharePoint, Microsoft SQL Server nebo aplikace třetích stran.

- Poslední aktualizace, která může souviset s aktualizací na úrovni operačního systému, nebo kumulativní aktualizace a opravy na úrovni aplikace.

- Dotaz na změny nebo zastaralé indexy. SQL Server a aplikace na datové vrstvě Oracle mají taky optimalizaci plánu dotazů jako jiný faktor. Změny dat nebo chybějící vhodné indexy můžou způsobit, že se několik dotazů bude náročných na výpočetní výkon.

- Specifické pro virtuální počítače Azure. Existují určité procesy, jako je například agent monitorování, agent MMA nebo klient zabezpečení, které mohou způsobit vysokou spotřebu procesoru. Tyto procesy se musí zobrazit v perspektivě konfigurace nebo známých problémů.

## <a name="troubleshoot-the-issue"></a>Řešení problému

Tento článek se zaměřuje na izolaci problematického procesu. Další analýza bude specifická pro proces, který využívá vysokou spotřebu procesoru.

Například pokud je proces SQL Server (sqlservr.exe), další kroky budou analyzovat, který dotaz využíval největší cykly procesoru v určitém časovém období.

### <a name="scope-the-issue"></a>Určení rozsahu problému

Tady je pár otázek, které je potřeba vyřešit při odstraňování problému:

- Existuje nějaký vzor problému? Například problém s vysokým využitím procesoru nastane v určitou dobu každý den, týden nebo měsíc? Pokud ano, můžete tento problém korelovat s úlohou, sestavou nebo přihlášením uživatele?

- Byl problém s vysokým využitím procesoru od poslední změny kódu spuštěn? Použili jste aktualizaci ve Windows nebo v aplikaci?

- Zvýšil se problém s vysokým využitím procesoru po změně úlohy, jako je například zvýšení počtu uživatelů, vyššího datového toku dat nebo větší počet sestav?

- V případě Azure se při potížích s vysokým využitím procesoru spustí některá z následujících podmínek?

  - Po nedávném opakovaném nasazení nebo restartování
  - Když se změní typ SKU nebo virtuální počítač
  - Při přidání nového rozšíření
  - Po provedení změn nástroje pro vyrovnávání zatížení

### <a name="azure-caveats"></a>Upozornění Azure

Pochopte své úlohy. Po výběru virtuálního počítače můžete podceňují skutečnou počty virtuálních PROCESORů (vCPU), když se podíváte na celkové měsíční náklady na hostování. Pokud je vaše zatížení náročné na výpočetní výkon, výběr menší skladové jednotky virtuálního počítače, který má jednu nebo dvě vCPU, může způsobit problémy s úlohou. Otestujte různé konfigurace pro vaše úlohy, abyste zjistili, jaké nejlepší výpočetní funkce se vyžadují.

Existují některé řady virtuálních počítačů, například B (režim shlukování), které se doporučují pro zajištění kvality (QA) a testování. Používání těchto řad v produkčním prostředí omezuje možnosti výpočetní funkce po vyčerpání kreditů procesoru.

Pro známé aplikace, jako jsou SQL Server, Oracle, Vzdálená plocha, Windows Virtual Desktop, IIS nebo SharePoint, existují články s osvědčenými postupy Azure, které obsahují doporučení pro minimální konfiguraci pro tyto úlohy.

### <a name="ongoing-high-cpu-issues"></a>Probíhající problémy s vysokým využitím procesoru

Pokud k problému dochází nyní, jedná se o nejlepší možnost zachycení trasování procesu, abyste zjistili, jaký je způsob problému. K vyhledání tohoto procesu můžete použít stávající nástroje, které jste používali pro místní Windows servery. Podporu Azure pro virtuální počítače Azure doporučuje následující nástroje.

### <a name="perfinsights"></a>PerfInsights

PerfInsights je doporučeným nástrojem z podpory Azure při potížích s výkonem virtuálních počítačů. Je navržený tak, aby pokryl osvědčené postupy a vyhrazené karty analýzy pro grafy procesoru, paměti a v/v s vysokým rozlišením. Můžete ji spustit pomocí Azure Portal, nebo v rámci virtuálního počítače. Data můžete sdílet s týmem podpory Azure.

#### <a name="run-perfinsights"></a>Spustit PerfInsights

PerfInsights je k dispozici pro operační [systém Windows](./how-to-use-perfinsights.md) i [Linux](./how-to-use-perfinsights-linux.md) . V případě systému Windows jsou zde možnosti.

#### <a name="run-and-analyze-reports-through-azure-portal"></a>Spouštění a analýza sestav pomocí Azure Portal

Při [instalaci prostřednictvím Azure Portal](./performance-diagnostics.md)ve skutečnosti nainstaluje na virtuální počítač rozšíření. Uživatelé můžou nainstalovat PerfInsights jako rozšíření tak, že se předají přímo do [rozšíření v okně virtuálního počítače](./performance-diagnostics-vm-extension.md)a pak zvolí možnost Diagnostika výkonu.

#### <a name="azure-portal-option-1"></a>Azure Portal možnost 1

Přejděte do okna virtuálního počítače a vyberte možnost **Diagnostika výkonu** . Zobrazí se výzva k instalaci možnosti (používá rozšíření) na virtuálním počítači, pro který jste ho vybrali.

  ![Nainstalovat diagnostiku výkonu](./media/troubleshoot-high-cpu-issues-azure-windows-vm/1-install-performance-diagnostics.png)

#### <a name="azure-portal-option-2"></a>Možnost Azure Portal 2

Přejděte k části **Diagnostika a řešení problémů** v okně virtuálního počítače a vyhledejte **problémy s výkonem virtuálního počítače**.

  ![Řešení potíží s výkonem virtuálních počítačů](./media/troubleshoot-high-cpu-issues-azure-windows-vm/2-troubleshoot-vm-performance-issues.png)

Pokud vyberete možnost **odstraňování potíží**, instalační obrazovka PerfInsights se načte.

Pokud vyberete **instalovat**, instalace nabízí různé možnosti shromažďování.

  ![Analýza výkonu](./media/troubleshoot-high-cpu-issues-azure-windows-vm/3-performance-analysis.png)

Číslované možnosti v snímku obrazovky se vztahují na následující komentáře:

1. Pro možnost **vysoký procesor**vyberte **Analýza výkonu** nebo **Upřesnit**.

2. Přidáte-li zde příznaky, budou přidány do sestavy, která vám pomůže sdílet informace s podporou Azure.

3. Vyberte dobu trvání shromažďování dat. Pro možnost vysoký procesor vyberte aspoň 15 minut nebo více. V režimu Azure Portal můžete shromažďovat až 15 minut dat. Pro delší období shromažďování musíte spustit program jako spustitelný soubor v rámci virtuálního počítače.

4. Pokud jste požádali o podporu Azure ke shromáždění těchto dat, můžete sem přidat číslo lístku. Toto pole je volitelné.

5. Toto pole vyberte, pokud chcete přijmout licenční smlouvu s koncovým uživatelem (EULA).

6. Toto pole vyberte, pokud máte v úmyslu tuto sestavu zpřístupnit týmu podpory Azure, který pomáhá v tomto případě.

Sestava je uložena na jednom z účtů úložiště v rámci vašeho předplatného. Je k dispozici pro zobrazení a stažení později.

#### <a name="run-perfinsights-from-within-the-vm"></a>Spuštění PerfInsights z virtuálního počítače

Tato metoda se dá použít, pokud máte v úmyslu spouštět PerfInsights po delší dobu. [Článek PerfInsights](./how-to-use-perfinsights.md#how-do-i-run-perfinsights) poskytuje podrobný návod k různým příkazům a příznakům, které jsou nutné ke spuštění PerfInsights jako spustitelného souboru. Pro účely vysokého využití procesoru budete potřebovat jeden z následujících režimů:

- Rozšířený scénář

  - `PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics`

- Scénář zpomalit virtuální počítač (výkon)

  - `PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>`

Výstup příkazu bude ve stejné složce, do které jste uložili spustitelný soubor PerfInsights.

#### <a name="what-to-look-for-in-the-report"></a>Co hledat v sestavě

Po spuštění sestavy závisí umístění obsahu na tom, zda bylo spuštěno prostřednictvím Azure Portal nebo jako spustitelného souboru. Pro kteroukoli z možností získáte přístup k vygenerované složce protokolu nebo ke stažení (Pokud Azure Portal) místně pro účely analýzy.

### <a name="run-through-the-azure-portal"></a>Spuštění prostřednictvím Azure Portal

  ![Diagnostika výkonu s vysokým dopadem](./media/troubleshoot-high-cpu-issues-azure-windows-vm/4-high-impact-performance-diagnostics.png)

  ![Stáhnout sestavu](./media/troubleshoot-high-cpu-issues-azure-windows-vm/5-download-report.png)

#### <a name="run-from-within-vm"></a>Spuštění z virtuálního počítače

Struktura složek by měla vypadat podobně jako na následujících obrázcích:

  ![Vybrat výstup](./media/troubleshoot-high-cpu-issues-azure-windows-vm/6-select-output.png)

  ![Struktura složek](./media/troubleshoot-high-cpu-issues-azure-windows-vm/7-folder-structure.png)

1. Jakékoli další kolekce, jako je Perfmon, Xperf, Netmon, protokol SMB, protokoly událostí atd., najdete ve složce Output.

1. Skutečná sestava společně s analýzou a doporučeními.

1. V případě výkonu (VMslow) i Pokročilá sestava shromažďuje informace o nástroji **perfmon** po dobu trvání běhu PerfInsights.

1. Protokoly událostí zobrazují rychlé zobrazení užitečných podrobností o selhání na úrovni systému nebo procesů.

#### <a name="where-to-start"></a>Kde začít

Otevřete sestavu PerfInsights. Karta **nálezy** protokoluje všechny odlehlé hodnoty z pohledu spotřeby prostředků. Pokud existují instance s vysokým využitím procesoru, karta **nálezy** ji zařadí do kategorií s vysokým dopadem nebo s negativním dopadem na střední hodnotu.

  ![Prostředky úrovně dopadu](./media/troubleshoot-high-cpu-issues-azure-windows-vm/8-impact-level-resources.png)

Podobně jako v předchozím příkladu byl PerfInsights spuštěn po dobu 30 minut. Po polovinu času byl zvýrazněný proces vyčerpán procesor na vyšší straně. Pokud byl stejný proces spuštěn po celou dobu shromažďování, úroveň dopadu se změnila na **Vysoká**.

Pokud rozbalíte **nálezovou** událost, zobrazí se několik klíčových podrobností. Na kartě jsou uvedeny procesy v sestupném pořadí, podle **průměrné spotřeby procesoru** a ukazuje, zda proces souvisí se systémem, aplikací ve vlastnictví společnosti Microsoft (SQL, IIS) nebo procesem třetí strany.

#### <a name="more-details"></a>Další podrobnosti

V rámci **procesoru** je vyhrazená subtab, která se dá použít pro podrobnou analýzu vzorů, na jádro nebo pro proces.

Karta **spotřebitelé na nejvyšší úrovni** má dva oddělené oddíly, a tady můžete zobrazit statistiky pro jednotlivé procesory. Návrh aplikace je často Single-Threaded nebo se přijedná k jednomu procesoru. V tomto scénáři se jeden nebo několik jader spouští na 100 procent, zatímco ostatní jádra běží na očekávaných úrovních. Tyto scénáře jsou složitější, protože průměrný procesor na serveru se zdá běžet podle očekávání, ale procesy, které jsou připnuté na jádra s vysokým využitím, budou pomalejší, než se očekávalo.

  ![vysoké využití procesoru](./media/troubleshoot-high-cpu-issues-azure-windows-vm/9-high-cpu-usage.png)

Druhá část (stejně důležitá) je **horní dlouhodobě dlouhotrvajících uživatelů procesoru**. V této části najdete jak podrobnosti procesu, tak i jejich vzor využití procesoru. Seznam se seřadí s vysokým průměrem uživatelů procesoru v horní části.

  ![Dlouhodobě běžící spotřebitelé procesoru](./media/troubleshoot-high-cpu-issues-azure-windows-vm/10-top-long-running-cpu-consumers.png)

Tyto dvě karty budou dostatečně k dispozici, aby bylo možné nastavit cestu k dalšímu postupu pro řešení potíží. V závislosti na procesu, který řídí vysoký stav procesoru, budete muset vyřešit otázky, které se dřív požadovaly. Procesy jako SQL Server (sqlservr.exe) nebo služba IIS (w3wp.exe) vyžadují konkrétní přechod k podrobnostem o změnách dotazů nebo kódu, které způsobují tuto podmínku. U systémových procesů, jako je WMI nebo Lsass.exe, je nutné provést jinou cestu.

U procesů souvisejících s virtuálními počítači Azure, jako jsou spustitelné soubory rozšíření RDAgent, OMS a monitoring, možná budete muset opravit nové buildu nebo verzi tím, že získáte pomoc od týmu podpory Azure.

### <a name="perfmon"></a>Perfmon

**Perfmon** je jedním z nejstarších nástrojů pro řešení potíží se zdroji na Windows serveru. Neposkytuje jasné hlášení, které obsahuje doporučení nebo zjištění. Místo toho vyžaduje, aby uživatel prozkoumal shromážděná data a použil konkrétní filtr pod různými kategoriemi čítačů.

PerfInsights shromažďuje Perfmon jako dodatečný protokol pro VMSlow a pokročilé scénáře. Perfmon se ale dá shromažďovat nezávisle a má tyto další výhody:

- Dá se shromažďovat vzdáleně.

- Dá se naplánovat pomocí **úloh**.

- Dá se shromažďovat po delší dobu trvání nebo v nepřetržitém režimu pomocí funkce pro uvedení na více systému.

Vezměte v úvahu stejný příklad, který je zobrazený v PerfInsights, a zjistěte, jak nástroj Perfmon zobrazuje tato data. Požadované kategorie čítače jsou následující:

- Informace o procesoru > > času procesoru _Total

- Proces >% ProcessorTime > všechny instance

#### <a name="where-to-start"></a>Kde začít

Názvy výstupního souboru programu Perfmon mají `.blg` rozšíření. Tyto soubory můžete shromáždit nezávisle nebo pomocí PerfInsights. Pro tuto diskuzi budete používat nástroj Perfmon `.blg` , který je součástí PerfInsights dat a který byl shromážděn podle předchozího příkladu.

V programu Perfmon nejsou k dispozici žádné výchozí sestavy připravené pro uživatele. Existují různá zobrazení, která mění typ grafu, ale filtr procesu (nebo práce potřebná k identifikaci procesů příčinou) je ruční.

> [!NOTE]
> [Nástroj PAL](https://github.com/clinthuffman/PAL) může využívat `.blg` soubory a generovat podrobné sestavy.

Začněte tím, že vyberete kategorii **Přidat čítače** .

1. V části **Dostupné čítače**vyberte čítač **% ProcessorTime** v kategorii **informace o procesoru** .

1. Vyberte **_Total**, což vám poskytne statistiku všech kombinovaných jader.

1. Vyberte **Přidat**. V okně se zobrazí **% ProcessorTime** v části  **přidané čítače**.

  ![Přidat čas procesoru](./media/troubleshoot-high-cpu-issues-azure-windows-vm/11-add-processor-time.png)

Po načtení čítačů se v časovém rámci shromažďování zobrazí grafy trendů řádků. Můžete vybrat nebo vymazat čítače. Zatím jste přidali jenom jeden čítač.

  ![Nastavení sledování výkonu](./media/troubleshoot-high-cpu-issues-azure-windows-vm/12-performance-monitor-1.png)

Každý čítač bude mít **průměrnou**, **minimální**a **maximální** hodnotu. Zaměřte se na **průměrné** i **maximální** hodnoty, protože průměrná hodnota se může lišit v závislosti na délce trvání sběru dat. Pokud se aktivita s vysokým PROCESORem zobrazila po dobu 10 minut, ale celková kolekce byla 40 minut, bude průměrná hodnota mnohem nižší.

Graf předchozí trend ukazuje, že **Celkový procesor** byl téměř 80% po dobu přibližně 15 minut.

#### <a name="identify-the-process"></a>Identifikace procesu

Zjistili jsme, že u serveru byla po určitou dobu spotřeba s vysokým využitím procesoru, ale ještě jsme neidentifikovali ovladač. Na rozdíl od použití PerfInsights musíte v tomto případě ručně vyhledat proces příčinou.

Pro tuto úlohu musíte vymazat nebo odebrat čítače ( **% ProcessorTime** ), které jste dříve přidali, a pak přidat novou kategorii:

- Proces >% ProcessorTime > všechny instance

Tato kategorie načte čítače pro všechny procesy spuštěné v daném čase.

  ![Přidání čítačů](./media/troubleshoot-high-cpu-issues-azure-windows-vm/13-add-counters.png)

V typickém provozním počítači mohou být spuštěny stovky nebo procesy. Proto může chvíli trvat, než se vymaže všechny čítače, u kterých se zdá, že mají graf s nízkým nebo plochým trendem.

Chcete-li zrychlit tento proces, použijte zobrazení **histogramu** a změňte typ zobrazení na **spojnici** na **histogram**, což vám poskytne pruhový graf. Zjistíte, že je snazší zvolit procesy, u kterých dochází k vysokému využití procesoru během shromažďování.

Vzhledem k tomu, že vždy bude pruhový **celkem**, zaměřte se na pruhy, které zobrazují vysokou míru vyčerpání. Můžete odstranit ostatní pruhy a vyčistit tak zobrazení. Nyní se posune zpět na zobrazení **řádků** .

  ![Indikátory sledování výkonu](./media/troubleshoot-high-cpu-issues-azure-windows-vm/14-performance-monitor-2.png)

Nyní je snazší zachytit proces příčinou. Ve výchozím nastavení jsou **maximální** a **minimální** hodnoty násobky čísla buď v jádrech na serveru, nebo v vláknech procesu.

  ![Výsledky sledování výkonu](./media/troubleshoot-high-cpu-issues-azure-windows-vm/15-performance-monitor-3.png)

Seznam dostupných nástrojů nekončí na PerfInsights pro Perfmon. Máte přístup k jiným nástrojům, jako je například **ProcessMonitor** (procmon) nebo **Xperf**. K dispozici je mnoho nástrojů třetích stran, které mohou používat podle potřeby.

### <a name="azure-monitoring-tools"></a>Monitorovací nástroje Azure

Virtuální počítače Azure mají spolehlivé metriky, které zahrnují základní informace, jako je procesor, vstupně-výstupní operace sítě a vstupně-výstupní bajty. Pro pokročilé metriky, jako je například Azure Monitor, budete muset provést pouze několik výběrů ke konfiguraci a použití účtu úložiště, který určíte.

#### <a name="basic-default-counters"></a>Základní (výchozí) čítače

  ![Metriky grafů](./media/troubleshoot-high-cpu-issues-azure-windows-vm/16-chart-metrics.png)

#### <a name="enabling-azure-monitor"></a>Povolení Azure Monitor

Po povolení Azure Monitor metriky nainstaluje software na virtuální počítač rozšíření a pak začne shromažďovat podrobné metriky, mezi které patří čítače výkonu.

  ![Účet úložiště diagnostiky](./media/troubleshoot-high-cpu-issues-azure-windows-vm/17-diagnostics-storage-account.png)

**Základní** kategorie čítače jsou nastaveny jako **výchozí**. Můžete ale také nastavit **vlastní** kolekci.

  ![Čítače výkonu](./media/troubleshoot-high-cpu-issues-azure-windows-vm/18-performance-counters.png)

Až budou nastavení povolená, můžete tyto čítače **hosta** zobrazit v části **metriky** . Pokud metriky dosáhnou určité prahové hodnoty, můžete také nastavit **výstrahy** (včetně e-mailových zpráv).

  ![Obor názvů metrik](./media/troubleshoot-high-cpu-issues-azure-windows-vm/19-metrics-namespace.png)

Další informace o tom, jak používat Azure monitor ke správě virtuálních počítačů Azure, najdete v tématu [monitorování virtuálních počítačů Azure pomocí Azure monitor](../../azure-monitor/insights/monitor-vm-azure.md).

### <a name="reactive-troubleshooting"></a>Reaktivní odstraňování potíží

Pokud k problému došlo, je nutné zjistit, co způsobilo problém s vysokým využitím procesoru na prvním místě. Reaktivní potřebujete pomoc podporují může být obtížné. Režim shromažďování dat nebude vhodný, protože k problému již došlo.

Pokud byl tento problém jednorázový, může být obtížné určit, která aplikace ji způsobila. Pokud byl virtuální počítač Azure nakonfigurovaný tak, aby používal OMS nebo jiné sledování diagnostiky, můžete získat přehled o tom, co způsobilo problém.

Pokud pracujete se opakujícím se vzorem, Shromážděte data v době, kdy k problému pravděpodobně dojde dál.

PerfInsights ještě nemá **plánovanou možnost spuštění** . Nástroj Perfmon však lze spustit a naplánovat prostřednictvím příkazového řádku.

### <a name="logman-command"></a>Logman – příkaz

Příkaz **logman create Counter** se používá ke spuštění kolekce Perfmon prostřednictvím příkazového řádku, k jeho naplánování pomocí **Správce úloh**nebo k jeho vzdálenému spuštění.

**Ukázka** (zahrnuje režim vzdálené kolekce)

`Logman create counter LOGNAME -u DOMAIN\USERNAME * -f bincirc -v mmddhhmm -max 300 -c "\\SERVERNAME\LogicalDisk(*)\*" "\\SERVERNAME\Memory\*" "\\SERVERNAME\Network Interface(*)\*" "\\SERVERNAME\Paging File(*)\*" "\\SERVERNAME\PhysicalDisk(*)\*" "\\SERVERNAME\Process(*)\*" "\\SERVERNAME\Redirector\*" "\\SERVERNAME\Server\*" "\\SERVERNAME\System\*" "\\SERVERNAME\Terminal Services\*" "\\SERVERNAME\Processor(*)\*" "\\SERVERNAME\Cache\*" -si 00:01:00`

Logman.exe taky můžete spustit z partnerského počítače Azure, který je ve stejné virtuální síti.

Další informace o těchto parametrech najdete v tématu [logman create Counter](/windows-server/administration/windows-commands/logman-create-counter).

Po shromáždění dat Perfmon během nastalování tohoto problému jsou zbývající kroky pro analýzu dat stejné, jak je popsáno výše.

## <a name="conclusion"></a>Závěr

V případě problémů s výkonem je důležité pochopit vaše úlohy, abyste mohli problém vyřešit. Možnosti na různých SKU virtuálních počítačů a různých možnostech diskového úložiště je nutné vyhodnotit tím, že zachováte fokus na produkční úlohy. Proces testování řešení na různých virtuálních počítačích vám může usnadnit provádění tohoto nejlepšího rozhodnutí.

Vzhledem k tomu, že se operace uživatele a množství dat liší, vždy udržujte vyrovnávací paměť v možnostech výpočetní funkce, sítě a vstupně-výstupních operací virtuálního počítače. Všechny náhlé změny v úlohách teď neovlivňují.

Pokud předvedete brzké zvýšení zatížení, přejděte k vyšší SKU, která má vyšší výpočetní výkon. Pokud bude zatížení náročné na výpočetní výkon, vyberte skladové jednotky virtuálních počítačů.