---
title: Ladění pravidla výkonu Storm Azure Data Lake Store | Microsoft Docs
description: Ladění pravidla výkonu Storm Azure Data Lake Store
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
ms.openlocfilehash: 5ebca90ffd679de1c30d1bc324bf4f1c3b9f6f70
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198856"
---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-store"></a>Pokyny pro Storm v HDInsight a Azure Data Lake Store optimalizace výkonu

Pochopení faktory, které je potřeba zvážit při optimalizaci výkonu topologie Azure Storm. Například je důležité si uvědomit, charakteristiky práci funkcích spouts a funkce bolts (jestli práce je vstupně-výstupních operací nebo náročná na paměť). Tento článek se zabývá řadu pokyny, včetně řešení běžných potíží optimalizace výkonu.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Store**. Pokyny k jeho vytvoření najdete v tématu [Začínáme s Azure Data Lake Store](data-lake-store-get-started-portal.md).
* **Cluster Azure HDInsight** s přístupem k účtu Data Lake Store. V tématu [vytvoření clusteru HDInsight s Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Ujistěte se, že povolení vzdálené plochy pro cluster.
* **Spuštění clusteru Storm v Data Lake Store**. Další informace najdete v tématu [Storm v HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-overview).
* **Pokyny v Data Lake Store ladění výkonu**.  Obecný výkon koncepty, najdete v části [Data Lake Store výkonu ladění pokyny](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance).  

## <a name="tune-the-parallelism-of-the-topology"></a>Vyladění paralelismu topologie

Je možné ke zlepšení výkonu zvýšením souběžnosti vstupně-výstupních operací do a z Data Lake Store. Topologie Storm má nastavení konfigurace, které určují paralelismus:
* Počet pracovních procesů (maximální pracovních procesů jsou rovnoměrně rozdělené mezi virtuálními počítači).
* Počet instancí vykonavatele funkcí spout.
* Počet instancí vykonavatele funkcí bolt.
* Počet funkcí spout úlohy.
* Počet úloh funkcí bolt.

Například v clusteru s 4 virtuální počítače a 4 pracovních procesů, vykonavatelů 32 spout a 32 funkcí spout úkoly a 256 bolt vykonavatelů a 512 bolt úlohy, zvažte následující:

Každý nadřízeného, což je pracovního uzlu, má jednoho pracovního procesu Java virtual machine (JVM). Tento proces JVM spravuje 4 spout vláken a 64 bolt vláken. V rámci každé vlákno se úkoly spouštějí postupně. V předchozí konfiguraci každou funkcí spout vlákno je 1 úkol a každé vlákno bolt má 2 úlohy.

Tady jsou různé součásti související se situací Storm, a jejich vliv na úroveň stupně paralelního zpracování, které máte:
* Z hlavního uzlu (nazývané Nimbus v Storm) se používá k odeslání a správou úloh. Tyto uzly nemají žádný vliv na stupně paralelního zpracování.
* Uzly nadřízeného. V prostředí HDInsight odpovídá do pracovního uzlu virtuálního počítače Azure.
* Úlohy pracovního procesu jsou Storm procesů spuštěných ve virtuálních počítačích. Každý úkol pracovní odpovídá JVM instance. Storm distribuuje počet pracovních procesů, které určíte jako rovnoměrně k pracovním uzlům.
* Spout a funkce bolt vykonavatele instancí. Každá instance vykonavatele odpovídá na vlákno běžící v rámci pracovních procesů (JVMs).
* Storm úlohy. Toto jsou logické úlohy, aby každá z těchto vláken spustit. Tím nezmění úroveň paralelismu, takže byste měli zvážit, pokud potřebujete více úkolů na prováděcího modulu, nebo ne.

### <a name="get-the-best-performance-from-data-lake-store"></a>Získání nejlepší výkon z Data Lake Store

Při práci se službou Data Lake Store, získáte nejlepší výkon, pokud byste udělat následující:
* Sloučení malou připojí do větší velikosti (v ideálním případě 4 MB).
* Počet souběžných požadavků, jak můžete proveďte. Protože každý podproces bolt provádí blokování čtení, budete chtít mít někde v rozsahu 8-12 vláken na jádra. Díky tomu, že síťový adaptér a dobře využito procesoru. Větší virtuální počítač umožňuje více souběžných požadavků.  

### <a name="example-topology"></a>Příklad topologie

Předpokládejme, že máte cluster 8 pracovní uzel s D13v2 virtuální počítač Azure. Tento virtuální počítač nemá 8 jader, takže mezi 8 pracovní uzly, budete mít 64 celkový počet jader.

Řekněme, že provedeme 8 vláken bolt na jádra. Zadaný 64 jader, která znamená, že chceme 512 celkový bolt vykonavatele instancí (vlákna). V takovém případě Řekněme, že jsme začínat jeden JVM na virtuální počítač a používají převážně souběžnosti vláken v rámci systém JVM k dosažení souběžnosti. To znamená, že potřebujeme 8 úlohy pracovního procesu (jeden na každý virtuální počítač Azure) a 512 vykonavatelů funkcí bolt. V této konfiguraci Storm se pokusí distribuovat pracovníci rovnoměrně mezi uzly pracovního procesu (také označované jako nadřízeného uzlů), která poskytuje každý pracovní uzel 1 JVM. Teď v rámci vedoucí, se pokusí distribuovat vykonavatelů rovnoměrně mezi vedoucí Storm, udělíte každý nadřízeného (JVM) 8 vláken každý.

## <a name="tune-additional-parameters"></a>Vyladění dalších parametrů
Až budete mít základní topologie, můžete zvážit, jestli chcete upravit některý z parametrů:
* **Počet JVMs za pracovního uzlu.** Pokud máte velké datové struktury (například vyhledávací tabulky) vyžaduje, aby je hostitel v paměti, každý JVM samostatná kopie. Alternativně můžete použít datovou strukturu napříč velký počet vláken, pokud máte méně JVMs. Pro vstupně-výstupních operací bolt počet JVMs neprovede tolik rozdíl jako počet vláken přidat mezi tyto JVMs. Pro jednoduchost je vhodné mít jeden JVM za pracovního procesu. V závislosti na akci vaše bolt, nebo jaké aplikace je zpracování vyžadují, když budete muset změnit toto číslo.
* **Počet vykonavatelů funkcí spout.** Vzhledem k tomu, že v předchozím příkladu používá funkce bolts pro zápis do Data Lake Store, počet funkcích spouts není přímo relevantní pro výkon funkcí bolt. V závislosti na množství zpracování nebo děje v spout vstupně-výstupních operací, je však vhodné pro optimalizaci funkcích spouts pro nejlepší výkon. Ujistěte se, že máte dostatek funkcích spouts, abyste mohli udržovat funkce bolts zaneprázdněný. Výstup míry funkcích spouts by měl odpovídat propustnost funkce bolts. Skutečné konfigurace závisí na funkcí spout.
* **Počet úloh.** Každý bolt spouští jako jedno vlákno. Další úlohy za bolt neposkytují žádné další souběžnosti. Jenom jednou, které jsou benefitu je, pokud má váš proces to v úvahu řazenou kolekci členů velká část doby provádění funkcí bolt. Je vhodné do skupiny, že mnoho řazených kolekcí členů do větší připojit před odesláním potvrzení z bolt. Ano ve většině případů poskytují více úkolů žádné další výhody.
* **Místní nebo náhodně seskupení.** Pokud je toto nastavení povoleno, řazené kolekce členů se odesílají do funkcí bolts v rámci stejné pracovní proces. Tím se snižuje volání mezi procesy komunikace a sítě. Toto nastavení se doporučuje pro většinu topologií.

Tento základní scénář je to dobrý výchozí bod. Testování s vlastními daty upravit předchozí parametry, které chcete dosáhnout optimálního výkonu.

## <a name="tune-the-spout"></a>Vyladění spout

Můžete upravit následující nastavení a vylaďte funkcí spout.

- **Časový limit řazené kolekce členů: topology.message.timeout.secs**. Toto nastavení určuje dobu zprávu potřebný k dokončení a přijímat potvrzení, považuje se nezdařilo.

- **Maximální paměť na pracovní proces: worker.childopts**. Toto nastavení umožňuje určit další parametry příkazového řádku pracovníkům Java. Nejčastěji používaná nastavení tady je XmX, která určuje maximální velikost paměti přidělené JVM haldě.

- **Maximální počet funkcí spout čekající na vyřízení: topology.max.spout.pending**. Toto nastavení určuje počet řazených kolekcí členů, která může být v cestě (ještě nebyla potvrzena na všechny uzly v topologii) na vlákno spout kdykoli.

 Dobrý výpočtu udělat, je odhad velikosti jednotlivých vaší řazené kolekce členů. Potom rozmyslete si, kolik paměti jeden spout vlákno má. Celkové paměti přidělené na vlákno, dělený tato hodnota by měla poskytují horní mez pro maximální spout čekající na vyřízení parametr.

## <a name="tune-the-bolt"></a>Vyladění bolt
Pokud píšete do Data Lake Store, nastavte zásady synchronizace velikost (vyrovnávací paměti na straně klienta) na 4 MB. Abyste vyprázdnili nebo hsync() pak provádí jenom v případě, že je velikost vyrovnávací paměti v této hodnotě. Data Lake Store ovladač v pracovním procesu virtuálního počítače automaticky nemá tato ukládání do vyrovnávací paměti, pokud provádíte explicitně hsync().

Data Lake Store Storm bolt výchozí obsahuje parametr zásady synchronizace velikost (fileBufferSize), můžete použít k vyladění tento parametr.

V I náročnými topologie je vhodné tak, aby měl každý podproces bolt zapisovat do vlastního souboru a nastavit zásadu otočení souboru (fileRotationSize). Pokud soubor dosáhne určité velikosti, automaticky vyprázdní datového proudu a je zapsaný do nového souboru. Velikost souboru doporučené pro otočení je 1 GB.

### <a name="handle-tuple-data"></a>Zpracování dat řazené kolekce členů

V Storm funkcích spout obsahuje pro řazené kolekce členů dokud je explicitně potvrzené funkcí bolt. Pokud byl načten funkcí bolt řazené kolekce členů, ale nebyl dosud potvrzeny, nemusí mít spout jako trvalý, do Data Lake Store back-end. Po řazené kolekce členů potvrzen, spout můžete záruku stálosti funkcí bolt a můžete pak odstraňte zdrojová data z jakéhokoli zdroje je čtení z.  

Chcete-li dosáhnout optimálního výkonu v Data Lake Store, máte bolt vyrovnávací paměti 4 MB dat řazené kolekce členů. Zapište do Data Lake Store zpět end jeden zápis 4 MB. Po data byla úspěšně zapsána do úložiště (ve volání hflush()) bolt můžete potvrdit data zpět spout. Toto je, jaké jsou zde zadané bolt příklad. Je také přijatelné pro uložení větší počet řazených kolekcí členů, než Přišla žádost hflush() a potvrzené řazené kolekce členů. Nicméně tím se zvyšuje počet řazených kolekcí členů v cestě spout musí obsahovat, a proto zvyšuje velikost paměti požadované pro prostředí Java Virtual Machine.

> [!NOTE]
Aplikace může být potřeba řazených kolekcí členů častěji (na velikosti dat menší než 4 MB) na vědomí z jiných důvodů bez výkonu. Ale, která může ovlivnit propustnost vstupu/výstupu k back-endu úložiště. Pečlivě zvažte tato kompromis proti bolt vstupně-výstupní výkon.

Pokud není vysoký počet příchozích řazené kolekce členů, takže vyrovnávací paměti 4 MB trvá dlouhou dobu k vyplnění, zvažte zmírnění tím, že:
* Snížení počtu funkce bolts, takže jsou méně vyrovnávací paměti k vyplnění.
* S založené na čase nebo na základě počtu zásad, kde je hflush() spustí každých x vyprázdnění nebo každých milisekundách y a řazené kolekce členů nashromáždila, pokud jsou potvrzené zpět.

Všimněte si, že propustnost v tomto případě je nižší, ale s pomalým rychlost událostí, maximální propustnost není největších cíl přesto. Tyto způsoby zmírnění můžete snížit celkovou dobu, která je potřebná pro řazené kolekce členů mají být předány prostřednictvím úložišti. To může být důležité, zda chcete, aby kanál v reálném čase i pomocí rychlost nízkou událostí. Všimněte si, že pokud vaše míra příchozích řazené kolekce členů je nízké, byste měli upravit parametr topology.message.timeout_secs tak řazené kolekce členů není vypršení časového limitu při jejich načítání do vyrovnávací paměti nebo zpracovat.

## <a name="monitor-your-topology-in-storm"></a>Monitorování topologie v Storm  
Je spuštěn topologii, můžete ji sledovat v uživatelském rozhraní Storm. Zde jsou hlavní parametry, které chcete podívejte se na:

* **Celkový proces spuštění latence.** Toto je průměrná doba, jakou jeden záznam trvat vysílaných spout, zpracování funkcí bolt a potvrzené.

* **Celkový počet bolt proces latence.** Toto je průměrný čas spotřebovaného řazené kolekce členů v bolt, dokud neobdrží potvrzení.

* **Celkový počet bolt spustit latence.** Toto je průměrný čas strávený funkcí bolt v metoda execute.

* **Počet chyb.** Označuje počet řazených kolekcí členů, které se nepodařilo plně zpracovány před vypršením časového limitu.

* **Kapacity.** Toto je míra je zatížení systému. Pokud tento počet je 1, fungují tak rychle, jak mohou vaše funkce bolts. Pokud je menší než 1, zvýšit paralelismu. Pokud je větší než 1, snížit paralelismu.

## <a name="troubleshoot-common-problems"></a>Řešení běžných potíží
Tady jsou několik běžných scénářů řešení potíží.
* **Mnoho řazených kolekcí členů jsou vypršení časového limitu.** Podívejte se na každém uzlu v topologii k určení, kde je kritická místa. Nejběžnějším důvodem je, že funkce bolts nejsou schopné udržovat tempo s funkcích spouts. To vede k zablokování vnitřní vyrovnávací paměti při čekání na zpracování řazené kolekce členů. Zvažte možnost zvýšení hodnoty časového limitu nebo snížení maximální spout čekající na vyřízení.

* **Je latence vysoké celkový proces spouštění, ale proces latence nízkou bolt.** V takovém případě je možné, že řazené kolekce členů nejsou potvrzování dostatečně fast. Zkontrolujte, zda jsou k dispozici dostatečný počet acknowledgers. Další možností je, že při předtím, než funkce bolts spustit, je zpracování čekají ve frontě příliš dlouho. Snížit maximálního počtu spout čekající na vyřízení.

* **Je vysoká bolt provést latence.** To znamená, že metodě execute() vaše bolt trvá příliš dlouho. Optimalizace kódu, nebo podívejte se na velikosti zápisu a vyprázdnit chování.

### <a name="data-lake-store-throttling"></a>Omezení data Lake Store
Jestli jste nedosáhli omezení šířky pásma poskytuje Data Lake Store, může se zobrazit selhání úkolů. Omezení chyby v protokolech úloh. Paralelismus může snížit zvýšením velikosti kontejneru.    

Pokud chcete zkontrolovat, pokud jste jsou získávání omezené, povolte ladění na straně klienta protokolování:

1. V **Ambari** > **Storm** > **konfigurace** > **Advanced storm-worker-log4j**, změnit **&lt;kořenový úroveň = "informace"&gt;** k  **&lt;kořenový úroveň = "ladění"&gt;**. Restartujte všechny uzly nebo služby pro danou konfiguraci vstoupily v platnost.
2. Monitorování topologie Storm přihlásí uzlů pracovního procesu (v části /var/log/storm/worker-artifacts /&lt;TopologyName&gt;/&lt;port&gt;/worker.log) pro Data Lake Store omezení výjimky.

## <a name="next-steps"></a>Další postup
Optimalizace další výkonu pro Storm může být odkazováno v [tomto blogu](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/).

Další příklad ke spuštění, naleznete v části [následujícímu na Githubu](https://github.com/hdinsight/storm-performance-automation).
