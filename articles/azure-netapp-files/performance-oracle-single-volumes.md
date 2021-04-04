---
title: Výkon databáze Oracle na Azure NetApp Files jednom svazku | Microsoft Docs
description: Popisuje výsledky testu výkonu Azure NetApp Files jednoho svazku v databázi Oracle.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: c6cdf2f6dada0aa4dea2f70f18237b7ee39e3ea1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91571339"
---
# <a name="oracle-database-performance-on-azure-netapp-files-single-volumes"></a>Výkon databází Oracle na jednoduchých svazcích Azure NetApp Files

Tento článek popisuje následující témata týkající se Oracle v cloudu. Tato témata můžou být obzvláště důležitá pro správce databáze, architekta cloudu nebo architekta úložiště:   

* Čím je úloha online zpracování transakcí (OLTP) (většinou náhodných vstupně-výstupních operací) nebo úlohy OLAP (Online Analytical Processing) (většinou sekvenční vstupně-výstupní operace), co vypadá s výkonem?   
* Jaký je rozdíl mezi výkonem normálního klienta systému Linux NFS (kNFS) a klienta pro systém souborů NFS s přímým přístupem k systému souborů Oracle?
* Pokud jde o šířku pásma, je k dispozici výkon jednoho Azure NetApp Filesho svazku?

## <a name="testing-environment-and-components"></a>Testovací prostředí a součásti

Následující diagram znázorňuje prostředí používané pro testování. Pro zajištění konzistence a jednoduchosti byly použity Ansible playbooky pro nasazení všech prvků zkušebního DNA.

![Testovací prostředí Oracle](../media/azure-netapp-files/performance-oracle-test-environment.png)  

### <a name="virtual-machine-configuration"></a>Konfigurace virtuálního počítače

Testy použily pro virtuální počítač následující nastavení:
* Operační systém:   
    RedHat Enterprise Linux 7,8 (wle-ora01)
* Typy instancí:   
    Při testování byly použity dva modely – D32s_v3 a D64s_v3
* Počet síťových rozhraní:   
    Jedna (1) umístěná v podsíti 3  
* Disků   
    Binární soubory a operační systémy Oracle byly umístěny na jeden disk Premium

### <a name="azure-netapp-files-configuration"></a><a name="anf_config"></a>Konfigurace Azure NetApp Files
Testy používaly následující konfiguraci Azure NetApp Files:   

* Velikost fondu kapacity:  
    Byly nakonfigurované různé velikosti fondu: 4 TiB, 8 TiB, 16 TiB, 32 TiB 
* Úroveň služby:  
    Ultra (128 MiB/s šířky pásma na 1 TiB přidělené kapacity svazku)
* Svazků  
    Jeden a dva testy svazků byly vyhodnoceny

### <a name="workload-generator"></a>Generátor úloh 

Testy použily vygenerované úlohy SLOB 2.5.4.2. SLOB (Silly Little Oracle test) je známý generátor úloh v prostoru Oracle, který je určený k zatížení a testování subsystému v/v s využitím fyzického vstupně-výstupního zatížení s SGA vyrovnávací pamětí.  

SLOB 2.5.4.2 nepodporuje připojení k databázi (PDB). V takovém případě se do skriptů a přidala změna, `setup.sh` `runit.sh` aby se do ní přidala podpora PDB.  

Proměnné SLOB použité v testech jsou popsány v následujících částech.

#### <a name="workload-80-select-20-update--random-io--slobconf-variables"></a>Zatížení 80% SELECT, 20% aktualizace | Náhodné vstupně-výstupní operace – `slob.conf` proměnné   

`UPDATE_PCT=20`   
`SCAN_PCT=0`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

#### <a name="workload-100-select--sequential-io--slobconf-variables"></a>Pracovní vytížení 100% vyberte | Sekvenční vstupně-výstupní operace – `slob.conf` proměnné

`UPDATE_PCT=0`   
`SCAN_PCT=100`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

### <a name="database"></a>databáze

Verze Oracle, která se používá pro testy, je Oracle Database Enterprise Edition 19.3.0.0.

Parametry Oracle jsou následující:  
* `sga_max_size`: 4096M
* `sga_target`: 4096
* `db_writer_processes`: 12
* `awr_pdb_autoflush_enabled`: true
* `filesystemio_options`: SETALL
* `log_buffer`: 134217728

Pro databázi SLOB byl vytvořen soubor PDB.

Následující diagram znázorňuje tabulkový prostor s názvem PERFIO s 600 GB (20 datových souborů, 30 GB každého), který se vytvořil pro hostování čtyř uživatelských schémat SLOB. Každé schéma uživatele má velikost 125 GB.

![Databáze Oracle](../media/azure-netapp-files/performance-oracle-tablespace.png)  

## <a name="performance-metrics"></a>Metriky výkonu

Cílem bylo ohlásit vstupně-výstupní výkon, který aplikace má. Proto všechny diagramy v tomto článku používají metriky hlášené databází Oracle prostřednictvím svých sestav úložiště automatických úloh (AWR). Metriky používané v diagramech jsou následující:   

* **Průměrný počet vstupně-výstupních požadavků za sekundu**   
    Odpovídá součtu průměrných požadavků na vstupně-výstupní operace čtení/sec a průměrného počtu požadavků na vstupně-výstupní operace zápisu za sekundu z oddílu zátěžového profilu.
* **Průměr v/v MB/s**   
    Odpovídá součtu průměrných vstupně-výstupních operací čtení MB/s a průměrného zápisu v/v za sekundu v části profil zatížení.
* **Průměrná latence čtení**   
    Odpovídá průměrné latenci události čekání Oracle "sekvenční čtení souboru" v mikrosekundách
* **Počet vláken/schémat**   
    Odpovídá počtu vláken SLOB na schéma uživatele.

## <a name="performance-measurement-results"></a>Výsledky měření výkonu  

Tato část popisuje výsledky měření výkonu.

### <a name="linux-knfs-client-vs-oracle-direct-nfs"></a>Klient pro Linux kNFS vs. Oracle Direct NFS

Tento scénář je spuštěný na virtuálním počítači Azure Standard_D32s_v3 (Intel E5-2673 V4 @ 2,30 GHz). Zatížení je 75% SELECT a 25% aktualizace, většinou náhodných vstupně-výstupních operací, a s dosažením vyrovnávací paměti databáze ~ 7,5%. 

Jak je znázorněno v následujícím diagramu, klient Oracle DNFS se doručí až na 2,8 × větší propustnost než běžný klient pro Linux kNFS:  

![Klient pro Linux kNFS v porovnání s přímým přístupem k systému Oracle](../media/azure-netapp-files/performance-oracle-kfns-compared-dnfs.png)  

Následující diagram znázorňuje křivku latence pro operace čtení. V tomto kontextu je kritickým bodem pro klienta kNFS jedno připojení soketu TCP systému souborů NFS navázané mezi klientem a serverem NFS (Azure NetApp Files svazek).  

![Klient pro Linux kNFS v porovnání s křivkou latence systému Oracle Direct NFS](../media/azure-netapp-files/performance-oracle-latency-curve.png)  

Klient DNFS mohl odeslat více vstupně-výstupních požadavků za sekundu, protože umožňuje vytvořit stovky připojení soketu TCP, a díky tomu využít paralelismus. Jak je popsáno v tématu [konfigurace Azure NetApp Files](#anf_config), jednotlivé další TIB kapacity umožňují další 128MiB/s šířky pásma. DNFS topped z 1 GiB/s propustnost, což je limit, který je nastaven na 8-TiB. S ohledem na větší kapacitu by byla zařízena větší propustnost.

Propustnost je pouze jedním z důležitých informací. Dalším aspektem je latence, která má primární dopad na činnost koncového uživatele. Jak ukazuje následující diagram, zvýšení latence je možné očekávat mnohem rychleji s kNFS než s DNFS. 

![Klient pro Linux kNFS v porovnání s latencí čtení v systému Oracle Direct NFS](../media/azure-netapp-files/performance-oracle-read-latency.png)  

Histogramy poskytují vynikající přehled o latencích databáze. Následující diagram nabízí kompletní pohled z perspektivy zaznamenaného sekvenčního čtení souboru databáze při použití DNFS v nejvyšší datovém bodě souběžnosti (32 vláken/schémat). Jak je znázorněno v následujícím diagramu, 47% všech operací čtení bylo dodrženo mezi 512 mikrosekundami a 1000 mikrosekund, zatímco 90% všech operací čtení byla obsluhována latencí pod 2 MS.

![KNFS klient pro Linux v porovnání s histogramy pro Oracle Direct NFS](../media/azure-netapp-files/performance-oracle-histogram-read-latency.png)  

V závěru je jasné, že DNFS se musí nacházet v případě, že se zvyšuje výkon instance databáze Oracle v systému souborů NFS.

### <a name="single-volume-performance-limits"></a>Omezení výkonu s jedním svazkem

Tato část popisuje omezení výkonu jednoho svazku s náhodnými vstupně-výstupními operacemi a sekvenční vstupně-výstupní operace. 

#### <a name="random-io"></a>Náhodné vstupně-výstupní operace

DNFS je schopný spotřebovat mnohem větší šířku pásma, než je k dispozici 8 TB Azure NetApp Files kvóty výkonu. Zvýšením kapacity Azure NetApp Files svazku na 16 TiB, což je okamžitá změna, objem šířky pásma se zvýšil z 1024 MiB/s o dvojnásobek na 2048 MiB/s. 

Následující diagram znázorňuje konfiguraci pro úlohu 80% Select a 20% aktualizace a s poměrem přístupů do vyrovnávací paměti databáze 8%. SLOB byl schopný zařídit jeden svazek na vstupně-výstupní požadavky 200 000 NFS za sekundu. Vzhledem k tomu, že každá operace má 8 – KiB velikost, testovaný systém byl schopný doručovat ~ 200 000 IO požadavky/s nebo 1600 MiB/s.
 
![Propustnost Oracle DNFS](../media/azure-netapp-files/performance-oracle-dnfs-throughput.png)  

Následující diagram zatáčky latence čtení ukazuje, že se při zvýšení propustnosti čtení latence zvyšuje plynule pod 1-MS čárou 165 000 a při průměrné latenci čtení v/v Průměrná doba čtení v ~ 1,3 MS.  Tato hodnota je nestavová latence pro rychlost vstupu a výstupu nedosažitelná s skoro jakoukoli jinou technologií v cloudu Azure. 

![Křivka latence Oracle DNFS](../media/azure-netapp-files/performance-oracle-dnfs-latency-curve.png)  

#### <a name="sequential-io"></a>Sekvenční vstupně-výstupní operace  

Jak je znázorněno v následujícím diagramu, ne všechny vstupně-výstupní operace jsou náhodné a berou v úvahách RMAN zálohování nebo úplnou kontrolu tabulek, například jako úlohy, které vyžadují velkou šířku pásma, jak můžou získat.  Pomocí stejné konfigurace, jak je popsáno dříve, ale se svazkem se změnila velikost 32 TiB, následující diagram ukazuje, že jediná instance Oracle DB může prosazovat vzhůru až 3 900 MB/s propustností, velmi blízko kvóty výkonu Azure NetApp Files svazku 32 TB (128 MB/s × 32 = 4096 MB/s).

![I/O Oracle DNFS](../media/azure-netapp-files/performance-oracle-dnfs-io.png)  

V souhrnu vám Azure NetApp Files pomůže přebírat databáze Oracle do cloudu. Poskytuje výkon, pokud to databáze vyžaduje. Kvótu svazku můžete kdykoli dynamicky a bez přerušení měnit.

## <a name="next-steps"></a>Další kroky

- [Doporučení srovnávacích testů výkonnosti pro Azure NetApp Files](azure-netapp-files-performance-metrics-volumes.md)
- [Srovnávací testy výkonu pro Linux](performance-benchmarks-linux.md)