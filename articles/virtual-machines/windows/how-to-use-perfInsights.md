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
ms.openlocfilehash: f15875610e2035c6f4c10c36e19c02f3e045b3ea
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2018
---
# <a name="how-to-use-perfinsights"></a>Jak používat PerfInsights 

[PerfInsights](http://aka.ms/perfinsightsdownload) je automatizovaného skriptu, který shromažďuje užitečné diagnostické informace. Také běží přízvuk zatížení vstupu a výstupu a poskytuje sestavy analýzy k řešení potíží s výkonem virtuálního počítače Windows v Azure. To lze spustit na virtuálních počítačích jako samostatný skript, nebo přímo z portálu nainstalováním [rozšíření virtuálního počítače diagnostiky výkonu Azure](performance-diagnostics-vm-extension.md).

Pokud dochází k problémům s výkonem s virtuálními počítači, než se obrátíte na podporu, spusťte tento skript.

## <a name="supported-troubleshooting-scenarios"></a>Podporované scénáře řešení potíží

PerfInsights můžete shromažďovat a analyzovat několik druhů informace. Následující části se věnují běžné scénáře.

### <a name="collect-basic-configuration"></a>Shromažďovat základní konfigurace 

Tento scénář shromažďuje konfigurace disku a další důležité informace, včetně:

-   Protokoly událostí

-   Stav sítě pro všechny příchozí a odchozí připojení

-   Konfigurace nastavení sítě a brány firewall

-   Seznam úloh pro všechny aplikace, které jsou aktuálně spuštěny v systému

-   Soubor s informacemi o vytvořené msinfo32 pro virtuální počítač

-   Nastavení konfigurace databáze serveru Microsoft SQL Server (Pokud je virtuální počítač se identifikuje jako server, který se systémem SQL Server)

-   Čítače spolehlivost úložiště

-   Důležité opravy hotfix pro Windows

-   Ovladače nainstalované filtru

Toto je pasivní kolekce informace, které by neměly vliv na systém. 

>[!Note]
>Tento scénář je automaticky zahrnuty v každé z následujících scénářů.

### <a name="benchmarking"></a>Srovnávací testy

Tento scénář běží [Diskspd](https://github.com/Microsoft/diskspd) srovnávacího testu (IOPS a MB/s) pro všechny disky, které jsou připojené k virtuálnímu počítači. 

> [!Note]
> Tento scénář může mít vliv na systém a nesmí se spouštět na systému za provozu produkční. V případě potřeby spusťte tento scénář v vyhrazené údržby vyhnuli potížím. Zvýšit zatížení, která je způsobena trasování nebo srovnávacího testu testovací může nepříznivě ovlivnit výkon virtuálního počítače.
>

### <a name="slow-vm-analysis"></a>Pomalé analysis virtuálních počítačů 

Tento scénář spustí [čítače výkonu](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) trasování pomocí čítačů, které jsou určené v souboru Generalcounters.txt. Pokud virtuální počítač se identifikuje jako server, který je spuštěn SQL Server, spustí se trasování čítače výkonu. Dělá to tak, a to pomocí čítače, které se nacházejí v souboru Sqlcounters.txt a zahrnuje také diagnostická data výkonu.

### <a name="slow-vm-analysis-and-benchmarking"></a>Pomalé analýzy virtuálních počítačů a srovnávací testy

Tento scénář spustí [čítače výkonu](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) trasování, který je následován [Diskspd](https://github.com/Microsoft/diskspd) srovnávacího testu. 

> [!Note]
> Tento scénář může mít vliv na systém a nesmí se spouštět na systému za provozu produkční. V případě potřeby spusťte tento scénář v vyhrazené údržby vyhnuli potížím. Zvýšit zatížení, která je způsobena trasování nebo srovnávacího testu testovací může nepříznivě ovlivnit výkon virtuálního počítače.
>

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

Pokud spustíte vlastní pomalé analysis virtuálních počítačů, můžete vybrat trasování do souběžně. Je bylo možné spouštět všechny (čítače výkonu, Xperf, sítě a StorPort) Chcete-li. Po dokončení trasování je nástroj spuštěn srovnávacího testu Diskspd, pokud je zaškrtnuto. 

> [!Note]
> Tento scénář může mít vliv na systém a nesmí se spouštět na systému za provozu produkční. V případě potřeby spusťte tento scénář v vyhrazené údržby vyhnuli potížím. Zvýšit zatížení, která je způsobena trasování nebo srovnávacího testu testovací může nepříznivě ovlivnit výkon virtuálního počítače.
>

## <a name="what-kind-of-information-is-collected-by-the-script"></a>Jaký druh informací se shromažďují skript?

Zaznamená informace o virtuální počítač s Windows, disky nebo konfigurace fondů úložiště, čítače výkonu, a jsou shromažďovány různé trasování. To závisí na výkonu scénáře, který používáte. Podrobnosti naleznete v následující tabulce:

|Data shromážděná                              |  |  | Scénáře výkonu |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                              | Shromažďovat základní konfigurace | Srovnávací testy | Pomalé analysis virtuálních počítačů | Pomalé analýzy virtuálních počítačů a srovnávací testy | Azure analysis soubory | Vlastní pomalé analysis virtuálních počítačů |
| Informace z protokolů událostí      | Ano                        | Ano                                | Ano                      | Ano                            | Ano                  | Ano                  |
| Informace o systému               | Ano                        | Ano                                | Ano                      | Ano                            | Ano                  | Ano                  |
| Svazek mapy                       | Ano                        | Ano                                | Ano                      | Ano                            | Ano                  | Ano                  |
| Mapa disku                         | Ano                        | Ano                                | Ano                      | Ano                            | Ano                  | Ano                  |
| Spuštěné úkoly                    | Ano                        | Ano                                | Ano                      | Ano                            | Ano                  | Ano                  |
| Čítače spolehlivost úložiště     | Ano                        | Ano                                | Ano                      | Ano                            | Ano                  | Ano                  |
| Informace o úložiště              | Ano                        | Ano                                | Ano                      | Ano                            | Ano                  | Ano                  |
| Výstup fsutil                    | Ano                        | Ano                                | Ano                      | Ano                            | Ano                  | Ano                  |
| Informace o ovladač filtru               | Ano                        | Ano                                | Ano                      | Ano                            | Ano                  | Ano                  |
| Výstup příkazu netstat                   | Ano                        | Ano                                | Ano                      | Ano                            | Ano                  | Ano                  |
| Konfigurace sítě            | Ano                        | Ano                                | Ano                      | Ano                            | Ano                  | Ano                  |
| Konfigurace brány firewall           | Ano                        | Ano                                | Ano                      | Ano                            | Ano                  | Ano                  |
| Konfigurace systému SQL Server         | Ano                        | Ano                                | Ano                      | Ano                            | Ano                  | Ano                  |
| Trasování diagnostiky výkonu * | Ano                        | Ano                                | Ano                      |                                | Ano                  | Ano                  |
| Trasování čítače výkonu **     |                            |                                    |                          |                                |                      | Ano                  |
| Trasování čítač SMB **             |                            |                                    |                          |                                | Ano                  |                      |
| Čítač trasování systému SQL Server **      |                            |                                    |                          |                                |                      | Ano                  |
| XPerf trasování                      |                            |                                    |                          |                                |                      | Ano                  |
| StorPort trasování                   |                            |                                    |                          |                                |                      | Ano                  |
| Trasování sítě                    |                            |                                    |                          |                                | Ano                  | Ano                  |
| Trasování srovnávacího testu Diskspd ***      |                            | Ano                                |                          | Ano                            |                      |                      |
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

## <a name="run-the-perfinsights-script-on-your-vm"></a>Spusťte skript PerfInsights na vašem virtuálním počítači

### <a name="what-do-i-have-to-know-before-i-run-the-script"></a>Co je nutné vědět před I spusťte skript? 

#### <a name="script-requirements"></a>Požadavky na skript

-  Tento skript musí spustit na virtuálním počítači, který má problémy s výkonem. 

-  Podporovány jsou následující operační systémy: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 a Windows Server 2016; Windows 8.1 a Windows 10.

#### <a name="possible-problems-when-you-run-the-script-on-production-vms"></a>Možné problémy při spuštění skriptu na produkční virtuální počítače

-  Pro všechny scénáře testu typovou úlohou nebo scénář "Vlastní pomalé analysis virtuálního počítače", který je nakonfigurovaný na použití Xperf nebo nástroje Diskspd skript může nepříznivě ovlivnit výkon virtuálního počítače. Tyto scénáře nespouštějte v produkčním prostředí.

-  Pro všechny scénáře testu typovou úlohou nebo scénář "Vlastní pomalé analysis virtuálního počítače", který je nakonfigurovaný na použití nástroje Diskspd Ujistěte se, že žádné další aktivita na pozadí naruší vstupně-výstupní úlohy.

-  Ve výchozím nastavení používá skript ke shromažďování dat na jednotku dočasné úložiště. Pokud trasování zůstane povolena delší dobu, může být množství dat, které jsou shromážděny relevantní. To může snížit dostupnost místa na disku dočasné a proto může ovlivnit všechny aplikace, které jsou závislé na této jednotce.

### <a name="how-do-i-run-perfinsights"></a>Jak spustit PerfInsights? 

PerfInsights můžete spustit na virtuálním počítači nainstalováním [rozšíření virtuálního počítače diagnostiky výkonu Azure](performance-diagnostics-vm-extension.md). Můžete je také spustit jako samostatný skript. 

**Nainstalujte a spusťte PerfInsights z portálu Azure**

Další informace o této možnosti najdete v tématu [instalaci rozšíření Azure výkon diagnostiky virtuálního počítače](performance-diagnostics-vm-extension.md#install-the-extension).  

**Spusťte skript PerfInsights v samostatném režimu**

Pokud chcete spustit skript PerfInsights, postupujte takto:


1. Stáhněte si [PerfInsights.zip](http://aka.ms/perfinsightsdownload).

2. Odblokování PerfInsights.zip souboru. Chcete-li to provést, klikněte pravým tlačítkem na soubor PerfInsights.zip a vyberte **vlastnosti**. V **Obecné** vyberte **Odblokovat**a potom vyberte **OK**. To zajišťuje, že skript běží bez jakékoli další bezpečnostní zobrazí výzvu.  

    ![Snímek obrazovky PerfInsights vlastnosti, se zvýrazněnou odblokovat](media/how-to-use-perfInsights/unlock-file.png)

3.  Rozbalte komprimovaný soubor PerfInsights.zip do dočasné jednotky (ve výchozím nastavení, to je obvykle diskovou jednotku d). Komprimovaný soubor musí obsahovat následující soubory a složky:

    ![Snímek obrazovky soubory ve složce zip](media/how-to-use-perfInsights/file-folder.png)

4.  Otevřete prostředí Windows PowerShell jako správce a spusťte skript PerfInsights.ps1.

    ```
    cd <the path of PerfInsights folder >
    Powershell.exe -ExecutionPolicy UnRestricted -NoProfile -File .\\PerfInsights.ps1
    ```

    Můžete chtít zadejte "y", potvrďte, že chcete změnit zásady spouštění.

    V **Všimněte si a svůj souhlas** dialogové okno, máte možnost sdílet diagnostické informace s Microsoft Support. Budete také musí souhlasit s licenční smlouvy pokračujte. Proveďte požadovaná nastavení a potom vyberte **spustit skript**.

    ![Dialogové okno snímek obrazovky upozornění a souhlasu](media/how-to-use-perfInsights/disclaimer.png)

5.  Pokud je dostupná, když spustíte skript pro odeslání číslo případu. Pak vyberte **OK**.
    
    ![Snímek obrazovky dialogového okna ID podpory](media/how-to-use-perfInsights/enter-support-number.png)

6.  Vyberte jednotku dočasné úložiště. Skript může automaticky rozpoznat písmeno jednotky. Dojde-li všechny problémy v této fázi, může být vyzvání k vyberte jednotky (výchozí jednotce je D). Protokolů vytvořených jsou zde uložené v protokolu\_složky kolekce. Po zadání nebo přijměte písmeno jednotky, vyberte **OK**.

    ![Snímek obrazovky dialogového okna jednotku](media/how-to-use-perfInsights/enter-drive.png)

7.  Vyberte ze seznamu zadaný odstraňování potíží.

       ![Snímek obrazovky seznamu scénáře řešení potíží](media/how-to-use-perfInsights/select-scenarios.png)

Můžete také spustit PerfInsights bez uživatelského rozhraní. Tento příkaz spustí "analysis pomalé virtuálních počítačů" řešení potíží s scénář bez uživatelského rozhraní. Budete vyzváni k souhlas s právní omezení a smlouva EULA, které jsou uvedené v kroku 4.

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30"

Pokud chcete PerfInsights spouštět v bezobslužném režimu, použijte **- AcceptDisclaimerAndShareDiagnostics** parametr. Například použijte následující příkaz:

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30 -AcceptDisclaimerAndShareDiagnostics"

### <a name="how-do-i-troubleshoot-issues-while-running-the-script"></a>Jak řešit problémy při spouštění skriptu?

Pokud skript ukončí neobvyklým způsobem, můžete se vrátit do konzistentního stavu spuštěním skriptu společně s přepínačem čištění následujícím způsobem:

    powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -Cleanup"

Pokud všechny problémy, ke kterým došlo během automatické zjišťování dočasné jednotce, může být vyzvání k vyberte jednotky (výchozí jednotce je D).

![Snímek obrazovky dialogového okna jednotku](media/how-to-use-perfInsights/enter-drive.png)

Skript odinstaluje nástroj nástroje a odebere dočasné složky.

### <a name="troubleshoot-other-script-issues"></a>Řešení potíží s další problémy skriptu 

Dojde-li potíže při spuštění skriptu, stiskněte kombinaci kláves Ctrl + C přerušení skript. Pokud budete pokračovat i po několika pokusech dojít k selhání skriptu, spusťte skript v režimu ladění pomocí "-Debug" parametr možnost při spuštění.

Po dojde k selhání, zkopírujte úplný výstup konzoly prostředí PowerShell a odeslat do agenta nástroje Microsoft Support, který pomáhá vám pomoci při řešení problému.

### <a name="how-do-i-run-the-script-in-custom-slow-vm-analysis-mode"></a>Jak spustit skript v režimu "Vlastní pomalé analysis virtuálních počítačů"?

Výběrem **vlastní pomalé virtuálních počítačů analysis**, můžete povolit několik trasování paralelně (k výběru několika trasování, použijte klávesu Shift):

![Snímek obrazovky seznam scénářů](media/how-to-use-perfInsights/select-scenario.png)

Když vyberete trasování čítače výkonu, trasování Xperf, síťové trasování nebo scénářů, Storport trasování, postupujte podle pokynů v dialogových oknech následné. Pokuste se znovu provést problémy s pomalým výkonem po spuštění trasování.

Spuštění trasování pomocí dialogového okna následující:

![Dialogové okno snímek obrazovky z spustit výkonu čítač trasování](media/how-to-use-perfInsights/start-trace-message.png)

Chcete-li zastavit trasování, budete muset potvrzení příkazu v dialogovém okně druhý.

![Dialogové okno snímek obrazovky z zastavení výkonu čítač trasování](media/how-to-use-perfInsights/stop-trace-message.png)
![– snímek obrazovky o zastavení všech trasování dialogové okno](media/how-to-use-perfInsights/ok-trace-message.png)

Po dokončení trasování nebo operations nový soubor se zobrazí v D:\\protokolu\_kolekce (nebo dočasné jednotce). Název souboru je **CollectedData\_rrrr MM-dd\_hh\_mm\_ss.zip.** Tento soubor můžete odeslat do agenta podporu pro analýzu.

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
