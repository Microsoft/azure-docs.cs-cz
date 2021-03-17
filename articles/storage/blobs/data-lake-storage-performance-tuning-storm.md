---
title: 'Ladění výkonu: zaplavení, HDInsight & Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Seznamte se s pokyny pro optimalizaci výkonu topologie Azure v clusteru Azure HDInsight a Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 4db85357ee970d13d6b4fcce195cae66932bed18
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912786"
---
# <a name="tune-performance-storm-hdinsight--azure-data-lake-storage-gen2"></a>Ladění výkonu: zaplavení, HDInsight & Azure Data Lake Storage Gen2

Seznamte se s faktory, které byste měli vzít v úvahu při ladění výkonu topologie Azure. Je například důležité pochopit charakteristiky práce prováděné spoutů a šrouby (zda je práce v/v nebo v paměti). Tento článek obsahuje řadu pokynů pro ladění výkonu, včetně řešení běžných potíží.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen2**. Pokyny, jak ho vytvořit, najdete v tématu [rychlý Start: vytvoření účtu úložiště pro analýzu](../common/storage-account-create.md).
* **Cluster Azure HDInsight** s přístupem k účtu Data Lake Storage Gen2. Viz [použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md). Ujistěte se, že jste pro cluster povolili vzdálenou plochu.
* **Spuštění clusteru nečinnosti v Data Lake Storage Gen2**. Další informace najdete v tématu zaplavení [v HDInsight](../../hdinsight/storm/apache-storm-overview.md).
* **Pokyny k ladění výkonu na data Lake Storage Gen2**.  Obecné koncepty výkonu najdete v tématu [Data Lake Storage Gen2 doprovodné materiály k ladění výkonu](data-lake-storage-performance-tuning-guidance.md).   

## <a name="tune-the-parallelism-of-the-topology"></a>Vyladění paralelismu topologie

Můžete zvýšit výkon tím, že zvýšíte souběžnost vstupně-výstupních operací na a z Data Lake Storage Gen2. Topologie neserializátoru má sadu konfigurací, které určují paralelismus:
* Počet pracovních procesů (pracovní procesy jsou rovnoměrně distribuovány napříč virtuálními počítači).
* Počet instancí prováděcího modulu Spout
* Počet instancí vykonavatele šroubů
* Počet úloh Spout
* Počet úloh šroubů.

Například v clusteru se čtyřmi virtuálními počítači a 4 pracovními procesy, 32 Spout vykonavatelé a 32 úlohy Spout 256 a prováděcí moduly a úlohy 512, zvažte následující:

Každý správce, který je pracovním uzlem, má jeden proces JVM (work Java Virtual Machine). Tento proces JVM spravuje 4 vlákna Spout a vlákna 64. V každém vlákně se úlohy spouštějí postupně. V předchozí konfiguraci každé vlákno Spout má 1 úlohu a každé vlákno šroubu má 2 úlohy.

V rámci zaplavování jsou zde různé komponenty a jejich vliv na úroveň paralelismu, které máte:
* Hlavní uzel (s názvem Nimbus v zaplavě) slouží k odesílání a správě úloh. Tyto uzly nemají žádný vliv na stupeň paralelismu.
* Uzly správce. Ve službě HDInsight to odpovídá pracovním uzlům Azure VM.
* Pracovní úlohy jsou procesy pro práci v rámci virtuálních počítačů. Každý pracovní úkol odpovídá instanci JVM. Hodnota vyplavě distribuuje počet pracovních procesů, které zadáte do pracovních uzlů, co nejblíže.
* Instance prováděče Spout a šroubů. Každá instance vykonavatele odpovídá vláknu běžícímu v rámci pracovních procesů (JVMs).
* Úlohy s více operacemi. Jedná se o logické úlohy, které každý z těchto vláken spouští. Tato akce nemění úroveň paralelismu, takže byste měli vyhodnotit, jestli potřebujete více úloh na vykonavatele.

### <a name="get-the-best-performance-from-data-lake-storage-gen2"></a>Dosažení nejlepšího výkonu z Data Lake Storage Gen2

Při práci s Data Lake Storage Gen2 dosáhnete nejlepšího výkonu, pokud provedete následující:
* Sloučení malých připojení do větších velikostí.
* Udělejte tolik souběžných požadavků, kolik můžete. Vzhledem k tomu, že každé vlákno šroubování blokuje čtení, chcete mít někde v rozsahu 8-12 vláken na jádro. Tím se zachová síťová karta a výkon procesoru. Větší virtuální počítač umožňuje víc souběžných požadavků.  

### <a name="example-topology"></a>Ukázková topologie

Předpokládejme, že máte cluster s 8 uzly pracovního procesu s D13v2 virtuálním počítačem Azure. Tento virtuální počítač má 8 jader, takže mezi uzly 8 pracovních procesů máte 64 celkem jader.

Řekněme, že provedeme 8 vláken na jádro. S 64 jádry to znamená, že chceme instance vykonavatele 512 Total (tzn. Threads). V takovém případě řekněme, že začneme s jedním JVM na jeden virtuální počítač a hlavně pomocí souběžnosti vláken v rámci JVM dosáhnout souběžnosti. To znamená, že potřebujeme 8 pracovních úkolů (jeden pro každý virtuální počítač Azure) a prováděcí moduly 512. Vzhledem k této konfiguraci se nevyužívá k tomu, že se proces rozšíří pracovní procesy rovnoměrně za pracovní uzly (označované také jako uzly vedoucího procesu), přičemž každý pracovní uzel 1 JVM. V rámci vedoucích se v rámci nadřízených procesů pokusí distribuovat vykonavatele rovnoměrně mezi vedoucími, přičemž každý nadřízený (to znamená JVM) 8 vláken každé z nich.

## <a name="tune-additional-parameters"></a>Ladění dalších parametrů
Po použití základní topologie můžete zvážit, zda chcete upravit některý z parametrů:
* **Počet JVMs na pracovní uzel** Pokud máte rozsáhlou datovou strukturu (například vyhledávací tabulku), kterou hostuje paměť, každý JVM vyžaduje samostatnou kopii. Případně můžete datovou strukturu použít napříč mnoha vlákny, pokud máte méně JVMs. V případě vstupně-výstupních operací šroubu se počet JVMs neprovádí jako rozdíl mezi počtem vláken přidaných mezi tyto JVMs. Pro zjednodušení je dobré mít jeden JVM na pracovní proces. V závislosti na tom, co vaše šroub dělá nebo jaké zpracování aplikace potřebujete, ale možná budete muset změnit toto číslo.
* **Počet prováděcích modulů Spout** Vzhledem k tomu, že předchozí příklad používá šrouby pro zápis do Data Lake Storage Gen2, počet spoutů není přímo relevantní pro výkon šroubu. V závislosti na množství zpracování nebo vstupně-výstupních operacích v Spout je ale vhodné vyladit spoutů pro nejlepší výkon. Ujistěte se, že máte dostatek spoutů, aby bylo možné podržet šrouby obsazené. Výstupní sazby spoutů by měly odpovídat propustnosti šrouby. Skutečná konfigurace závisí na Spout.
* **Počet úkolů.** Každý šroub se spouští jako jedno vlákno. Další úkoly na šroub neposkytují žádnou další souběžnost. V takovém případě je v případě, že váš proces potvrzování řazené kolekce členů používá velký podíl času spuštění vaší služby, jediný čas, kdy je výhoda výhodná. Před odesláním potvrzení ze svého šroubu je dobré seskupit mnoho řazených kolekcí členů do většího připojení. Ve většině případů ale více úkolů neposkytuje žádné další výhody.
* **Místní nebo náhodné seskupení.** Pokud je toto nastavení povoleno, budou řazeny do šrouby v rámci stejného pracovního procesu. Tím se sníží komunikace mezi procesy a síťové hovory. Tento postup je doporučený pro většinu topologií.

Tento základní scénář je dobrým výchozím bodem. Otestujte vlastními daty a selepšit předchozí parametry, abyste dosáhli optimálního výkonu.

## <a name="tune-the-spout"></a>Nalaďte Spout

K ladění Spout můžete upravit následující nastavení.

- **Časový limit řazené kolekce členů: topologie. Message. Timeout. sekund** Toto nastavení určuje dobu, po kterou se zpráva dokončí, a před tím, než se považuje za neúspěšnou, obdržení potvrzení.

- **Maximální velikost paměti na pracovní proces: Work. childopts**. Toto nastavení umožňuje určit další parametry příkazového řádku pro pracovníky Java. Nejčastěji používané nastavení je XmX, které určuje maximální velikost paměti přidělené haldě JVM.

- **Maximální počet nevyřízených Spout: topologal. max. Spout. Pending**. Toto nastavení určuje počet řazených kolekcí členů, které mohou být v každém okamžiku na jednom vlákně Spout (dosud nepotvrzeny na všech uzlech v topologii).

  Dobrým výpočtem je odhad velikosti každé řazené kolekce členů. Pak Zjistěte, kolik paměti jedno vlákno spouto. Celková paměť přidělená vláknu, dělená touto hodnotou, by měla poskytnout horní mez pro parametr Max Spout Pending.

Výchozí hodnota Data Lake Storage Gen2 pro zaplavování má parametr zásad synchronizace velikosti (fileBufferSize), který se dá použít k vyladění tohoto parametru.

V případě topologií náročných na vstupně-výstupní operace je vhodné, aby každý podproces šroubů napsal do vlastního souboru a nastavil zásady pro rotaci souborů (fileRotationSize). Když soubor dosáhne určité velikosti, datový proud se automaticky vyprázdní a zapíše se do něj nový soubor. Doporučená velikost souboru pro rotaci je 1 GB.

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

### <a name="data-lake-storage-gen2-throttling"></a>Omezení Data Lake Storage Gen2
Pokud jste dosáhli limitu šířky pásma poskytovaného Data Lake Storage Gen2, může se zobrazit chyba úlohy. V protokolech úloh se podívejte na chyby omezování. Paralelismus můžete snížit zvýšením velikosti kontejneru.    

Pokud chcete zjistit, jestli se vám omezilo omezení, povolte protokolování ladění na straně klienta:

1. V **Ambari**.  >  **Storm**  >  **Config**  >  **log4j konfigurace Advanced**, změňte **&lt; kořenovou úroveň = "informace" &gt;** na **&lt; kořenovou úroveň = "ladění" &gt;**. Restartujte všechny uzly/služby, aby se konfigurace projevila.
2. Sledujte protokoly rozplavení v uzlech pracovních procesů (pod/var/log/Storm/Worker-artifacts/em &lt; &gt; / &lt; &gt; /Worker.log portu) pro výjimky omezování Data Lake Storage Gen2.

## <a name="next-steps"></a>Další kroky
Na [tomto blogu](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)se dá odkazovat na další ladění výkonu pro zaplavení.

Další příklad ke spuštění najdete [v tomto tématu na GitHubu](https://github.com/hdinsight/storm-performance-automation).