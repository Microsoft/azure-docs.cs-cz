---
title: Azure Data Lake Storage Gen2 Storm výkonu pokyny k ladění | Dokumentace Microsoftu
description: Azure Data Lake Storage Gen2 Storm výkonu pokyny k ladění
services: storage
author: swums
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: stewu
ms.openlocfilehash: 5d7b798c66ec6512c8badcccbf36d6f2f0d50e3b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882943"
---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-storage-gen2"></a>Průvodce laděním výkonu pro Storm v HDInsight a Azure Data Lake Storage Gen2

Seznamte se s faktory, které by se měly zvažovat při optimalizaci výkonu topologii Storm v Azure. Například je důležité pochopit vlastnosti práce provedené spoutů a boltů (Určuje, zda práce se vstupně-výstupních operací nebo paměť). Tento článek popisuje celou řadu pokyny, včetně řešení běžných potíží pro optimalizaci výkonu.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen2**. Pokyny k jeho vytvoření najdete v tématu [rychlý start: Vytvořit úložiště účtů pro analytické](data-lake-storage-quickstart-create-account.md).
* **Azure HDInsight cluster** s přístupem k účtu Data Lake Storage Gen2. Zobrazit [clusterů pomocí Azure Data Lake Storage Gen2 s Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2). Ujistěte se, že se že povolení vzdálené plochy pro cluster.
* **S clusterem Storm v Data Lake Storage Gen2**. Další informace najdete v tématu [Storm v HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-overview).
* **Pokyny pro Data Lake Storage Gen2 pro optimalizaci výkonu**.  Obecné informace o výkonu koncepty, najdete v části [Data Lake Storage Gen2 ladění Průvodce výkonem](data-lake-storage-performance-tuning-guidance.md).   

## <a name="tune-the-parallelism-of-the-topology"></a>Vyladění paralelismus topologii

Je možné zvýšit výkon zvýšením souběžnosti vstupně-výstupních operací do a z Data Lake Storage Gen2. Topologie Storm obsahuje sadu konfigurací, které určují paralelismus:
* Počet pracovních procesů (zaměstnanci jsou rovnoměrně rozloženy mezi virtuální počítače).
* Počet instancí spout prováděcího modulu.
* Počet instancí bolt prováděcího modulu.
* Počet úkolů spout.
* Počet úkolů bolt.

Například v clusteru s 4 virtuální počítače a 4 pracovních procesů, moduly provádění 32 spout a 32 spout úlohy a 256 bolt prováděcí moduly a 512 bolt úkoly, zvažte následující:

Každý správce, což je pracovního uzlu, má jednoho pracovního procesu Java virtual machine (JVM). Tento proces JVM spravuje 4 vlákna spout a 64 bolt vlákna. V rámci každé vlákno se úkoly spouštějí postupně. Předchozí konfiguraci každé vlákno spout má 1 úloha a každé vlákno bolt má 2 úlohy.

V Storm tady se využívá řada různých komponent a jaký vliv mají stupeň paralelismu, ke kterým máte:
* Hlavní uzel (v Storm jako Nimbus) se používá k odesílání a Správa úloh. Tyto uzly mít žádný vliv na míru paralelismu.
* Dohledové uzly. V HDInsight odpovídá pracovním uzlu virtuálního počítače Azure.
* Úlohy pracovního procesu jsou Storm procesy ve virtuálních počítačích. Každý úkol pracovního procesu odpovídá instanci JVM. Storm distribuuje počet pracovních procesů, který zadáte jako rovnoměrně k pracovním uzlům.
* Součástí spout a funkce bolt instancí prováděcího modulu. Každá instance prováděcí modul odpovídá do vlákna běžícího v rámci pracovních procesů (JVMs).
* Úlohy pro Storm. Toto jsou logické úlohy, že každý z těchto spuštění vlákna. Nezmění se stupeň paralelismu, takže by se měl vyhodnotit, pokud potřebujete více úkolů na prováděcí modul, nebo ne.

### <a name="get-the-best-performance-from-data-lake-storage-gen2"></a>Získání nejlepšího výkonu z Data Lake Storage Gen2

Při práci s Data Lake Storage Gen2 je získáte nejlepší výkon, pokud nastavíte takto:
* COALESCE malou připojí do větší velikosti.
* Proveďte jako libovolný počet souběžných požadavků. Protože každý podproces bolt dělá blokující operace čtení, budete chtít mít někde v rozsahu od 8 12 vláken na jádro. To zajišťuje síťového adaptéru a také využívá procesor. Větší virtuální počítač umožňuje víc souběžných požadavků.  

### <a name="example-topology"></a>Ukázkové topologie

Předpokládejme, že máte cluster 8 pracovních uzlů v případě virtuálních počítačů Azure D13v2. Tento virtuální počítač má 8 jader, takže mezi 8 pracovních uzlů, budete mít 64 celkový počet jader.

Řekněme, že děláme 8 vláken bolt na jádro. Zadaný 64 jádra, to znamená, že chceme 512 celkový bolt prováděcí modul instance (vláken). V takovém případě Řekněme, že jsme začínat jeden JVM na virtuální počítač a používají převážně souběžnosti vláken v rámci JVM dosáhnout souběžnosti. To znamená, že potřebujeme 8 úlohy pracovního procesu (jeden do každého virtuálního počítače Azure) a moduly provádění 512 bolt. V této konfiguraci Storm se pokusí distribuovat pracovních procesů rovnoměrně mezi uzly pracovního procesu (označované také jako dohledové uzly), poskytuje každý pracovní uzel 1 JVM. Teď v rámci správců, se pokusí distribuovat prováděcí moduly rovnoměrně mezi nadřízeným Storm, poskytuje každý správce (JVM) 8 vlákny každý.

## <a name="tune-additional-parameters"></a>Ladit další parametry
Jakmile budete mít základní topologie, můžete zvážit, jestli chcete upravit některou parametry:
* **Počet JVMs na pracovní uzel.** Pokud máte strukturu velkých objemů dat (například vyhledávací tabulky), které je hostují v paměti, každý JVM vyžaduje samostatnou kopii. Alternativně můžete použít strukturu dat napříč několika vlákny, pokud máte méně JVMs. Pro vstupně-výstupních operací bolt počet JVMs Nedovolte, aby byly co nejvíce rozdíl jako počet vláken přidali napříč těmito JVMs. Pro jednoduchost je dobré mít jeden JVM za pracovního procesu. V závislosti na tom, co dělá váš bolt nebo jaké aplikace zpracování, které vyžadují, i když může být nutné změnit toto číslo.
* **Počet spout prováděcí moduly.** Protože předchozí příklad používá k zápisu do Data Lake Storage Gen2 bolty, počet spoutů není přímo souvisí s výkonem bolt. V závislosti na objemu zpracování nebo probíhající spout vstupně-výstupních operací, je však vhodné optimalizovat spoutů pro zajištění nejlepšího výkonu. Ujistěte se, že máte dostatek spoutů mohli udržovat zaneprázdněný boltů. Výstup sazby spoutů by měl odpovídat propustnost boltů. Skutečné konfigurace závisí spout.
* **Počet úloh.** Každý bolt běží jako jedno vlákno. Další úkoly na bolt neposkytují žádné další souběžnosti. Na kterých výhod se pouze pokud váš proces potvrdil řazené kolekce členů má velkou část času provádění funkcí bolt. Je vhodné do skupiny, že mnoho řazených kolekcí členů do větší připojení před odesláním potvrzení z bolt. Takže ve většině případů poskytují více úkolů žádné další výhody.
* **Místní nebo seskupení náhodně.** Když je toto nastavení povolené, řazené kolekce členů se odesílají do funkcí bolts v rámci stejného pracovního procesu. To snižuje volání meziprocesové komunikace a sítě. To se doporučuje pro většinu topologií.

Tento základní scénář je dobrým výchozím bodem. Test s vlastními daty upravit předchozí parametry pro zajištění optimálního výkonu.

## <a name="tune-the-spout"></a>Ladění spout

Můžete upravit následující nastavení a vylaďte spout.

- **Časový limit řazené kolekce členů: topology.message.timeout.secs**. Toto nastavení určuje dobu zprávu potřebný k dokončení a zobrazí potvrzení, než bude považován za se nezdařilo.

- **Maximální velikost paměti na pracovní proces: worker.childopts**. Toto nastavení umožňuje určit další parametry příkazového řádku pracovníkům Java. Nejčastěji používaná nastavení tady je XmX, která určuje maximální velikost paměti přidělené JVM haldy.

- **Spout maximální počet čekajících: topology.max.spout.pending**. Toto nastavení určuje počet řazených kolekcí členů, které mohou být v letu (ještě nebyla potvrzena na všechny uzly v topologii) na vlákno spout kdykoli.

 Dobré výpočtu provedete je odhadovat velikost všech vašich řazené kolekce členů. Poté zjistěte, kolik paměti jeden spout vlákno má. Celková paměť přidělená pro vlákno, rozdělené podle této hodnoty by vám měl dát horní mez pro maximální spout čekající na parametr.

Data Lake Storage Gen2 Storm bolt výchozí má parametr zásad synchronizace velikost (fileBufferSize), který slouží k ladění tohoto parametru.

Můžu vstupně-výstupními operacemi topologie je vhodné mít každý podproces bolt zapisovat do vlastního souboru a nastavit zásady souborů otočení (fileRotationSize). Pokud soubor dosáhne určité velikosti, automaticky vyprázdní datový proud a je zapsaný do nového souboru. Velikost doporučená soubor pro rotaci je 1 GB.

## <a name="monitor-your-topology-in-storm"></a>Monitorování topologie ve Stormu  
Zatímco vaše topologie běží, ji můžete sledovat v uživatelském rozhraní Storm. Tady jsou hlavní parametry podívat se na:

* **Latence spuštění celkový proces.** Toto je průměrná doba trvání jedné řazené kolekce členů, protože ho vygeneroval spout, zpracování funkcí bolt a potvrzení.

* **Latence procesu celkový bolt.** Toto je průměrná doba zpracování v řazené kolekci členů na bolt, až do obdržení potvrzení.

* **Latence provedení celkem bolt.** Toto je průměrná doba zpracování funkcí bolt v metodě execute.

* **Počet chyb.** To se vztahuje na počet řazených kolekcí členů, které se nepodařilo plně zpracovány před vypršením časového limitu.

* **Kapacita.** Toto je míra je zatížení systému. Pokud je toto číslo 1, vaše funkce bolts práci tak rychle, jak to jde. Pokud je menší než 1, zvýšení paralelismu. Pokud je větší než 1, omezte paralelismus.

## <a name="troubleshoot-common-problems"></a>S řešením běžných problémů
Tady je několik běžných scénářů řešení potíží.
* **Počet řazených kolekcí členů, se nestíhají.** Podívejte se na každém uzlu v topologii k určení snížení výkonu. Nejčastější příčinou je, že nejsou schopné udržovat tempo s spoutů boltů. To vede k zablokování vnitřní vyrovnávací paměti při čekání na zpracování řazené kolekce členů. Zvažte zvýšení hodnoty časového limitu klesající nebo rostoucí maximální spout čekající.

* **Je latence spuštění vysoký celkový počet procesů, ale latence procesu s nízkou bolt.** V takovém případě je možné, že řazené kolekce členů nejsou potvrzování dostatečně rychle. Zkontrolujte, zda jsou dostatečný počet acknowledgers. Další možností je, že čekání ve frontě příliš dlouho předtím, než funkce bolts spustit jejich zpracování. Snižte max. spout čekající.

* **Je latence provedení vysokou bolt.** To znamená, že metoda execute() vaše bolt trvá moc dlouho. Optimalizace kódu, podívejte se na velikosti zápisů a vyprázdnění chování.

### <a name="data-lake-storage-gen2-throttling"></a>Data Lake Storage Gen2 omezování
Pokud dosáhnete limitů šířky pásma poskytuje Data Lake Storage Gen2, může se zobrazit selhání úkolů. Pro omezení chyby v protokolech úloh. Zvětšení velikosti kontejneru, můžete zmenšit paralelismu.    

A zkontrolujte, zda jste se získávání omezují, povolte protokolování na straně klienta ladění:

1. V **Ambari** > **Storm** > **Config** > **Advanced storm pracovního log4j**, změnit **&lt;kořenové úrovně = "informace"&gt;** k  **&lt;kořenové úrovně = "debug"&gt;**. Restartujte všechny uzly nebo službu k provedení změn konfigurace.
2. Monitorování topologie Storm protokoly na pracovních uzlech (v části /var/log/storm/worker-artifacts /&lt;TopologyName&gt;/&lt;port&gt;/worker.log) pro Data Lake Storage Gen2 omezování výjimky.

## <a name="next-steps"></a>Další postup
Optimalizace pro Storm může být odkazováno v dalších výkonu [tento blog](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/).

Další příklad spuštění, naleznete v tématu [tohoto objektu na Githubu](https://github.com/hdinsight/storm-performance-automation).
