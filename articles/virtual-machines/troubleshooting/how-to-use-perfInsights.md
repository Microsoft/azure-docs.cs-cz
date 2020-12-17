---
title: Jak používat PerfInsights v Microsoft Azure | Microsoft Docs
description: Naučte se používat PerfInsights k řešení problémů s výkonem virtuálních počítačů s Windows.
services: virtual-machines-windows
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
ms.openlocfilehash: 9e298bf39446024f384b9af142fe3000e936bb6d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656848"
---
# <a name="how-to-use-perfinsights-in-azure"></a>Jak používat PerfInsights v Azure

[PerfInsights](https://aka.ms/perfinsightsdownload) je samoobslužný Nástroj pro diagnostiku, který shromažďuje a analyzuje diagnostická data a poskytuje zprávu, která vám může pomoct řešit problémy s výkonem virtuálních počítačů s Windows v Azure. PerfInsights je možné spustit na virtuálních počítačích jako samostatný nástroj přímo z portálu pomocí [diagnostiky výkonu pro virtuální počítače Azure](performance-diagnostics.md)nebo instalací [rozšíření virtuálního počítače Azure Performance Diagnostics](performance-diagnostics-vm-extension.md).

Pokud dochází k problémům s výkonem s virtuálními počítači, než se obrátíte na podporu, spusťte tento nástroj.

## <a name="supported-troubleshooting-scenarios"></a>Podporované scénáře řešení potíží

PerfInsights může shromažďovat a analyzovat několik druhů informací. V následujících částech najdete běžné scénáře.

### <a name="quick-performance-analysis"></a>Rychlá analýza výkonu

Tento scénář shromažďuje konfiguraci disku a další důležité informace, včetně těchto:

-   Protokoly událostí

-   Stav sítě pro všechna příchozí a odchozí připojení

-   Nastavení konfigurace sítě a brány firewall

-   Seznam úkolů pro všechny aplikace, které jsou aktuálně spuštěné v systému

-   Microsoft SQL Server nastavení konfigurace databáze (Pokud je virtuální počítač identifikovaný jako server, na kterém běží SQL Server)

-   Čítače spolehlivosti úložiště

-   Důležité opravy hotfix systému Windows

-   Nainstalované ovladače filtru

Jedná se o pasivní shromažďování informací, které by neměly mít vliv na systém. 

>[!Note]
>Tento scénář je automaticky zahrnutý v každém z následujících scénářů:

### <a name="benchmarking"></a>Srovnávací testy

V tomto scénáři se spustí srovnávací test [DiskSpd](https://github.com/Microsoft/diskspd) (IOPS a MB/s) pro všechny jednotky, které jsou připojené k virtuálnímu počítači. 

> [!Note]
> Tento scénář může ovlivnit systém a neměl by být spuštěn v živém produkčním systému. V případě potřeby tento scénář spusťte ve vyhrazeném časovém období údržby, abyste se vyhnuli jakýmkoli problémům. Zvýšené zatížení, které je způsobeno trasováním nebo testem srovnávacích testů, může negativně ovlivnit výkon virtuálního počítače.
>

### <a name="performance-analysis"></a>Analýza výkonu

Tento scénář spustí trasování [čítače výkonu](/windows/win32/perfctrs/performance-counters-portal) pomocí čítačů, které jsou zadány v RuleEngineConfig.jsv souboru. Pokud je virtuální počítač identifikovaný jako server, na kterém běží SQL Server, spustí se trasování čítače výkonu. K tomu slouží čítače, které se nacházejí v RuleEngineConfig.jsv souboru. Tento scénář také zahrnuje data diagnostiky výkonu.

### <a name="azure-files-analysis"></a>Analýza souborů Azure

Tento scénář spouští speciální zachycení čítače výkonu společně se síťovým trasováním. Funkce Capture zahrnuje všechny čítače sdílených složek klienta protokolu SMB (Server Message Block). Níže jsou uvedeny některé čítače výkonu sdílené složky klienta SMB, které jsou součástí zachycení:

| **Typ**     | **Čítač sdílených složek klienta SMB** |
|--------------|-------------------------------|
| IOPS         | Požadavky na data/s             |
|              | Počet žádostí o čtení za sekundu             |
|              | Požadavky na zápis za sekundu            |
| Latence      | Prům. s/požadavek na data         |
|              | Prům. sekund/čtení                 |
|              | Prům. s/zápis                |
| Velikost v/v      | Průměrný počet bajtů/dat – požadavek       |
|              | Střední bajty/čtení               |
|              | Střední bajty/zápis              |
| Propustnost   | Bajty dat/s                |
|              | Přečtené bajty/s                |
|              | Zapsané bajty/s               |
| Délka fronty | Průměrná délka fronty čtení        |
|              | Průměrná délka fronty zápisu       |
|              | Průměrná délka fronty dat        |

### <a name="advanced-performance-analysis"></a>Pokročilá analýza výkonu

Když spustíte pokročilou analýzu výkonu, vyberete možnost trasování pro paralelní spuštění. Pokud chcete, můžete je spustit vše (čítač výkonu, Xperf, síť a ovladač StorPort).  

> [!Note]
> Tento scénář může ovlivnit systém a neměl by být spuštěn v živém produkčním systému. V případě potřeby tento scénář spusťte ve vyhrazeném časovém období údržby, abyste se vyhnuli jakýmkoli problémům. Zvýšené zatížení, které je způsobeno trasováním nebo testem srovnávacích testů, může negativně ovlivnit výkon virtuálního počítače.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Jaký druh informací shromažďuje služba PerfInsights?

Shromažďují se informace o virtuálním počítači s Windows, discích nebo konfiguraci fondů úložiště, čítačích výkonu, protokolech a různých trasováních. Závisí na scénáři výkonu, který používáte. Podrobnosti jsou uvedené v následující tabulce:

| Shromažďovaná data | Rychlá analýza výkonu | Srovnávací testy | Analýza výkonu | Analýza souborů Azure | Pokročilá analýza výkonu |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|
| Informace z protokolů událostí       | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Systémové informace                | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Mapa svazků                        | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Mapa disku                          | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Spuštěné úlohy                     | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Čítače spolehlivosti úložiště      | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Informace o úložišti               | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Fsutil Output                     | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Filtrovat informace ovladače                | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Výstup příkazu netstat                    | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Konfigurace sítě             | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Konfigurace brány firewall            | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Konfigurace SQL Serveru          | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Trasování diagnostiky výkonu *  | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Trasování čítače výkonu * *      |                            |                                    | Ano                      |                      | Ano                  |
| Trasování čítače SMB * *              |                            |                                    |                          | Ano                  |                      |
| Trasování čítače SQL Server * *       |                            |                                    | Ano                      |                      | Ano                  |
| Xperf trasování                       |                            |                                    |                          |                      | Ano                  |
| Trasování StorPort                    |                            |                                    |                          |                      | Ano                  |
| Trasování sítě                     |                            |                                    |                          | Ano                  | Ano                  |
| Sledování srovnávacích testů DiskSpd * * _       |                            | Ano                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-_"></a>Trasování diagnostiky výkonu (_)

Spouští modul založený na pravidlech na pozadí ke shromažďování dat a diagnostice probíhajících problémů s výkonem. V současné době jsou podporovány následující pravidla:

- Pravidlo HighCpuUsage: detekuje vysoké doby využití procesoru a v těchto obdobích zobrazuje uživatele s nejvyšším využitím procesoru.
- Pravidlo HighDiskUsage: detekuje doba využití disku na fyzických discích a v těchto obdobích zobrazuje uživatele s nejvyšším využitím disku.
- Pravidlo HighResolutionDiskMetric: zobrazuje počet vstupně-výstupních operací pro vstupně-výstupní operace a metriky latence na 50 milisekund pro každý fyzický disk. Pomáhá rychle identifikovat dobu omezení disku.
- Pravidlo HighMemoryUsage: detekuje dobu využití vysoké paměti a v těchto obdobích zobrazuje uživatele s největším využitím paměti.

> [!NOTE] 
> V současné době jsou podporovány verze Windows, které zahrnují .NET Framework 4,5 nebo novější verze.

### <a name="performance-counter-trace-"></a>Trasování čítače výkonu (* *)

Shromažďuje následující čítače výkonu:

- \Process, \Processor, \Memory, \Thread, \PhysicalDisk a \ logický disk
- \Cache\Dirty stránky, vyprázdnění \Cache\Lazy zápisu za sekundu, \Server\Pool nestránkované, selhání a \Server\Pool stránkovaného selhání
- Vybrané čítače v rámci rozhraní \Network, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments, \Network Adapter, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \Network, Policy\Packets technologie QoS \Per, aktivita síťového rozhraní na kartě BSP procesoru a \Microsoft Winsock

#### <a name="for-sql-server-instances"></a>Pro instance SQL Server
- \SQL Server: Správce vyrovnávací paměti, \SQLServer: statistiky fondu zdrojů a \SQLServer: Statistika SQL \
- \SQLServer: zámky, \SQLServer: Obecné, Statistika
- \SQLServer: metody přístupu

#### <a name="for-azure-files"></a>Pro soubory Azure
\SMB sdílené složky klienta

### <a name="diskspd-benchmark-trace-_"></a>Sledování srovnávacích testů DiskSpd (* * _)
DiskSpd vstupně-výstupních úloh testů (disk s operačním systémem [zápis] a jednotky fondu [čtení/zápis])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>Spuštění nástroje PerfInsights na VIRTUÁLNÍm počítači

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Co musím vědět před spuštěním tohoto nástroje? 

#### <a name="tool-requirements"></a>Požadavky na nástroj

-  Tento nástroj se musí spustit na virtuálním počítači, který má problém s výkonem. 

-  Podporovány jsou následující operační systémy: _ Windows Server 2019
   * Windows Server 2016
   * Windows Server 2012 R2
   * Windows Server 2012
   * Windows Server 2008 R2
   * Windows 10
   * Windows 8.1
   * Windows 8

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Možné problémy při spuštění nástroje na produkčních virtuálních počítačích

-  Pro scénář srovnávacích testů nebo pro scénář "Pokročilá analýza výkonu", který je nakonfigurován tak, aby používal Xperf nebo DiskSpd, může tento nástroj negativně ovlivnit výkon virtuálního počítače. Tyto scénáře by se neměly spouštět v živém provozním prostředí.

-  Pro scénář srovnávacích testů nebo pro scénář "Pokročilá analýza výkonu", který je nakonfigurován na používání DiskSpd, se ujistěte, že žádná jiná aktivita na pozadí nekoliduje s vstupně-výstupní úlohou.

-  Nástroj ve výchozím nastavení používá k shromažďování dat jednotku dočasného úložiště. Pokud trasování zůstává povolené po delší dobu, může to být relevantní množství shromažďovaných dat. To může snížit dostupnost místa na dočasném disku a může proto ovlivnit jakoukoli aplikaci, která na této jednotce závisí.

### <a name="how-do-i-run-perfinsights"></a>Návody PerfInsights spustit? 

PerfInsights můžete na virtuálním počítači spustit tak, že nainstalujete [rozšíření virtuálního počítače Azure Performance Diagnostics](performance-diagnostics-vm-extension.md). Můžete ho také spustit jako samostatný nástroj. 

**Instalace a spuštění PerfInsights z Azure Portal**

Další informace o této možnosti najdete v tématu [Instalace rozšíření virtuálního počítače Azure Performance Diagnostics](performance-diagnostics-vm-extension.md#install-the-extension).  

**Spustit PerfInsights v samostatném režimu**

Chcete-li spustit nástroj PerfInsights, postupujte podle následujících kroků:


1. Stáhněte si [PerfInsights.zip](https://aka.ms/perfinsightsdownload).

2. Odblokuje soubor PerfInsights.zip. Provedete to tak, že kliknete pravým tlačítkem na soubor PerfInsights.zip a vyberete **vlastnosti**. Na kartě **Obecné** vyberte **odblokovat** a pak vyberte **OK**. Tím se zajistí, že se nástroj spustí bez dalších výzev zabezpečení.  

    ![Snímek obrazovky s vlastnostmi PerfInsights se zvýrazněným odblokem](media/how-to-use-perfInsights/pi-unlock-file.png)

3.  Rozbalte komprimovaný soubor PerfInsights.zip na dočasnou jednotku (ve výchozím nastavení je to obvykle jednotka D). 

4.  Otevřete příkazový řádek systému Windows jako správce a pak spusťte PerfInsights.exe, abyste zobrazili dostupné parametry příkazového řádku.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Snímek obrazovky s výstupem příkazového řádku PerfInsights](media/how-to-use-perfInsights/pi-commandline.png)
    
    Základní syntaxe pro spouštění scénářů PerfInsights je:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    Pomocí níže uvedeného příkladu můžete spustit scénář analýzy výkonu po dobu 5 minut:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Následující příklad můžete použít ke spuštění pokročilého scénáře s trasováním Xperf a čítače výkonu po dobu 5 minut:
    
    ```
    PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Pomocí níže uvedeného příkladu můžete spustit scénář analýzy výkonu po dobu 5 minut a odeslat soubor zip výsledků do účtu úložiště:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>
    ```

    Všechny dostupné scénáře a možnosti můžete vyhledat pomocí příkazu **/list** :
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Před spuštěním scénáře PerfInsights vyzve uživatele, aby souhlasil se sdílením diagnostických informací a souhlasil se smlouvou EULA. Tyto výzvy přeskočíte pomocí možnosti **/AcceptDisclaimerAndShareDiagnostics** . 
    >
    >Pokud máte aktivní lístek podpory s Microsoftem a provozujete PerfInsights na žádost pro inženýra podpory, se kterým pracujete, ujistěte se, že jste zadali číslo lístku podpory pomocí možnosti **/SR** .
    >
    >Ve výchozím nastavení se PerfInsights pokusí aktualizovat na nejnovější verzi, pokud je dostupná. K přeskočení automatické aktualizace použijte parametr **/SkipAutoUpdate** nebo **/sau** .  
    >
    >Pokud není zadaný přepínač trvání **/d** , PerfInsights vás vyzve k reprodukci problému při spouštění vmslow, azurefiles a pokročilých scénářů. 

Po dokončení trasování nebo operací se ve stejné složce jako PerfInsights zobrazí nový soubor. Název souboru je **PerformanceDiagnostics \_ yyyy-MM-DD \_hh-mm-ss-fff.zip.** Tento soubor můžete poslat agentovi podpory pro účely analýzy nebo otevřít sestavu v souboru zip a zkontrolovat si závěry a doporučení.

## <a name="review-the-diagnostics-report"></a>Kontrola diagnostické sestavy

V **PerformanceDiagnostics souboru \_ RRRR-MM- \_hh-mm-ss-fff.zipDD** můžete najít sestavu HTML, která podrobně popisuje závěry PerfInsights. Chcete-li sestavu zkontrolovat, rozbalte soubor **PerformanceDiagnostics \_ yyyy-MM-DD \_hh-mm-ss-fff.zip** a pak otevřete soubor **PerfInsights Report.html** .

Vyberte kartu **zjištění** .

![Snímek obrazovky s kartou přehled sestavy ](media/how-to-use-perfInsights/pi-finding-tab.png)
 ![ PerfInsights Snímek obrazovky karty úložiště sestavy PerfInsights](media/how-to-use-perfInsights/pi-findings.png)

> [!NOTE] 
> Nálezy zařazené do kategorie vysoká jsou známé problémy, které mohou způsobit problémy s výkonem. Nálezy kategorizované jako střední představuje neoptimální konfigurace, které nemusí nutně způsobovat problémy s výkonem. Nálezy zařazené do kategorie nízká jsou informativní pouze informativní příkazy.

Přečtěte si doporučení a odkazy pro všechna vysoká a střední zjištění. Seznamte se s tím, jak mohou ovlivnit výkon, a také o osvědčených postupech pro konfigurace optimalizované pro výkon.

### <a name="storage-tab"></a>Karta úložiště

V části **nálezy** se zobrazují různé závěry a doporučení týkající se úložiště.

Oddíly **Mapa disku** a **Mapa svazků** popisují, jak se vzájemně souvisí logické svazky a fyzické disky.

V perspektivě fyzického disku (mapa disku) zobrazuje tabulka všechny logické svazky, které jsou na disku spuštěné. V následujícím příkladu **PhysicalDrive2** spustí dva logické svazky vytvořené na více oddílech (J a H):

![Snímek obrazovky s kartou disk](media/how-to-use-perfInsights/pi-disk-tab.png)

V perspektivě svazku (mapa svazků) jsou v tabulkách zobrazeny všechny fyzické disky v rámci jednotlivých logických svazků. Všimněte si, že u polí RAID/dynamic disks můžete spustit logický svazek na více fyzických discích. V následujícím příkladu je *C: \\ Mount* přípojný bod nakonfigurovaný jako *SpannedDisk* na fyzických discích 2 a 3:

![Snímek obrazovky s kartou Volume](media/how-to-use-perfInsights/pi-volume-tab.png)

### <a name="sql-tab"></a>Karta SQL

Pokud cílový virtuální počítač hostuje nějaké instance SQL Server, zobrazí se v sestavě další karta s názvem **SQL**:

![Snímek obrazovky s kartou SQL](media/how-to-use-perfInsights/pi-sql-tab.png)

Tato část obsahuje kartu **zjištění** a další karty pro všechny SQL Server instance hostované na virtuálním počítači.

Karta **nálezy** obsahuje seznam všech nalezených problémů s výkonem souvisejících s SQL, včetně doporučení.

V následujícím příkladu se zobrazí **PhysicalDrive0** (spouští se jednotka C). Důvodem je to, že soubory **modeldev** a **modellog** jsou umístěné na jednotce C a jsou různými typy (například datový soubor a transakční protokol).

![Snímek obrazovky s informacemi o protokolu](media/how-to-use-perfInsights/pi-log-info.png)

Karty pro konkrétní instance SQL Server obsahují obecnou část, která zobrazuje základní informace o vybrané instanci. Karty také obsahují další části pro rozšířené informace, včetně nastavení, konfigurací a uživatelských možností.

### <a name="diagnostic-tab"></a>Karta Diagnostika
Karta **Diagnostika** obsahuje informace o hlavních spotřebitelích procesoru, disku a paměti v počítači po dobu trvání běhu PerfInsights. Můžete také najít informace o důležitých opravách, které systém může chybět, seznam úkolů a důležité systémové události. 

## <a name="references-to-the-external-tools-used"></a>Odkazy na použité externí nástroje

### <a name="diskspd"></a>DiskSpd

DiskSpd je generátor zátěžového úložiště a nástroj pro testování výkonu od Microsoftu. Další informace najdete v tématu [DiskSpd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>Xperf

Xperf je nástroj příkazového řádku pro zachycení trasování ze sady nástrojů Windows Performance Toolkit. Další informace najdete v tématu [Windows Performance Toolkit – Xperf](/archive/blogs/ntdebugging/windows-performance-toolkit-xperf).

## <a name="next-steps"></a>Další kroky

Protokoly diagnostiky a sestavy můžete odeslat podpora Microsoftu k dalšímu přezkoumání. Podpora může vyžadovat, abyste odeslali výstup, který je vygenerovaný PerfInsights, aby pomohl procesu řešení potíží.

Na následujícím snímku obrazovky se zobrazí zpráva podobná tomu, co se vám může zobrazit:

![Snímek obrazovky ukázkové zprávy z podpora Microsoftu](media/how-to-use-perfInsights/pi-support-email.png)

Postupujte podle pokynů ve zprávě pro přístup k pracovnímu prostoru přenosu souborů. Pro zvýšení zabezpečení je nutné při prvním použití změnit heslo.

Po přihlášení se zobrazí dialogové okno pro nahrání souboru **\_ \_hh-mm-ss-fff.ziprrrr-mm-dd** , který byl shromážděn nástrojem PerfInsights.
