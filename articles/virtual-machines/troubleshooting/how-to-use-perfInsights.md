---
title: Použití PerfInsights v Microsoft Azure | Dokumentace Microsoftu
description: Naučí, jak použití PerfInsights k řešení potíží s výkonem virtuálního počítače Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: cb414abcbbf2db7b7cd6a3d724e50010beeef647
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275731"
---
# <a name="how-to-use-perfinsights"></a>Použití PerfInsights

[PerfInsights](https://aka.ms/perfinsightsdownload) je nástroj Diagnostika samoobslužné podpory, který shromažďuje a analyzuje diagnostická data a poskytuje sestavy, které vám pomohou vyřešit problémy s výkonem virtuálního počítače Windows v Azure. PerfInsights může běžet na virtuálních počítačích jako samostatný nástroj, přímo z portálu pomocí [výkonu diagnostiky pro virtuální počítače Azure](performance-diagnostics.md), nebo po instalaci [rozšíření virtuálního počítače výkon diagnostiky Azure ](performance-diagnostics-vm-extension.md).

Pokud máte problémy s výkonem s virtuálními počítači, než se obrátíte na podporu, spusťte tento nástroj.

## <a name="supported-troubleshooting-scenarios"></a>Podporované scénáře řešení potíží

PerfInsights můžete shromažďovat a analyzovat několik druhů informací. Následující části se věnují běžné scénáře.

### <a name="quick-performance-analysis"></a>Rychlý výkon analýzy

Tento scénář shromažďuje konfiguraci disku a další důležité informace, včetně:

-   Protokoly událostí

-   Stav sítě pro všechny příchozí a odchozí připojení

-   Konfigurace nastavení sítě a brány firewall

-   Seznam kroků pro všechny aplikace, které jsou aktuálně spuštěné v systému

-   Nastavení konfigurace databáze serveru Microsoft SQL Server (Pokud je virtuální počítač se identifikuje jako server, na kterém běží SQL Server)

-   Spolehlivost čítače úložiště

-   Důležité opravy hotfix pro Windows

-   Filtrovat nainstalované ovladače

Toto je pasivní kolekce informací, které by neměla mít vliv na systém. 

>[!Note]
>Tento scénář je automaticky zahrnut v každém z následujících scénářů:

### <a name="benchmarking"></a>Srovnávací testy

Tento scénář spuštěn [nástroje Diskspd](https://github.com/Microsoft/diskspd) srovnávacího testu (vstupně-výstupních operací a MB/s) pro všechny jednotky, které jsou připojené k virtuálnímu počítači. 

> [!Note]
> Tento scénář může mít vliv na systém a by neměl být spuštěn v produkčním systému. V případě potřeby spusťte tento scénář v okně vyhrazené Údržba a vyhněte se problémům. Vysoké pracovní vytížení, jež je způsobena trasování nebo srovnávacích testů může nepříznivě ovlivnit výkon virtuálního počítače.
>

### <a name="performance-analysis"></a>Analýza výkonu

Tento scénář spustí [čítač výkonu](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) trasování pomocí čítače, které jsou uvedeny v souboru RuleEngineConfig.json. Pokud virtuální počítač je identifikován jako server, na kterém běží SQL Server, je spustit trasování čítače výkonu. Dělá to pomocí čítače, které se nacházejí v souboru RuleEngineConfig.json. Tento scénář také zahrnuje diagnostická data výkonu.

### <a name="azure-files-analysis"></a>Azure analysis soubory

Tento scénář spustí zachycení čítače výkonu speciální spolu s trasování v síti. Zachytávání zahrnuje všechny čítače zprávy bloku SMB (Server) klienta sdílené složky. Tady jsou některé klíčové klienta sdílená složka čítače výkonu SMB, které jsou součástí zachytávání:

| **Typ**     | **Čítač sdílené složky SMB klienta** |
|--------------|-------------------------------|
| IOPS         | Data požadavků za sekundu             |
|              | Číst počet požadavků za sekundu             |
|              | Zápis požadavků za sekundu            |
| Latence      | Požadavek na střední sec/dat         |
|              | Průměrná/čtení                 |
|              | Průměrná doba/zápis                |
| Velikost vstupně-výstupních operací      | Střední Žádost o bajtů/dat       |
|              | Střední / Přečtených bajtů               |
|              | Střední Bajty/zápis              |
| Propustnost   | Data bajty/s                |
|              | Přečtené bajty/s                |
|              | Bajty zapsané/s               |
| Délka fronty | Střední Délka fronty pro čtení        |
|              | Střední Délka fronty zápisu       |
|              | Střední Délka fronty data        |

### <a name="advanced-performance-analysis"></a>Analýza výkonu Upřesnit

Když spustíte analýzu Upřesnit, vyberte trasování k paralelnímu spuštění. Pokud chcete, můžete je spustit všechny (čítače výkonu, Xperf, sítě a StorPort).  

> [!Note]
> Tento scénář může mít vliv na systém a by neměl být spuštěn v produkčním systému. V případě potřeby spusťte tento scénář v okně vyhrazené Údržba a vyhněte se problémům. Vysoké pracovní vytížení, jež je způsobena trasování nebo srovnávacích testů může nepříznivě ovlivnit výkon virtuálního počítače.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Jaké informace jsou shromažďovány podle PerfInsights?

Informace o virtuální počítač Windows, disků nebo konfigurace fondů úložiště, čítače výkonu, protokoly a se shromažďují různé trasování. To závisí na výkonu scénáře, který používáte. Následující tabulka obsahuje podrobnosti:

|Data shromážděná                              |  |  | Scénáře výkonu |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                               | Rychlý výkon analýzy | Srovnávací testy | Analýza výkonu | Azure analysis soubory | Analýza výkonu Upřesnit |
| Informace z protokolů událostí       | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Systémové informace                | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Mapování svazku                        | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Mapování disku                          | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Spuštěné úkoly                     | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Spolehlivost čítače úložiště      | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Informace o úložiště               | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Výstup fsutil                     | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Informace o filtru ovladačů                | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Výstup příkazu netstat                    | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Konfigurace sítě             | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Konfigurace brány firewall            | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Konfigurace systému SQL Server          | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Trasování diagnostiky výkonu *  | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Čítače sledování výkonu **      |                            |                                    | Ano                      |                      | Ano                  |
| SMB čítačů trasování **              |                            |                                    |                          | Ano                  |                      |
| Trasování SQL serveru čítač **       |                            |                                    | Ano                      |                      | Ano                  |
| Trasování nástroje XPerf                       |                            |                                    |                          |                      | Ano                  |
| Ovladače StorPort trasování                    |                            |                                    |                          |                      | Ano                  |
| Trasování sítě                     |                            |                                    |                          | Ano                  | Ano                  |
| Trasování srovnávacího testu Diskspd ***       |                            | Ano                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Trasování diagnostiky výkonu (*)

Běží na pozadí pro shromažďování dat a diagnostikovat problémy s výkonem probíhající modul založený na pravidlech. Aktuálně jsou podporovány následující pravidla:

- Pravidlo HighCpuUsage: zjistí vysokou období využití procesoru a uvádí hlavních spotřebitelů využití procesoru během těchto období přistupovalo.
- Pravidlo HighDiskUsage: zjistí období využití disku na fyzických discích a ukazuje využití příjemci nejvyšší disku během těchto období přistupovalo.
- Pravidlo HighResolutionDiskMetric: ukazuje metriky latence vstupně-výstupních operací, propustnosti a vstupně-výstupních operací na 50 milisekund u každého fyzického disku. Pomáhá rychle identifikovat období omezování disku.
- Pravidlo HighMemoryUsage: zjistí období využití velkého množství paměti a zobrazí hlavní paměti spotřebitele využití během těchto období přistupovalo.

> [!NOTE] 
> V současné době se podporují Windows, které zahrnují rozhraní .NET Framework 4.5 nebo novější verze.

### <a name="performance-counter-trace-"></a>Čítače sledování výkonu (*)

Shromažďuje následující čítače výkonu:

- \Process \Processor, \Memory, \Thread, \PhysicalDisk a \LogicalDisk
- \Cache\Dirty stránky, \Cache\Lazy zápisu vyprázdnění za sekundu, \Server\Pool nestránkovaného, chyby a selhání \Server\Pool stránkovaného fondu
- Vybrané čítače v rámci \Network rozhraní \IPv4\Datagrams \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments, \Network adaptér, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \ QoS Policy\Packets sítě, \Per procesor síťové rozhraní karty aktivity a \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>Pro instance systému SQL Server
- Správce vyrovnávací paměti serveru: \SQL, \SQLServer:Resource fondu statistiky a \SQLServer:SQL Statistics\
- \SQLServer:Locks \SQLServer:General statistiky
- \SQLServer:Access metody

#### <a name="for-azure-files"></a>Pro soubory Azure
\SMB sdílených složek klienta

### <a name="diskspd-benchmark-trace-"></a>Trasování srovnávacího testu Diskspd (*)
Diskspd vstupně-výstupních operací úloh testy (Disk s operačním systémem [zápis] a jednotky fondu [r/w])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>Spusťte nástroj PerfInsights na vašem virtuálním počítači

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Co musím vědět před mohu spustit nástroj? 

#### <a name="tool-requirements"></a>Požadavky na nástroj

-  Tento nástroj musíte spustit na virtuálním počítači, který má tyto problémy s výkonem. 

-  Podporovány jsou následující operační systémy: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 a Windows Server 2016, Windows 8.1 a Windows 10.

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Možné problémy při spuštění nástroje na produkčních virtuálních počítačů

-  Pro srovnávací testy scénář nebo scénáře "Pokročilé analýzy výkonu", který je nakonfigurován pro použití nástroje Xperf nebo nástroje Diskspd nástroj může nepříznivě ovlivnit výkon virtuálního počítače. Tyto scénáře neměli spouštět v produkčním prostředí.

-  Pro srovnávací testy scénář nebo scénáře "Pokročilé analýzy výkonu", který je nakonfigurován pro použití nástroje Diskspd Ujistěte se, že žádné další aktivitu na pozadí dochází ke kolizím s vstupně-výstupní úlohy.

-  Ve výchozím nastavení používá nástroj pro shromažďování dat disk dočasného úložiště. Při trasování zůstane povolena po delší dobu, objem shromážděných dat můžou být relevantní. To může snížit dostupnost místo na dočasném disku a proto může ovlivnit jakékoli aplikace, která závisí na této jednotce.

### <a name="how-do-i-run-perfinsights"></a>Spouštění PerfInsights 

PerfInsights můžete spustit na virtuálním počítači s instalací [rozšíření virtuálního počítače Azure výkonu Diagnostics](performance-diagnostics-vm-extension.md). Můžete ho spustit také jako samostatný nástroj. 

**Nainstalujte a spusťte PerfInsights z portálu Azure portal**

Další informace o této možnosti najdete v tématu [instalaci virtuálního počítače rozšíření diagnostiky Azure výkonu](performance-diagnostics-vm-extension.md#install-the-extension).  

**Spustit PerfInsights v samostatném režimu**

Chcete-li spustit nástroj PerfInsights, postupujte takto:


1. Stáhněte si [PerfInsights.zip](https://aka.ms/perfinsightsdownload).

2. Odblokování PerfInsights.zip souboru. Chcete-li to provést, klikněte pravým tlačítkem na soubor PerfInsights.zip a vyberte **vlastnosti**. V **Obecné** kartu, vyberte možnost **Odblokovat**a pak vyberte **OK**. Tím se zajistí, že je nástroj spuštěn bez jakékoli další bezpečnostní výzvy.  

    ![Snímek obrazovky PerfInsights vlastnosti, se zvýrazněnou odblokování](media/how-to-use-perfInsights/unlock-file.png)

3.  Rozbalte zkomprimovaný soubor PerfInsights.zip do dočasné jednotky (ve výchozím nastavení, to je obvykle jednotce D). 

4.  Otevřete příkazový řádek Windows jako správce a spusťte PerfInsights.exe zobrazíte parametry příkazového řádku k dispozici.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Snímek obrazovky PerfInsights výstup příkazového řádku](media/how-to-use-perfInsights/PerfInsightsCommandline.png)
    
    Základní syntaxe pro spouštění PerfInsights scénáře je:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    Můžete použít následujícím příkladu pro spuštění scénáře pro analýzu výkonu pro 5 minut:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    V následujícím příkladu můžete použít ke spuštění pokročilý scénář pomocí nástroje Xperf a výkonu čítačů trasování pro 5 minut:
    
    ```
    PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Můžete použít následujícím příkladu ke spuštění scénáře pro analýzu výkonu pro 5 minut a nahrajte soubor zip výsledek do účtu úložiště:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>
    ```

    Můžete vyhledat všechny dostupné scénářích a možnostech pomocí **/list** příkaz:
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Před spuštěním scénáře, PerfInsights vyzve uživatele k souhlasí, že sdílet diagnostické informace a souhlas se smlouvou EULA. Použití **/AcceptDisclaimerAndShareDiagnostics** možnost pro přeskočení těchto pokynů. 
    >
    >Pokud máte s Microsoftem a spuštěné PerfInsights dle požadavku pracují s pracovníkem technické podpory active lístku, ujistěte se, že k poskytování na podporu lístek čísla pomocí **/SR** možnost.
    >
    >Ve výchozím nastavení PerfInsights to samotné aktualizace na nejnovější verzi, pokud je k dispozici. Použití **/SkipAutoUpdate** nebo **/sau** parametru pro přeskočení automatické aktualizace.  
    >
    >Pokud doba trvání **/d** není zadán, PerfInsights vás vyzve k reprodukci problému při spuštění vmslow azurefiles a pokročilé scénáře. 

Po dokončení trasování nebo operace se zobrazí ve stejné složce jako PerfInsights nový soubor. Název souboru je **PerformanceDiagnostics\_rrrr MM-dd\_hh mm ss fff.zip.** Můžete odeslat tento soubor v agentovi podporu pro analýzu nebo otevřete sestavu v souboru zip a zkontrolujte výsledky a doporučení.

## <a name="review-the-diagnostics-report"></a>Zkontrolujte sestavu diagnostiky

V rámci **PerformanceDiagnostics\_rrrr MM-dd\_hh mm ss fff.zip** soubor, můžete najít zprávu ve formátu HTML s podrobnostmi o zjištění PerfInsights. Chcete-li zkontrolovat sestavy, rozbalte **PerformanceDiagnostics\_rrrr MM-dd\_hh mm ss fff.zip** souboru a pak otevřete **PerfInsights Report.html** souboru.

Vyberte **zjištění** kartu.

![Snímek obrazovky sestavy PerfInsights](media/how-to-use-perfInsights/findingtab.png)
![snímek sestavy PerfInsights](media/how-to-use-perfInsights/findings.PNG)

> [!NOTE] 
> Zjištění zařazených do kategorií vysoká jsou známé problémy, které můžou způsobovat problémy s výkonem. Zjištění kategorizována jako střední představují optimální konfigurace, které nutně nezpůsobí problémy s výkonem. Zjištění, které jsou klasifikovány jako s nízkou jsou pouze informativní příkazů.

Projděte si doporučení a odkazy na všechny vysoké a střední výsledky. Další informace o tom, jak může ovlivnit výkon a také o osvědčené postupy pro konfiguraci optimalizováno pro výkon.

### <a name="storage-tab"></a>Karta úložiště

**Zjištění** části zobrazí různé závěry a doporučení týkající se úložiště.

**Mapování disku** a **mapování svazku** části popisují, jak logické svazky a fyzické disky se vztahují k sobě navzájem.

V perspektivě fyzický disk (Disk Map) v tabulce jsou uvedeny všechny logické svazky, které jsou spuštěny na disku. V následujícím příkladu **PhysicalDrive2** spustí dvě logické svazků vytvořených na několik oddílů (J a H):

![Snímek obrazovky s kartou disku](media/how-to-use-perfInsights/disktab.png)

Tabulky v perspektivě svazek (Mapa svazku) zobrazují všechny fyzické disky v rámci každé logické svazku. Všimněte si, že RAID nebo dynamické disky, můžete spustit logický svazek na několik fyzických disků. V následujícím příkladu *C:\\připojit* přípojný bod je nakonfigurován jako *SpannedDisk* na fyzických discích, 2 a 3:

![Snímek obrazovky s kartou svazku](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-tab"></a>Karta SQL

Pokud cílový virtuální počítač hostuje všechny instance systému SQL Server, zobrazí se další kartu v sestavě, s názvem **SQL**:

![Snímek obrazovky SQL kartu](media/how-to-use-perfInsights/sqltab.png)

Tato část obsahuje **zjištění** karty a další záložky pro jednotlivé instance SQL serveru hostované na virtuálním počítači.

**Zjištění** karta obsahuje seznam všech SQL týkající se problémů s výkonem, které najde, spolu s doporučení.

V následujícím příkladu **PhysicalDrive0** (spuštěna jednotce C) se zobrazí. Důvodem je, že i **modeldev** a **modellog** soubory jsou umístěny na jednotce C a jsou z různých typů (například data souborů a protokolů transakcí, v uvedeném pořadí).

![Snímek obrazovky se informace protokolu](media/how-to-use-perfInsights/loginfo.png)

Karty pro konkrétní instance systému SQL Server obsahovat obecné oddíl, který zobrazuje základní informace o vybrané instance. Karty taky obsahovat další oddíly pro rozšířené informace, včetně nastavení, konfigurace a možnosti uživatele.

### <a name="diagnostic-tab"></a>Karta diagnostiky
**Diagnostických** karta obsahuje informace o hlavních spotřebitelů procesoru, disku a paměti počítače po dobu trvání spuštění PerfInsights. Můžete také najít informace o důležitých oprav, že systém pravděpodobně chybí, seznam úkolů a důležitých systémových událostí. 

## <a name="references-to-the-external-tools-used"></a>Odkazy na externí nástroje použít

### <a name="diskspd"></a>Nástroje Diskspd

Diskspd je úložiště zatížení generátoru pro kontejnery a výkonu testovací nástroj od Microsoftu. Další informace najdete v tématu [nástroje Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>Nástroje XPerf

Nástroje XPerf je nástroj příkazového řádku trasování z Windows Performance Toolkit. Další informace najdete v tématu [Windows Performance Toolkit – Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>Další postup

Diagnostické protokoly a sestavy můžete nahrát do Microsoft Support pro další kontrolu. Podpora vás může požádat, aby předaly výstupu, který je generován PerfInsights vám pomůže s proces řešení potíží.

Následující snímek obrazovky ukazuje podobný co se může zobrazit zpráva:

![Snímek obrazovky s ukázkovou zprávu z Microsoft Support](media/how-to-use-perfInsights/supportemail.png)

Postupujte podle pokynů ve zprávě pro přístup k pracovnímu prostoru přenos souboru. Za účelem zvýšení zabezpečení budete muset změnit heslo při prvním použití.

Po přihlášení se vás bude dialogové okno pro nahrání **PerformanceDiagnostics\_rrrr MM-dd\_hh mm ss fff.zip** souboru, která byla shromážděna PerfInsights.

