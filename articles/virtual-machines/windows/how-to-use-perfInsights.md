---
title: "Jak používat PerfInsights v Microsoft Azure | Microsoft Docs"
description: "Zjišťuje použití PerfInsights k řešení potíží s výkonem virtuálního počítače s Windows."
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: ee8334cbe9256b7a5ecd5e96afa2f15d6389afa8
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2018
---
# <a name="how-to-use-perfinsights"></a>Jak používat PerfInsights 

[PerfInsights](http://aka.ms/perfinsightsdownload) je nástroj pro diagnostiku návody pro Svépomocné řešení, který shromažďuje a analyzuje diagnostických dat a poskytuje sestavy k řešení potíží s výkonem virtuálního počítače Windows v Azure. PerfInsights lze spustit na virtuálních počítačích jako samostatný nástroj, nebo přímo z portálu nainstalováním [rozšíření virtuálního počítače diagnostiky výkonu Azure](performance-diagnostics-vm-extension.md).

Pokud dochází k problémům s výkonem s virtuálními počítači, než se obrátíte na podporu, spusťte tento nástroj.

## <a name="supported-troubleshooting-scenarios"></a>Podporované scénáře řešení potíží

PerfInsights můžete shromažďovat a analyzovat několik druhů informace. Následující části se věnují běžné scénáře.

### <a name="collect-basic-configuration"></a>Shromažďovat základní konfigurace 

Tento scénář shromažďuje konfigurace disku a další důležité informace, včetně:

-   Protokoly událostí

-   Stav sítě pro všechny příchozí a odchozí připojení

-   Konfigurace nastavení sítě a brány firewall

-   Seznam úloh pro všechny aplikace, které jsou aktuálně spuštěny v systému

-   Nastavení konfigurace databáze serveru Microsoft SQL Server (Pokud je virtuální počítač se identifikuje jako server, který se systémem SQL Server)

-   Čítače spolehlivost úložiště

-   Důležité opravy hotfix pro Windows

-   Ovladače nainstalované filtru

Toto je pasivní kolekce informace, které by neměly vliv na systém. 

>[!Note]
>Tento scénář je automaticky zahrnuta v každé z následujících scénářů:

### <a name="benchmarking"></a>Srovnávací testy

Tento scénář běží [Diskspd](https://github.com/Microsoft/diskspd) srovnávacího testu (IOPS a MB/s) pro všechny disky, které jsou připojené k virtuálnímu počítači. 

> [!Note]
> Tento scénář může mít vliv na systém a nesmí se spouštět na systému za provozu produkční. V případě potřeby spusťte tento scénář v vyhrazené údržby vyhnuli potížím. Zvýšit zatížení, která je způsobena trasování nebo srovnávacího testu testovací může nepříznivě ovlivnit výkon virtuálního počítače.
>

### <a name="slow-vm-analysis"></a>Pomalé analysis virtuálních počítačů 

Tento scénář spustí [čítače výkonu](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) trasování pomocí čítačů, které jsou určené v souboru RuleEngineConfig.json. Pokud virtuální počítač se identifikuje jako server, který se systémem SQL Server, je spustit trasování čítače výkonu. Dělá to tak pomocí čítačů, které se nacházejí v souboru RuleEngineConfig.json. Tento scénář také zahrnuje diagnostická data výkonu.

### <a name="azure-files-analysis"></a>Azure analysis soubory 

Tento scénář se spustí zachycení čítače výkonu speciální společně s provést síťové trasování. Zachytávání zahrnuje všechny čítače sdílené složky Server Message Block (SMB) klienta. Tady jsou některé klíčové SMB klienta sdílenou složku čítače výkonu, které jsou součástí zachytávání:

| **Typ**     | **Čítač sdílené složky SMB klienta** |
|--------------|-------------------------------|
| IOPS         | Data požadavků za sekundu             |
|              | Přečtěte si požadavky/s             |
|              | Zápis požadavků za sekundu            |
| Latence      | Střední doba nebo dat požadavku         |
|              | Průměrná doba/čtení                 |
|              | Průměrná doba/zápis                |
| Velikost vstupně-výstupní operace      | Střední Požadavek na bajtů nebo dat       |
|              | Střední Bajty/čtení               |
|              | Střední Bajty a zápis              |
| Propustnost   | Data Bytes/sec                |
|              | Číst bajty/s                |
|              | Zápis bajtů za sekundu               |
| Délka fronty | Střední Délka fronty pro čtení        |
|              | Střední Délka fronty zápisu       |
|              | Střední Délka fronty dat        |

### <a name="custom-slow-vm-analysis"></a>Vlastní pomalé analysis virtuálních počítačů 

Pokud spustíte vlastní pomalé analysis virtuálních počítačů, můžete vybrat trasování do souběžně. Pokud chcete, můžete je spustit všechny (čítače výkonu, Xperf, sítě a StorPort).  

> [!Note]
> Tento scénář může mít vliv na systém a nesmí se spouštět na systému za provozu produkční. V případě potřeby spusťte tento scénář v vyhrazené údržby vyhnuli potížím. Zvýšit zatížení, která je způsobena trasování nebo srovnávacího testu testovací může nepříznivě ovlivnit výkon virtuálního počítače.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Jaký druh informací shromažďuje PerfInsights?

Zaznamená informace o virtuální počítač s Windows, disky nebo konfigurace fondů úložiště, čítače výkonu, a jsou shromažďovány různé trasování. To závisí na výkonu scénáře, který používáte. Podrobnosti naleznete v následující tabulce:

|Data shromážděná                              |  |  | Scénáře výkonu |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                               | Shromažďovat základní konfigurace | Srovnávací testy | Pomalé analysis virtuálních počítačů | Azure analysis soubory | Vlastní pomalé analysis virtuálních počítačů |
| Informace z protokolů událostí       | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Informace o systému                | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Svazek mapy                        | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Mapa disku                          | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Spuštěné úkoly                     | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Čítače spolehlivost úložiště      | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Informace o úložiště               | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Výstup fsutil                     | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Informace o ovladač filtru                | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Výstup příkazu netstat                    | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Konfigurace sítě             | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Konfigurace brány firewall            | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Konfigurace systému SQL Server          | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Trasování diagnostiky výkonu *  | Ano                        | Ano                                | Ano                      | Ano                  | Ano                  |
| Trasování čítače výkonu **      |                            |                                    | Ano                      |                      | Ano                  |
| Trasování čítač SMB **              |                            |                                    |                          | Ano                  |                      |
| Čítač trasování systému SQL Server **       |                            |                                    | Ano                      |                      | Ano                  |
| XPerf trasování                       |                            |                                    |                          |                      | Ano                  |
| StorPort trasování                    |                            |                                    |                          |                      | Ano                  |
| Trasování sítě                     |                            |                                    |                          | Ano                  | Ano                  |
| Trasování srovnávacího testu Diskspd ***       |                            | Ano                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Sledování výkonu diagnostiky (*)

Běží na pozadí pro shromažďování dat a diagnostikovat problémy s výkonem probíhající modul založený na pravidlech. Aktuálně jsou podporovány následující pravidla:

- Pravidlo HighCpuUsage: zjistí vysoké období využití procesoru a zobrazuje hlavních spotřebitelů využití procesoru během těchto období.
- Pravidlo HighDiskUsage: zjistí využití období vysoké disku na fyzických discích a zobrazuje nejvyšší disku příjemci využití během těchto období.
- Pravidlo HighResolutionDiskMetric: ukazuje latence metriky IOP, propustnosti a vstupně-výstupních operací na 50 milisekund pro každého fyzického disku. Pomáhá rychle zjistit disku omezení tečky.
- Pravidlo HighMemoryUsage: zjistí období využití velkého množství paměti a zobrazuje hlavní paměti příjemci využití během těchto období.

> [!NOTE] 
> V současné době jsou podporovány verze Windows, které zahrnují rozhraní .NET Framework 3.5 nebo novější verze.

### <a name="performance-counter-trace-"></a>Trasování čítače výkonu (*)

Shromažďuje následující čítače výkonu:

- \Process, \Processor, \Memory, \Thread, \PhysicalDisk a \LogicalDisk
- \Cache\Dirty stránky, zápisů \Cache\Lazy/s, \Server\Pool nestránkovaného fondu, chyby a selhání \Server\Pool stránkovaného fondu
- Vybrané čítače pod \Network rozhraní \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments, \Network adaptéru, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \ Síťové technologie QoS Policy\Packets, \Per procesor síťové rozhraní karty aktivity a \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>Pro instance systému SQL Server
- Správce serveru: vyrovnávací paměti \SQL, \SQLServer:Resource fondu statistiky a \SQLServer:SQL Statistics\
- \SQLServer:Locks, \SQLServer:General statistiky
- \SQLServer:Access metody

#### <a name="for-azure-files"></a>Pro soubory Azure
\SMB sdílených složek klienta

### <a name="diskspd-benchmark-trace-"></a>Trasování srovnávacího testu Diskspd (*)
Zatížení testy Diskspd vstupně-výstupních operací (Disk s operačním systémem [zápisu] a fondu jednotky [pro čtení a zápis])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>Spusťte nástroj PerfInsights na vašem virtuálním počítači

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Co je nutné vědět před I spustit nástroj? 

#### <a name="tool-requirements"></a>Požadavky na nástroj

-  Tento nástroj musíte spustit na virtuálním počítači, který má problémy s výkonem. 

-  Podporovány jsou následující operační systémy: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 a Windows Server 2016; Windows 8.1 a Windows 10.

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Možné problémy při spuštění nástroje na produkční virtuální počítače

-  Pro scénář testu typovou úlohou nebo scénář "Vlastní pomalé analysis virtuálního počítače", který je nakonfigurovaný na použití Xperf nebo nástroje Diskspd nástroj může nepříznivě ovlivnit výkon virtuálního počítače. Tyto scénáře by neměla běžet v provozním prostředí.

-  Pro scénář testu typovou úlohou nebo scénář "Vlastní pomalé analysis virtuálního počítače", který je nakonfigurovaný na použití nástroje Diskspd Ujistěte se, že žádné další aktivita na pozadí naruší vstupně-výstupní úlohy.

-  Ve výchozím nastavení používá nástroj pro shromažďování dat jednotky dočasné úložiště. Pokud trasování zůstane povolena delší dobu, může být množství dat, které jsou shromážděny relevantní. To může snížit dostupnost místa na disku dočasné a proto může ovlivnit všechny aplikace, které jsou závislé na této jednotce.

### <a name="how-do-i-run-perfinsights"></a>Jak spustit PerfInsights? 

PerfInsights můžete spustit na virtuálním počítači nainstalováním [rozšíření virtuálního počítače diagnostiky výkonu Azure](performance-diagnostics-vm-extension.md). Můžete je také spustit jako samostatný nástroj. 

**Nainstalujte a spusťte PerfInsights z portálu Azure**

Další informace o této možnosti najdete v tématu [instalaci rozšíření Azure výkon diagnostiky virtuálního počítače](performance-diagnostics-vm-extension.md#install-the-extension).  

**Spustit PerfInsights v samostatném režimu**

Chcete-li spustit nástroj PerfInsights, postupujte takto:


1. Stáhněte si [PerfInsights.zip](http://aka.ms/perfinsightsdownload).

2. Odblokování PerfInsights.zip souboru. Chcete-li to provést, klikněte pravým tlačítkem na soubor PerfInsights.zip a vyberte **vlastnosti**. V **Obecné** vyberte **Odblokovat**a potom vyberte **OK**. To zajišťuje, že nástroj běží bez jakékoli další bezpečnostní zobrazí výzvu.  

    ![Snímek obrazovky PerfInsights vlastnosti, se zvýrazněnou odblokovat](media/how-to-use-perfInsights/unlock-file.png)

3.  Rozbalte komprimovaný soubor PerfInsights.zip do dočasné jednotky (ve výchozím nastavení, to je obvykle diskovou jednotku d). 

4.  Otevřete příkazový řádek systému Windows jako správce a spusťte PerfInsights.exe zobrazíte parametry příkazového řádku k dispozici.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Snímek obrazovky PerfInsights výstup příkazového řádku](media/how-to-use-perfInsights/PerfInsightsCommandline.png)
    
    Základní syntaxe pro spuštění scénáře PerfInsights je:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    Můžete použít následujícím příkladu ke spuštění pomalé scénář virtuálních počítačů pro 5 minut:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    V následujícím příkladu můžete použít ke spuštění vlastní scénáře s Xperf a výkonu čítač trasování pro 5 minut:
    
    ```
    PerfInsights /run custom xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Můžete vyhledat všechny dostupné scénáře a možnosti pomocí **/list** příkaz:
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Před spuštěním scénáři, PerfInsights výzvu k shodě sdílet diagnostické informace a souhlas s smlouvu EULA. Použití **/AcceptDisclaimerAndShareDiagnostics** možnost pro přeskočení tyto výzvy. 
    >
    >Pokud máte aktivní lístku s Microsoft a spuštěné PerfInsights na žádosti o práci se pracovník podpory, nezapomeňte poskytnout na podporu lístku číslo pomocí **/sr** možnost.
    >
    >Ve výchozím nastavení PerfInsights to samotný aktualizaci na nejnovější verzi, pokud je k dispozici. Použití **/SkipAutoUpdate** nebo **/sau** parametru pro přeskočení automatické aktualizace.  
    >
    >Pokud doba trvání přepínač **/d** není zadán, PerfInsights vás vyzve nepodařilo potíže při spouštění vmslow, azurefiles a vlastní scénáře. 

Po dokončení trasování nebo operations nový soubor se zobrazí v D:\\protokolu\_kolekce (nebo dočasné jednotce). Název souboru je **CollectedData\_rrrr MM-dd\_hh\_mm\_ss.zip.** Můžete odeslat tento soubor do agenta podporu pro analýzu nebo otevřete sestavu v souboru zip ke kontrole zjištění a doporučení.

## <a name="review-the-diagnostics-report"></a>Zkontrolujte výsledky diagnostiky

V rámci **CollectedData\_rrrr MM-dd\_hh\_mm\_ss.zip** soubor můžete najít s podrobnými informacemi o zjištění PerfInsights sestavu ve formátu HTML. Zkontrolujte sestavu, rozbalte **CollectedData\_rrrr MM-dd\_hh\_mm\_ss.zip** souboru a pak otevřete **PerfInsights Report.html** souboru.

Vyberte **zjištění** kartě.

![Snímek obrazovky sestavy PerfInsights](media/how-to-use-perfInsights/findingtab.png)
![snímek sestavy PerfInsights](media/how-to-use-perfInsights/findings.PNG)

> [!NOTE] 
> Zjištění, které jsou klasifikovány jako kritické známé problémy, které by mohly způsobit problémy s výkonem. Zjištění klasifikovány jako důležité představují-optimální konfigurace, které nutně nezpůsobí problémy s výkonem. Zjištění, které jsou klasifikovány jako informační jsou pouze informativní příkazy.

Zkontrolujte doporučení a odkazy na všechny výsledky kritická a důležitá. Informace o tom, jak může ovlivnit výkon a také o osvědčených postupech pro výkonu optimalizované konfigurace.

### <a name="storage-tab"></a>Karta úložiště

**Zjištění** části se zobrazují různé zjištění a doporučení týkající se úložiště.

**Disku mapy** a **svazku mapy** části popisují, jak logické svazky a fyzické disky, které se vztahují k sobě navzájem.

V pohledu fyzický disk (Disk Map) v tabulce jsou uvedeny všechny logické svazky, které běží na disku. V následujícím příkladu **PhysicalDrive2** spouští dva logické svazků vytvořených na více oddílů (J a H):

![Snímek obrazovky s kartou disku](media/how-to-use-perfInsights/disktab.png)

Tabulky v pohledu svazek (svazku Map) zobrazují všechny fyzické disky v rámci každé logické svazku. Všimněte si, že pro RAID nebo dynamické disky, můžete spustit logický svazek na několik fyzických disků. V následujícím příkladu *C:\\připojit* přípojný bod je nakonfigurovaný jako *SpannedDisk* na fyzických discích, 2 a 3:

![Snímek obrazovky s kartou svazku](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-tab"></a>Karta SQL

Pokud cílový počítač hostuje všechny instance systému SQL Server, se zobrazí další karta v sestavě, s názvem **SQL**:

![Snímek obrazovky SQL karta](media/how-to-use-perfInsights/sqltab.png)

Tato část obsahuje **zjištění** karta a další karty pro každý z instance systému SQL Server, který je hostitelem virtuálního počítače.

**Zjištění** karta obsahuje seznam všech SQL související s výkonem najde, společně s doporučení.

V následujícím příkladu **PhysicalDrive0** (spouštění jednotka C) se zobrazí. Je to proto, jak **modeldev** a **modellog** soubory jsou umístěny na jednotce C a jsou různých typů (jako je například datový soubor a protokol transakcí, v uvedeném pořadí).

![Snímek obrazovky s informacemi v protokolu](media/how-to-use-perfInsights/loginfo.png)

Karty pro určité instance systému SQL Server, obsahují obecné oddíl, který zobrazuje základní informace o vybrané instanci. Karty také obsahovat další oddíly pro rozšířené informace, včetně nastavení, konfigurace a možnosti uživatele.

### <a name="diagnostic-tab"></a>Karta diagnostiky
**Diagnostiky** karta obsahuje informace o hlavních spotřebitelů procesoru, paměti a disku v počítači po dobu trvání spuštění PerfInsights. Můžete také získat informace o důležitých oprav, systém může být chybějící, seznamu úloh a důležité systémové události. 

## <a name="references-to-the-external-tools-used"></a>Odkazy na externí nástroje použít

### <a name="diskspd"></a>Diskspd

Diskspd je úložiště zatížení generátor a výkon testu nástroj od společnosti Microsoft. Další informace najdete v tématu [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>Xperf

XPerf je nástroj příkazového řádku pro zachycení trasování z nástrojů výkonu systému Windows. Další informace najdete v tématu [nástrojů výkonu systému Windows – Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>Další postup

Diagnostické protokoly a sestavy můžete uložit do Microsoft Support pro další kontrolu. Podpora si mohou vyžádat přenosu výstupu, který je generován PerfInsights vám pomůže při proces řešení potíží.

Následující snímek obrazovky ukazuje podobná co může se zobrazit zpráva:

![Snímek obrazovky ukázkovou zprávu z Microsoft Support](media/how-to-use-perfInsights/supportemail.png)

Postupujte podle pokynů ve zprávě pro přístup k pracovním prostoru pro přenos souborů. Pro dodatečné zabezpečení budete muset změnit heslo při prvním použití.

Po přihlášení, zjistíte, dialogové okno pro odeslání **CollectedData\_rrrr MM-dd\_hh\_mm\_ss.zip** soubor, který byl shromážděný nástrojem PerfInsights.
