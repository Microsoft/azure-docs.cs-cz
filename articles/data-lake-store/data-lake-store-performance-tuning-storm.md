---
title: Optimalizace výkonu – vyplavení pomocí Azure Data Lake Storage Gen1
description: Seznamte se s faktory, které byste měli vzít v úvahu při ladění výkonu topologie Azure, včetně řešení běžných problémů.
author: stewu
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 68f30079d85e2064b92718c65b38dbb5069d810b
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92101643"
---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Pokyny k ladění výkonu pro zaplavení v HDInsight a Azure Data Lake Storage Gen1

Seznamte se s faktory, které byste měli vzít v úvahu při ladění výkonu topologie Azure. Je například důležité pochopit charakteristiky práce prováděné spoutů a šrouby (zda je práce v/v nebo v paměti). Tento článek obsahuje řadu pokynů pro ladění výkonu, včetně řešení běžných potíží.

## <a name="prerequisites"></a>Předpoklady

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen1**. Pokyny, jak ho vytvořit, najdete v tématu Začínáme [s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Cluster Azure HDInsight** s přístupem k účtu Data Lake Storage Gen1. Další informace najdete v tématu [Vytvoření clusteru HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Ujistěte se, že jste pro cluster povolili vzdálenou plochu.
* **Spuštění clusteru nečinnosti v Data Lake Storage Gen1**. Další informace najdete v tématu zaplavení [v HDInsight](../hdinsight/storm/apache-storm-overview.md).
* **Pokyny k ladění výkonu na data Lake Storage Gen1**.  Obecné koncepty výkonu najdete v tématu [Data Lake Storage Gen1 doprovodné materiály k ladění výkonu](./data-lake-store-performance-tuning-guidance.md).  

## <a name="tune-the-parallelism-of-the-topology"></a>Vyladění paralelismu topologie

Můžete zvýšit výkon tím, že zvýšíte souběžnost vstupně-výstupních operací na a z Data Lake Storage Gen1. Topologie neserializátoru má sadu konfigurací, které určují paralelismus:
* Počet pracovních procesů (pracovní procesy jsou rovnoměrně distribuovány napříč virtuálními počítači).
* Počet instancí prováděcího modulu Spout
* Počet instancí vykonavatele šroubů
* Počet úloh Spout
* Počet úloh šroubů.

Například v clusteru se čtyřmi virtuálními počítači a 4 pracovními procesy, 32 Spout vykonavatelé a 32 úlohy Spout 256 a prováděcí moduly a úlohy 512, zvažte následující:

Každý správce, který je pracovním uzlem, má jeden proces JVM (work Java Virtual Machine). Tento proces JVM spravuje 4 vlákna Spout a vlákna 64. V každém vlákně se úlohy spouštějí postupně. V předchozí konfiguraci má každé vlákno Spout jeden úkol a každé vlákno šroubu má dvě úlohy.

V rámci zaplavování jsou zde různé komponenty a jejich vliv na úroveň paralelismu, které máte:
* Hlavní uzel (s názvem Nimbus v zaplavě) slouží k odesílání a správě úloh. Tyto uzly nemají žádný vliv na stupeň paralelismu.
* Uzly správce. Ve službě HDInsight to odpovídá pracovním uzlům Azure VM.
* Pracovní úlohy jsou procesy pro práci v rámci virtuálních počítačů. Každý pracovní úkol odpovídá instanci JVM. Hodnota vyplavě distribuuje počet pracovních procesů, které zadáte do pracovních uzlů, co nejblíže.
* Instance prováděče Spout a šroubů. Každá instance vykonavatele odpovídá vláknu běžícímu v rámci pracovních procesů (JVMs).
* Úlohy s více operacemi. Jedná se o logické úlohy, které každý z těchto vláken spouští. Tato akce nemění úroveň paralelismu, takže byste měli vyhodnotit, jestli potřebujete více úloh na vykonavatele.

### <a name="get-the-best-performance-from-data-lake-storage-gen1"></a>Dosažení nejlepšího výkonu z Data Lake Storage Gen1

Při práci s Data Lake Storage Gen1 dosáhnete nejlepšího výkonu, pokud provedete následující:
* Sloučení malých připojení na větší velikosti (v ideálním případě 4 MB).
* Udělejte tolik souběžných požadavků, kolik můžete. Vzhledem k tomu, že každé vlákno šroubování blokuje čtení, chcete mít někde v rozsahu 8-12 vláken na jádro. Tím se zachová síťová karta a výkon procesoru. Větší virtuální počítač umožňuje víc souběžných požadavků.  

### <a name="example-topology"></a>Ukázková topologie

Předpokládejme, že máte k dispozici osm clusterů pracovních uzlů s virtuálním počítačem s D13v2 Azure. Tento virtuální počítač má osm jader, takže mezi osmi pracovními uzly máte 64 celkem jader.

Řekněme, že pro každé jádro provedeme osm vláken šroubů. S 64 jádry to znamená, že chceme instance vykonavatele 512 Total (tzn. Threads). V takovém případě řekněme, že začneme s jedním JVM na jeden virtuální počítač a hlavně pomocí souběžnosti vláken v rámci JVM dosáhnout souběžnosti. To znamená, že potřebujeme osm úloh pracovních procesů (jeden pro každý virtuální počítač Azure) a prováděcí moduly 512. Vzhledem k této konfiguraci se nevyužívá k tomu, že se proces rozšíří pracovní procesy rovnoměrně za pracovní uzly (označované také jako uzly vedoucího procesu), čímž se každý pracovní uzel jednou JVM. V rámci vedoucích se v rámci nadřízených procesů pokusí distribuovat vykonavatele rovnoměrně mezi vedoucími, přičemž každý nadřízený (to znamená JVM) osm vláken každé z nich.

## <a name="tune-additional-parameters"></a>Ladění dalších parametrů
Po použití základní topologie můžete zvážit, zda chcete upravit některý z parametrů:
* **Počet JVMs na pracovní uzel** Pokud máte rozsáhlou datovou strukturu (například vyhledávací tabulku), kterou hostuje paměť, každý JVM vyžaduje samostatnou kopii. Případně můžete datovou strukturu použít napříč mnoha vlákny, pokud máte méně JVMs. V případě vstupně-výstupních operací šroubu se počet JVMs neprovádí jako rozdíl mezi počtem vláken přidaných mezi tyto JVMs. Pro zjednodušení je dobré mít jeden JVM na pracovní proces. V závislosti na tom, co vaše šroub dělá nebo jaké zpracování aplikace potřebujete, ale možná budete muset změnit toto číslo.
* **Počet prováděcích modulů Spout** Vzhledem k tomu, že předchozí příklad používá šrouby pro zápis do Data Lake Storage Gen1, počet spoutů není přímo relevantní pro výkon šroubu. V závislosti na množství zpracování nebo vstupně-výstupních operacích v Spout je ale vhodné vyladit spoutů pro nejlepší výkon. Ujistěte se, že máte dostatek spoutů, aby bylo možné podržet šrouby obsazené. Výstupní sazby spoutů by měly odpovídat propustnosti šrouby. Skutečná konfigurace závisí na Spout.
* **Počet úkolů.** Každý šroub se spouští jako jedno vlákno. Další úkoly na šroub neposkytují žádnou další souběžnost. V takovém případě je v případě, že váš proces potvrzování řazené kolekce členů používá velký podíl času spuštění vaší služby, jediný čas, kdy je výhoda výhodná. Před odesláním potvrzení ze svého šroubu je dobré seskupit mnoho řazených kolekcí členů do většího připojení. Ve většině případů ale více úkolů neposkytuje žádné další výhody.
* **Místní nebo náhodné seskupení.** Pokud je toto nastavení povoleno, budou řazeny do šrouby v rámci stejného pracovního procesu. Tím se sníží komunikace mezi procesy a síťové hovory. Tento postup je doporučený pro většinu topologií.

Tento základní scénář je dobrým výchozím bodem. Otestujte vlastními daty a selepšit předchozí parametry, abyste dosáhli optimálního výkonu.

## <a name="tune-the-spout"></a>Nalaďte Spout

K ladění Spout můžete upravit následující nastavení.

- **Časový limit řazené kolekce členů: topologie. Message. Timeout. sekund** Toto nastavení určuje dobu, po kterou se zpráva dokončí, a před tím, než se považuje za neúspěšnou, obdržení potvrzení.

- **Maximální velikost paměti na pracovní proces: Work. childopts**. Toto nastavení umožňuje určit další parametry příkazového řádku pro pracovníky Java. Nejčastěji používané nastavení je XmX, které určuje maximální velikost paměti přidělené haldě JVM.

- **Maximální počet nevyřízených Spout: topologal. max. Spout. Pending**. Toto nastavení určuje počet řazených kolekcí členů, které mohou být v každém okamžiku na jednom vlákně Spout (dosud nepotvrzeny na všech uzlech v topologii).

  Dobrým výpočtem je odhad velikosti každé řazené kolekce členů. Pak Zjistěte, kolik paměti jedno vlákno spouto. Celková paměť přidělená vláknu, dělená touto hodnotou, by měla poskytnout horní mez pro parametr Max Spout Pending.

## <a name="tune-the-bolt"></a>Optimalizace šroubu
Při psaní do Data Lake Storage Gen1 nastavte zásady synchronizace velikosti (vyrovnávací paměť na straně klienta) na 4 MB. Vyprázdnit nebo HSync () se pak provede jenom v případě, že je velikost vyrovnávací paměti na této hodnotě. Ovladač Data Lake Storage Gen1 na virtuálním počítači pracovního procesu automaticky provádí ukládání do vyrovnávací paměti, pokud explicitně neprovedete HSync ().

Výchozí hodnota Data Lake Storage Gen1 pro zaplavování má parametr zásad synchronizace velikosti (fileBufferSize), který se dá použít k vyladění tohoto parametru.

V případě topologií náročných na vstupně-výstupní operace je vhodné, aby každý podproces šroubů napsal do vlastního souboru a nastavil zásady pro rotaci souborů (fileRotationSize). Když soubor dosáhne určité velikosti, datový proud se automaticky vyprázdní a zapíše se do něj nový soubor. Doporučená velikost souboru pro rotaci je 1 GB.

### <a name="handle-tuple-data"></a>Zpracování dat řazené kolekce členů

V systému se Spout nachází v řazené kolekci členů, dokud není explicitně potvrzeno šroubem. Pokud je v rámci pole řazená kolekce členů, ale ještě nebyla potvrzena, Spout nemusí být trvale Data Lake Storage Gen1 back-endu. Po potvrzení řazené kolekce členů může Spout zaručit trvalost a pak může odstranit zdrojová data z jakéhokoli zdroje, ze kterého čte.  

Pro nejlepší výkon na Data Lake Storage Gen1 mít vyrovnávací paměť šroubů 4 MB dat řazené kolekce členů. Pak zapište do Data Lake Storage Gen1 back-endu jako zápis 1 4 MB. Po úspěšném zapsání dat do úložiště (voláním hflush ()) může šroub potvrdit data zpět do Spout. To je to, co tady je uvedený příklad. Je také přijatelné, aby obsahoval větší počet řazených kolekcí členů před provedením volání hflush () a potvrzenými řazenými kolekcemi členů. To ale zvyšuje počet řazených kolekcí členů v letu, že Spout potřebuje držet, a zvyšuje tak množství paměti vyžadované na JVM.

> [!NOTE]
> Aplikace mohou mít požadavek na potvrzení řazených kolekcí členů častěji (u velikostí dat menších než 4 MB) pro jiné účely bez výkonu. To však může ovlivnit propustnost vstupně-výstupních operací do back-endu úložiště. Pečlivě navážíte tyto kompromisy proti vstupně-výstupnímu výkonu šroubů.

Pokud příchozí míra řazených kolekcí členů není vysoká, takže vyrovnávací paměť o velikosti 4 MB trvá delší dobu, zvažte tuto skutečnost:
* Snížení počtu šroubů, aby bylo možné vyplnit méně vyrovnávacích pamětí.
* Se zásadami založenými na čase nebo na základě počtu, kde se hflush () aktivuje při každém vyprázdnění x nebo každých y milisekund, a řazené kolekce členů se potvrzují zpátky.

Propustnost v tomto případě je nižší, ale s pomalou mírou událostí, maximální propustnost není největším cílem. Tato omezení pomáhají snížit celkovou dobu, kterou bude trvat, než se řazená kolekce členů dotéká do obchodu. Tato situace může nastat, pokud chcete, aby kanál v reálném čase byl i s nízkou rychlostí událostí. Všimněte si také, že pokud je vaše příchozí míra řazené kolekce členů nízká, měli byste upravit parametr topology.message.timeout_secs, aby nedošlo k vypršení časového limitu řazených kolekcí členů do vyrovnávací paměti nebo zpracování.

## <a name="monitor-your-topology-in-storm"></a>Monitorování topologie v prostředí s více podsady  
I když je vaše topologie spuštěná, můžete ji monitorovat v uživatelském rozhraní. Tady jsou hlavní parametry, které se mají najít:

* **Celková latence spuštění procesu.** Toto je průměrná doba, kterou bude jedna řazená kolekce členů vygenerována Spout, zpracována šroubem a potvrzena.

* **Celková latence procesu šroubů** Jedná se o průměrný čas strávený řazenou kolekcí členů na šroubu, dokud neobdrží potvrzení.

* **Celková latence spuštění šroubu** Toto je průměrný čas strávený šroubem v metodě Execute.

* **Počet selhání** To odkazuje na počet řazených kolekcí členů, jejichž úplné zpracování se nezdařilo před vypršením časového limitu.

* **Klíčivost.** Toto je měřítko způsobu, jakým je systém zaneprázdněný. Pokud je toto číslo 1, vaše šrouby funguje tak rychle, jak může. Pokud je menší než 1, zvyšte paralelismus. Pokud je větší než 1, snižte paralelismus.

## <a name="troubleshoot-common-problems"></a>Řešení běžných problémů
Tady je několik běžných scénářů řešení potíží.
* Pro **mnoho řazených kolekcí členů vypršel časový limit.** Podívejte se na každý uzel v topologii, abyste zjistili, kde je kritický bod. Nejběžnějším důvodem je to, že šrouby není možné udržet s spoutů. To vede k řazené kolekci členů k ukládání vnitřních vyrovnávacích pamětí při čekání na zpracování. Zvažte zvýšení hodnoty časového limitu nebo snížení maximálního počtu vyspoutch čeká na vyřízení.

* **Existuje vysoká latence provádění procesu, ale nízká latence procesu.** V tomto případě je možné, že se řazené kolekce členů dostatečně rychle nepotvrzují. Ověřte, zda je k dispozici dostatečný počet potvrzování. Další možností je, že čekají ve frontě na příliš dlouhou dobu, než je tento šrouby začne zpracovávat. Snižte maximální počet Spout čeká na vyřízení.

* **Vysoká latence spouštění.** To znamená, že metoda Execute () vašeho šroubu trvá příliš dlouho. Optimalizujte kód, nebo se podívejte na velikost zápisu a chování při vyprazdňování.

### <a name="data-lake-storage-gen1-throttling"></a>Omezení Data Lake Storage Gen1
Pokud jste dosáhli limitu šířky pásma poskytovaného Data Lake Storage Gen1, může se zobrazit chyba úlohy. V protokolech úloh se podívejte na chyby omezování. Paralelismus můžete snížit zvýšením velikosti kontejneru.    

Pokud chcete zjistit, jestli se vám omezilo omezení, povolte protokolování ladění na straně klienta:

1. V **Ambari**.  >  **Storm**  >  **Config**  >  **log4j konfigurace Advanced**, změňte ** &lt; kořenovou úroveň = "informace" &gt; ** na ** &lt; kořenovou úroveň = "ladění" &gt; **. Restartujte všechny uzly/služby, aby se konfigurace projevila.
2. Sledujte protokoly rozplavení v uzlech pracovních procesů (pod/var/log/Storm/Worker-artifacts/em &lt; &gt; / &lt; &gt; /Worker.log portu) pro výjimky omezování Data Lake Storage Gen1.

## <a name="next-steps"></a>Další kroky
Na [tomto blogu](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)se dá odkazovat na další ladění výkonu pro zaplavení.

Další příklad ke spuštění najdete [v tomto tématu na GitHubu](https://github.com/hdinsight/storm-performance-automation).