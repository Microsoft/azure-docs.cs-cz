---
title: Azure Data Lake Storage Gen1 Storm výkonu pokyny k ladění | Dokumentace Microsoftu
description: Azure Data Lake Storage Gen1 Storm výkonu pokyny k ladění
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: aa4d42a53e6fb8ea236a9d544102aab3dff19013
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129229"
---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Průvodce laděním výkonu pro Storm v HDInsight a Azure Data Lake Storage Gen1

Seznamte se s faktory, které by se měly zvažovat při optimalizaci výkonu topologii Storm v Azure. Například je důležité pochopit vlastnosti práce provedené spoutů a boltů (Určuje, zda práce se vstupně-výstupních operací nebo paměť). Tento článek popisuje celou řadu pokyny, včetně řešení běžných potíží pro optimalizaci výkonu.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen1**. Pokyny k jeho vytvoření najdete v tématu [Začínáme s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Cluster Azure HDInsight** s přístupem k účtu Data Lake Storage Gen1. Zobrazit [vytvoření clusteru HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Ujistěte se, že se že povolení vzdálené plochy pro cluster.
* **S clusterem Storm v Data Lake Storage Gen1**. Další informace najdete v tématu [Storm v HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-overview).
* **Pokyny pro Data Lake Storage Gen1 pro optimalizaci výkonu**.  Obecné informace o výkonu koncepty, najdete v části [Data Lake Storage Gen1 ladění Průvodce výkonem](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance).  

## <a name="tune-the-parallelism-of-the-topology"></a>Vyladění paralelismus topologii

Je možné zvýšit souběžnost vstupně-výstupní operace z Data Lake Storage Gen1 a zlepšit výkon. Topologie Storm obsahuje sadu konfigurací, které určují paralelismus:
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

### <a name="get-the-best-performance-from-data-lake-storage-gen1"></a>Získání nejlepšího výkonu z Data Lake Storage Gen1

Při práci s Data Lake Storage Gen1, získáte nejlepší výkon, pokud nastavíte takto:
* COALESCE malou připojí do větší velikosti (v ideálním případě 4 MB).
* Proveďte jako libovolný počet souběžných požadavků. Protože každý podproces bolt dělá blokující operace čtení, budete chtít mít někde v rozsahu od 8 12 vláken na jádro. To zajišťuje síťového adaptéru a také využívá procesor. Větší virtuální počítač umožňuje víc souběžných požadavků.  

### <a name="example-topology"></a>Ukázkové topologie

Předpokládejme, že máte cluster 8 pracovních uzlů v případě virtuálních počítačů Azure D13v2. Tento virtuální počítač má 8 jader, takže mezi 8 pracovních uzlů, budete mít 64 celkový počet jader.

Řekněme, že děláme 8 vláken bolt na jádro. Zadaný 64 jádra, to znamená, že chceme 512 celkový bolt prováděcí modul instance (vláken). V takovém případě Řekněme, že jsme začínat jeden JVM na virtuální počítač a používají převážně souběžnosti vláken v rámci JVM dosáhnout souběžnosti. To znamená, že potřebujeme 8 úlohy pracovního procesu (jeden do každého virtuálního počítače Azure) a moduly provádění 512 bolt. V této konfiguraci Storm se pokusí distribuovat pracovních procesů rovnoměrně mezi uzly pracovního procesu (označované také jako dohledové uzly), poskytuje každý pracovní uzel 1 JVM. Teď v rámci správců, se pokusí distribuovat prováděcí moduly rovnoměrně mezi nadřízeným Storm, poskytuje každý správce (JVM) 8 vlákny každý.

## <a name="tune-additional-parameters"></a>Ladit další parametry
Jakmile budete mít základní topologie, můžete zvážit, jestli chcete upravit některou parametry:
* **Počet JVMs na pracovní uzel.** Pokud máte strukturu velkých objemů dat (například vyhledávací tabulky), které je hostují v paměti, každý JVM vyžaduje samostatnou kopii. Alternativně můžete použít strukturu dat napříč několika vlákny, pokud máte méně JVMs. Pro vstupně-výstupních operací bolt počet JVMs Nedovolte, aby byly co nejvíce rozdíl jako počet vláken přidali napříč těmito JVMs. Pro jednoduchost je dobré mít jeden JVM za pracovního procesu. V závislosti na tom, co dělá váš bolt nebo jaké aplikace zpracování, které vyžadují, i když může být nutné změnit toto číslo.
* **Počet spout prováděcí moduly.** Protože předchozí příklad používá k zápisu do Data Lake Storage Gen1 bolty, počet spoutů není přímo souvisí s výkonem bolt. V závislosti na objemu zpracování nebo probíhající spout vstupně-výstupních operací, je však vhodné optimalizovat spoutů pro zajištění nejlepšího výkonu. Ujistěte se, že máte dostatek spoutů mohli udržovat zaneprázdněný boltů. Výstup sazby spoutů by měl odpovídat propustnost boltů. Skutečné konfigurace závisí spout.
* **Počet úloh.** Každý bolt běží jako jedno vlákno. Další úkoly na bolt neposkytují žádné další souběžnosti. Na kterých výhod se pouze pokud váš proces potvrdil řazené kolekce členů má velkou část času provádění funkcí bolt. Je vhodné do skupiny, že mnoho řazených kolekcí členů do větší připojení před odesláním potvrzení z bolt. Takže ve většině případů poskytují více úkolů žádné další výhody.
* **Místní nebo seskupení náhodně.** Když je toto nastavení povolené, řazené kolekce členů se odesílají do funkcí bolts v rámci stejného pracovního procesu. To snižuje volání meziprocesové komunikace a sítě. To se doporučuje pro většinu topologií.

Tento základní scénář je dobrým výchozím bodem. Test s vlastními daty upravit předchozí parametry pro zajištění optimálního výkonu.

## <a name="tune-the-spout"></a>Ladění spout

Můžete upravit následující nastavení a vylaďte spout.

- **Časový limit řazené kolekce členů: topology.message.timeout.secs**. Toto nastavení určuje dobu zprávu potřebný k dokončení a zobrazí potvrzení, než bude považován za se nezdařilo.

- **Maximální velikost paměti na pracovní proces: worker.childopts**. Toto nastavení umožňuje určit další parametry příkazového řádku pracovníkům Java. Nejčastěji používaná nastavení tady je XmX, která určuje maximální velikost paměti přidělené JVM haldy.

- **Spout maximální počet čekajících: topology.max.spout.pending**. Toto nastavení určuje počet řazených kolekcí členů, které mohou být v letu (ještě nebyla potvrzena na všechny uzly v topologii) na vlákno spout kdykoli.

 Dobré výpočtu provedete je odhadovat velikost všech vašich řazené kolekce členů. Poté zjistěte, kolik paměti jeden spout vlákno má. Celková paměť přidělená pro vlákno, rozdělené podle této hodnoty by vám měl dát horní mez pro maximální spout čekající na parametr.

## <a name="tune-the-bolt"></a>Vyladění bolt
Při psaní do Data Lake Storage Gen1, nastavte zásady synchronizace velikost (vyrovnávací paměti na straně klienta) na 4 MB. Vyprazdňování nebo hsync() pak proběhne pouze v případě, že je velikost vyrovnávací paměti v této hodnotě. Data Lake Storage Gen1 ovladače na pracovním procesu virtuálního počítače automaticky provede toto ukládání do vyrovnávací paměti, pokud explicitně provádět hsync().

Data Lake Storage Gen1 Storm bolt výchozí má parametr zásad synchronizace velikost (fileBufferSize), který slouží k ladění tohoto parametru.

Můžu vstupně-výstupními operacemi topologie je vhodné mít každý podproces bolt zapisovat do vlastního souboru a nastavit zásady souborů otočení (fileRotationSize). Pokud soubor dosáhne určité velikosti, automaticky vyprázdní datový proud a je zapsaný do nového souboru. Velikost doporučená soubor pro rotaci je 1 GB.

### <a name="handle-tuple-data"></a>Zpracování dat řazené kolekce členů

V Storm spout udržuje řazené kolekce členů dokud ho explicitně potvrzena funkcí bolt. Pokud řazená kolekce členů byl přečten funkcí bolt, ale nebyl dosud potvrzeno, spout nemusí nebyla uložena do Data Lake Storage Gen1 back-endu. Po potvrzení řazené kolekce členů spout může zaručit trvalost funkcí bolt a pak můžete z jakéhokoli zdroje je čtení z odstranit zdroj dat.  

Pro zajištění nejlepšího výkonu v Data Lake Storage Gen1 mají bolt vyrovnávací paměti 4 MB dat řazené kolekce členů. Potom zapište do Data Lake Storage Gen1 zpět koncový jako jeden zápis 4 MB. Po data byla úspěšně zapsána do úložiště (podle volání hflush()) bolt můžete potvrdit data zpět spout. To je, co dělá zadaný v tomto příkladu bolt. Také je přijatelné pro uchování větší počet řazených kolekcí členů, než je uskutečněn hovor hflush() a řazených kolekcí členů potvrzených. Tím se ale zvyšuje počet řazených kolekcí členů v letu, že spout musí obsahovat, a proto zvýšení množství paměti požadované na JVM.

> [!NOTE]
Aplikace může požadovat potvrzení řazené kolekce členů častěji (na velikosti dat menší než 4 MB) z jiných důvodů než výkonu. Nicméně, která může ovlivnit propustnost vstupně-výstupní operace úložiště back-endu. Pečlivě zvažují tento kompromis proti bolt vstupně-výstupní výkon.

Pokud není vysoký počet příchozích řazených kolekcí členů, tak vyrovnávací paměti 4 MB trvá dlouhou dobu na to, zvažte snížení rizik souvisejících s tím, že:
* Snížení počtu bolty, takže jsou menší počet vyrovnávacích pamětí tak, aby vyplnil.
* S časovou synchronizací nebo na základě počtu zásad, kde je hflush() aktivuje každých x vyprázdnění nebo každý y milisekund a řazených kolekcí členů nashromáždili zatím jsou potvrzeny zpět.

Všimněte si, že propustnost v tomto případě je nižší, ale s pomalé frekvence událostí maximální propustnost není největší cíle přesto. Tyto způsoby zmírnění rizik vám pomůže snížit celkový čas, která je potřebná pro řazené kolekce členů mají být předány prostřednictvím úložišti. To může být důležité, jestli chcete, aby kanál v reálném čase i s frekvence událostí nízká. Všimněte si také, že pokud vaše příchozí míra řazené kolekce členů je nízká, je třeba upravit parametr topology.message.timeout_secs tak řazené kolekce členů není vypršení časového limitu, zatímco se zobrazuje ukládány do vyrovnávací paměti nebo zpracovány.

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

### <a name="data-lake-storage-gen1-throttling"></a>Data Lake Storage Gen1 omezování
Pokud dosáhnete limitů šířky pásma poskytuje Gen1 úložiště Data Lake, může se zobrazit selhání úkolů. Pro omezení chyby v protokolech úloh. Zvětšení velikosti kontejneru, můžete zmenšit paralelismu.    

A zkontrolujte, zda jste se získávání omezují, povolte protokolování na straně klienta ladění:

1. V **Ambari** > **Storm** > **Config** > **Advanced storm pracovního log4j**, změnit **&lt;kořenové úrovně = "informace"&gt;** k  **&lt;kořenové úrovně = "debug"&gt;**. Restartujte všechny uzly nebo službu k provedení změn konfigurace.
2. Monitorování topologie Storm protokoly na pracovních uzlech (v části /var/log/storm/worker-artifacts /&lt;TopologyName&gt;/&lt;port&gt;/worker.log) pro Data Lake Storage Gen1 omezování výjimky.

## <a name="next-steps"></a>Další postup
Optimalizace pro Storm může být odkazováno v dalších výkonu [tento blog](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/).

Další příklad spuštění, naleznete v tématu [tohoto objektu na Githubu](https://github.com/hdinsight/storm-performance-automation).
