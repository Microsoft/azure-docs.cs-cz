---
title: Jak používat PerfInsights v Microsoft Azure| Dokumenty společnosti Microsoft
description: Dozvíte se, jak pomocí PerfInsights řešit problémy s výkonem virtuálních aplikací v systému Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: dcscontentpm
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 783b479dd3e5f429516799d7d3ea82f363cac2ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250124"
---
# <a name="how-to-use-perfinsights"></a>Použití PerfInsights

[PerfInsights](https://aka.ms/perfinsightsdownload) je samoobslužný diagnostický nástroj, který shromažďuje a analyzuje diagnostická data a poskytuje sestavu, která vám pomůže vyřešit problémy s výkonem virtuálních strojů windows v Azure. PerfInsights lze spustit na virtuálních počítačích jako samostatný nástroj, přímo z portálu pomocí [diagnostiky výkonu pro virtuální počítače Azure](performance-diagnostics.md)nebo instalací rozšíření [virtuálního počítače Azure Performance Diagnostics .](performance-diagnostics-vm-extension.md)

Pokud dochází k problémům s výkonem s virtuálními počítači, před kontaktováním podpory spusťte tento nástroj.

## <a name="supported-troubleshooting-scenarios"></a>Podporované scénáře řešení potíží

PerfInsights může shromažďovat a analyzovat několik druhů informací. Následující části popisují běžné scénáře.

### <a name="quick-performance-analysis"></a>Rychlá analýza výkonu

Tento scénář shromažďuje konfiguraci disku a další důležité informace, včetně:

-   Protokoly událostí

-   Stav sítě pro všechna příchozí a odchozí připojení

-   Nastavení konfigurace sítě a brány firewall

-   Seznam úloh pro všechny aplikace, které jsou aktuálně spuštěny v systému

-   Nastavení konfigurace databáze serveru Microsoft SQL Server (pokud je virtuální modul identifikován jako server se systémem SQL Server)

-   Čítače spolehlivosti úložiště

-   Důležité opravy hotfix systému Windows

-   Instalované ovladače filtrů

Jedná se o pasivní kolekci informací, které by neměly mít vliv na systém. 

>[!Note]
>Tento scénář je automaticky zahrnuta v každém z následujících scénářů:

### <a name="benchmarking"></a>Srovnávání

Tento scénář spustí test benchmark [diskspd](https://github.com/Microsoft/diskspd) (IOPS a MBPS) pro všechny jednotky, které jsou připojeny k virtuálnímu počítače. 

> [!Note]
> Tento scénář může ovlivnit systém a neměl by být spuštěn v systému živé výroby. V případě potřeby spusťte tento scénář ve vyhrazeném okně údržby, abyste se vyhnuli problémům. Zvýšené zatížení, které je způsobeno trasování nebo srovnávací test může nepříznivě ovlivnit výkon virtuálního počítače.
>

### <a name="performance-analysis"></a>Analýza výkonu

Tento scénář spustí trasování [čítače výkonu](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) pomocí čítačů, které jsou zadány v souboru RuleEngineConfig.json. Pokud je virtuální počítač identifikován jako server se systémem SQL Server, spustí se trasování čítače výkonu. Činí tak pomocí čítačů, které se nacházejí v souboru RuleEngineConfig.json. Tento scénář také zahrnuje data diagnostiky výkonu.

### <a name="azure-files-analysis"></a>Analýza souborů Azure

Tento scénář spustí zachycení čítače výkonu speciální výkon spolu se trasování sítě. Zachycení zahrnuje všechny čítače klienta Server Message Block (SMB). Níže jsou uvedeny některé klíčové čítače výkonu sdílené složky SMB, které jsou součástí sběru:

| **Typ**     | **Čítač sdílených složek klienta SMB** |
|--------------|-------------------------------|
| IOPS         | Žádosti o data/s             |
|              | Číst požadavky/s             |
|              | Zapsat požadavky/s            |
| Latence      | Vg. sec/Požadavek na data         |
|              | Vg. sec/Čtení                 |
|              | Vg. sec/Zápis                |
| Velikost vi      | Vzdusičné bajty/požadavek na data       |
|              | Vznětové bajty/čtení               |
|              | Vzdusičné bajty/zápis              |
| Propustnost   | Datové bajty/s                |
|              | Přečtené bajty/s                |
|              | Zapsané bajty/s               |
| Délka fronty | Vg. Délka fronty čtení        |
|              | Vg. Délka fronty zápisu       |
|              | Vproti. Délka datové fronty        |

### <a name="advanced-performance-analysis"></a>Pokročilá analýza výkonu

Při spuštění rozšířené analýzy výkonu vyberete trasování spustit paralelně. Pokud chcete, můžete je spustit všechny (Čítač výkonu, Xperf, Síť a StorPort).  

> [!Note]
> Tento scénář může ovlivnit systém a neměl by být spuštěn v systému živé výroby. V případě potřeby spusťte tento scénář ve vyhrazeném okně údržby, abyste se vyhnuli problémům. Zvýšené zatížení, které je způsobeno trasování nebo srovnávací test může nepříznivě ovlivnit výkon virtuálního počítače.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Jaký druh informací shromažďuje PerfInsights?

Shromažďují se informace o konfiguraci virtuálních počítačů s Windows, disků nebo fondů úložiště, čítačích výkonu, protokolech a různých trasováních. Záleží na scénáři výkonu, který používáte. Podrobnosti jsou uvedené v následující tabulce:

|Shromažďovaná data                              |  |  | Scénáře výkonu |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                               | Rychlá analýza výkonu | Srovnávání | Analýza výkonu | Analýza souborů Azure | Pokročilá analýza výkonu |
| Informace z protokolů událostí       | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Systémové informace                | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Mapa svazku                        | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Mapa disku                          | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Spouštění úloh                     | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Čítače spolehlivosti úložiště      | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Informace o úložišti               | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Výstup Fsutil                     | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Informace o ovladači filtru                | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Výstup Netstat                    | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Konfigurace sítě             | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Konfigurace brány firewall            | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Konfigurace SQL Serveru          | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Sledování diagnostiky výkonu *  | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Trasování čítače výkonu **      |                            |                                    | Ano                      |                      | Ano                  |
| Trasování čítače SMB **              |                            |                                    |                          | Ano                  |                      |
| Trasování čítače serveru SQL Server **       |                            |                                    | Ano                      |                      | Ano                  |
| Trasování Xperf                       |                            |                                    |                          |                      | Ano                  |
| Trasování StorPort                    |                            |                                    |                          |                      | Ano                  |
| Trasování sítě                     |                            |                                    |                          | Ano                  | Ano                  |
| Sledování benchmarku Diskspd ***       |                            | Ano                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Sledování diagnostiky výkonu (*)

Spustí modul založený na pravidlech na pozadí pro shromažďování dat a diagnostiku probíhajících problémů s výkonem. V současné době jsou podporována následující pravidla:

- Pravidlo HighCpuUsage: Detekuje vysoké období využití procesoru a zobrazuje hlavní spotřebitele využití procesoru během těchto období.
- Pravidlo HighDiskUsage: Zjišťuje vysoké období využití disku na fyzických discích a zobrazuje hlavní odběratelé využití disku během těchto období.
- Pravidlo HighResolutionDiskMetric: Zobrazuje metriky latence VOPS, propustnosti a vstupně-výstupních operací na 50 milisekund pro každý fyzický disk. Pomáhá rychle identifikovat období omezení disku.
- HighMemoryUsage pravidlo: Zjistí vysoké období využití paměti a zobrazuje hlavní odběratelé využití paměti během těchto období.

> [!NOTE] 
> V současné době jsou podporovány verze systému Windows, které obsahují rozhraní .NET Framework 4.5 nebo novější verze.

### <a name="performance-counter-trace-"></a>Trasování čítače výkonu (**)

Shromažďuje následující čítače výkonu:

- \Proces, \Procesor, \Paměť, \Podproces, \Fyzický disk a \Logický disk
- \Cache\Dirty Pages, \Cache\Lazy Write Flushs/s, \Server\Pool Nonpaged, Selhání a \Server\Chyby stránkovaného fondu
- Vybrané čítače v části \Network Interface, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments, \Network Adapter, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6 Connection\, \ \ Zásady služby QoS sítě\Pakety, aktivita karty síťového rozhraní \Per Processor a \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>Pro instance serveru SQL Server
- \SQL Server:Správce vyrovnávací paměti, \SQLServer:Statistiky fondu zdrojů a \SQLServer:Statistika SQL\
- \SQLServer:Zámky, \SQLServer:Obecné, Statistiky
- \SQLServer:Metody přístupu

#### <a name="for-azure-files"></a>Pro soubory Azure
\SMB sdílené složky klientů

### <a name="diskspd-benchmark-trace-"></a>Sledování srovnávacího testu Diskspd (***)
Testy pracovního vytížení Diskspd I/O (disk os [zápis] a jednotky fondu [čtení a zápis])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>Spuštění nástroje PerfInsights na virtuálním počítači

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Co musím vědět, než spustím nástroj? 

#### <a name="tool-requirements"></a>Požadavky na nástroje

-  Tento nástroj musí být spuštěn na virtuálním počítači, který má problém s výkonem. 

-  Podporovány jsou následující operační systémy: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 a Windows Server 2016; Windows 8.1 a Windows 10.

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Možné problémy při spuštění nástroje na produkčních virtuálních počítačích

-  Pro scénář benchmarkingu nebo scénář "Advanced performance analysis", který je nakonfigurován pro použití Xperf nebo Diskspd, může nástroj nepříznivě ovlivnit výkon virtuálního počítače. Tyto scénáře by neměly být spuštěny v živém produkčním prostředí.

-  Pro scénář srovnávání nebo scénář "Pokročilá analýza výkonu", který je nakonfigurován pro použití Diskspd, ujistěte se, že žádná jiná aktivita na pozadí nenarušuje vstupně-va úlohy.

-  Ve výchozím nastavení nástroj používá jednotku dočasného úložiště ke shromažďování dat. Pokud trasování zůstane povoleno po delší dobu, může být relevantní množství shromážděných dat. To může snížit dostupnost místa na dočasném disku a proto může ovlivnit všechny aplikace, které závisí na této jednotce.

### <a name="how-do-i-run-perfinsights"></a>Jak spustím PerfInsights? 

PerfInsights můžete spustit na virtuálním počítači instalací [rozšíření virtuálního počítače Azure Performance Diagnostics](performance-diagnostics-vm-extension.md). Můžete jej také spustit jako samostatný nástroj. 

**Instalace a spuštění PerfInsights z webu Azure Portal**

Další informace o této možnosti najdete [v tématu Instalace rozšíření virtuálního počítače diagnostiky výkonu Azure](performance-diagnostics-vm-extension.md#install-the-extension).  

**Spuštění perfinsights v samostatném režimu**

Chcete-li spustit nástroj PerfInsights, postupujte takto:


1. Stáhnout [PerfInsights.zip](https://aka.ms/perfinsightsdownload).

2. Odblokujte soubor PerfInsights.zip. Chcete-li to provést, klepněte pravým tlačítkem myši na soubor PerfInsights.zip a vyberte příkaz **Vlastnosti**. Na kartě **Obecné** vyberte **Odblokovat**a pak vyberte **OK**. Tím je zajištěno, že nástroj běží bez dalších výzev zabezpečení.  

    ![Snímek obrazovky s vlastnostmi PerfInsights se zvýrazněným odblokováním](media/how-to-use-perfInsights/pi-unlock-file.png)

3.  Rozbalte komprimovaný soubor PerfInsights.zip na dočasnou jednotku (ve výchozím nastavení se obvykle jedná o jednotku D). 

4.  Spusťte příkazový řádek systému Windows jako správce a spusťte program PerfInsights.exe a zobrazte dostupné parametry příkazového řádku.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Snímek obrazovky s výstupem příkazového řádku PerfInsights](media/how-to-use-perfInsights/pi-commandline.png)
    
    Základní syntaxe pro spuštění scénářů PerfInsights je:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    Níže uvedený příklad můžete použít ke spuštění scénáře analýzy výkonu po dobu 5 minut:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Následující příklad můžete použít ke spuštění rozšířeného scénáře se stopami čítačů Xperf a Performance po dobu 5 minut:
    
    ```
    PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Níže uvedený příklad můžete použít ke spuštění scénáře analýzy výkonu po dobu 5 minut a nahrání souboru zip výsledků do účtu úložiště:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>
    ```

    Všechny dostupné scénáře a možnosti můžete vyhledat pomocí příkazu **/list:**
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Před spuštěním scénáře perfInsights vyzve uživatele, aby souhlasil se sdílením diagnostických informací a souhlasem s eula. Pomocí **možnosti /AcceptDisclaimerAndShareDiagnostics** tyto výzvy přeskočíte. 
    >
    >Pokud máte aktivní lístek podpory s Microsoftem a spuštění PerfInsights na žádost pracovníka podpory, se kterým pracujete, ujistěte se, že zadat číslo lístku podpory pomocí **/sr** možnost.
    >
    >Ve výchozím nastavení se perfInsights pokusí aktualizovat na nejnovější verzi, pokud je k dispozici. K přeskočení automatické aktualizace použijte parametr **/SkipAutoUpdate** nebo **/sau.**  
    >
    >Pokud není zadán přepínač doby trvání **/d,** PerfInsights vás vyzve k reprodukci problému při spuštění vmslow, azurefiles a pokročilých scénářů. 

Po dokončení trasování nebo operace, nový soubor se zobrazí ve stejné složce jako PerfInsights. Název souboru je **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.zip.** Tento soubor můžete odeslat agentovi podpory k analýze nebo otevřít sestavu uvnitř souboru ZIP a zkontrolovat zjištění a doporučení.

## <a name="review-the-diagnostics-report"></a>Kontrola diagnostické sestavy

V souboru **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.zip** najdete zprávu HTML, která podrobně popisuje zjištění PerfInsights. Chcete-li zprávu zkontrolovat, rozbalte soubor **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.zip** a otevřete soubor **PerfInsights Report.html.**

Vyberte kartu **Zjištění.**

![Snímek obrazovky sestavy](media/how-to-use-perfInsights/pi-finding-tab.png)
![PerfInsights sestavy PerfInsights](media/how-to-use-perfInsights/pi-findings.png)

> [!NOTE] 
> Zjištění zařazená do kategorie vysoká jsou známé problémy, které mohou způsobit problémy s výkonem. Zjištění zařazená do kategorie média představují neoptimální konfigurace, které nemusí nutně způsobovat problémy s výkonem. Zjištění zařazená do kategorie low jsou pouze informativní.

Projděte si doporučení a odkazy pro všechny vysoké a střední nálezy. Přečtěte si, jak mohou ovlivnit výkon, a také osvědčené postupy pro konfigurace optimalizované pro výkon.

### <a name="storage-tab"></a>Karta Úložiště

V části **Zjištění** jsou zobrazena různá zjištění a doporučení týkající se úložiště.

Části **Mapa disků** a **Mapa svazků** popisují vzájemné vzájemné spojení s logickými svazky a fyzickými disky.

V perspektivě fyzického disku (Mapa disku) tabulka zobrazuje všechny logické svazky spuštěné na disku. V následujícím příkladu **PhysicalDrive2** spustí dva logické svazky vytvořené na více oddílů (J a H):

![Snímek obrazovky s kartou disk](media/how-to-use-perfInsights/pi-disk-tab.png)

V perspektivě svazku (Mapa svazku) se v tabulkách zobrazují všechny fyzické disky pod jednotlivými logickými svazky. Všimněte si, že pro disky RAID/Dynamic můžete spustit logický svazek na více fyzických discích. V následujícím příkladu je *\\C: mount* je přípojný bod nakonfigurovaný jako *SpannedDisk* na fyzických discích 2 a 3:

![Snímek obrazovky s kartou hlasitost](media/how-to-use-perfInsights/pi-volume-tab.png)

### <a name="sql-tab"></a>Karta SQL

Pokud cílový virtuální modul hostuje všechny instance serveru SQL Server, zobrazí se v sestavě další karta s názvem **SQL**:

![Snímek obrazovky karty SQL](media/how-to-use-perfInsights/pi-sql-tab.png)

Tato část obsahuje kartu **Zjištění** a další karty pro každou instanci serveru SQL Server hostované na virtuálním počítači.

Karta **Zjištění** obsahuje seznam všech zjištěných problémů s výkonem souvisejících s SQL spolu s doporučeními.

V následujícím příkladu **je zobrazena fyzická** jednotka 0 (spuštění jednotky C). Důvodem je, že **modeldev** a **modellog** soubory jsou umístěny na jednotce C a jsou různých typů (například datový soubor a transakční protokol, v uvedeném pořadí).

![Snímek obrazovky s informacemi protokolu](media/how-to-use-perfInsights/pi-log-info.png)

Karty pro konkrétní instance serveru SQL Server obsahují obecnou část, která zobrazuje základní informace o vybrané instanci. Karty také obsahují další oddíly pro pokročilé informace, včetně nastavení, konfigurace a uživatelské možnosti.

### <a name="diagnostic-tab"></a>Karta Diagnostika
Karta **Diagnostika** obsahuje informace o nejlepších zákaznících procesoru, disku a paměti v počítači po dobu trvání běhu služby PerfInsights. Můžete také najít informace o kritických opravách, které systém uniknou, seznamu úkolů a důležitých systémových událostech. 

## <a name="references-to-the-external-tools-used"></a>Odkazy na použité externí nástroje

### <a name="diskspd"></a>Diskspd

Diskspd je generátor zatížení úložiště a nástroj pro testování výkonu od společnosti Microsoft. Další informace naleznete v tématu [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>Xperf (Xperf)

Xperf je nástroj příkazového řádku pro zachycení trasování ze sady Nástrojů pro výkon systému Windows. Další informace naleznete v [tématu Windows Performance Toolkit – Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>Další kroky

Protokoly diagnostiky a sestavy můžete odeslat do podpory společnosti Microsoft k dalšímu prohledání. Podpora může požadovat, abyste přenesli výstup, který je generován PerfInsights pomoci s procesem řešení potíží.

Následující snímek obrazovky zobrazuje zprávu podobnou tomu, co se vám může zobrazit:

![Snímek obrazovky s ukázkovou zprávou z podpory Microsoftu](media/how-to-use-perfInsights/pi-support-email.png)

Podle pokynů ve zprávě získáte přístup k pracovnímu prostoru přenosu souborů. Pro další zabezpečení je třeba změnit heslo při prvním použití.

Po přihlášení najdete dialogové okno pro nahrání souboru **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.zip,** který byl shromážděn společností PerfInsights.

