---
title: 'Ladění výkonu: Storm, HDInsight & Azure Data Lake Storage Gen2 | Dokumenty společnosti Microsoft'
description: Azure Data Lake Storage Gen2 Storm pokyny pro ladění výkonu
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 125c583512f6bae34c2dd3c3dd76a1b96a181ac1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327909"
---
# <a name="tune-performance-storm-hdinsight--azure-data-lake-storage-gen2"></a>Výkon ladění: Storm, HDInsight & Azure Data Lake Storage Gen2

Seznamte se s faktory, které je třeba vzít v úvahu při ladění výkonu topologie Azure Storm. Například je důležité pochopit charakteristiky práce provedené výtokovými a šrouby (zda je práce náročná na vstupně-up nebo náročná na paměť). Tento článek popisuje řadu pokynů pro ladění výkonu, včetně řešení běžných problémů.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen2**. Pokyny k jeho vytvoření najdete v [tématu Úvodní příručka: Vytvoření účtu úložiště pro účely analýzy](data-lake-storage-quickstart-create-account.md).
* **Cluster Azure HDInsight** s přístupem k účtu Data Lake Storage Gen2. Viz [Použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2). Ujistěte se, že jste pro cluster povolili vzdálenou plochu.
* **Spuštění clusteru Storm na datovém úložišti jezera Gen2**. Další informace naleznete v [tématu Storm on HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-overview).
* **Pokyny pro ladění výkonu pro úložiště datových jezer Gen2**.  Obecné koncepty výkonu najdete v [tématu Data Lake Storage Gen2 Performance Tuning Guidance](data-lake-storage-performance-tuning-guidance.md).   

## <a name="tune-the-parallelism-of-the-topology"></a>Vylaďte paralelismus topologie

Můžete být schopni zlepšit výkon zvýšením souběžnosti vstupně-v do a z úložiště datového jezera Gen2. Topologie bouře má sadu konfigurací, které určují paralelismus:
* Počet pracovních procesů (pracovníci jsou rovnoměrně rozloženy mezi virtuálními zařízeními).
* Počet instancí vykonavatele výtoku.
* Počet instancí vykonavatele šroubů.
* Počet úloh výtoku.
* Počet šroubových úloh.

Například v clusteru se 4 virtuálními počítači a 4 pracovními procesy, 32 vykonavatelé výtoku výtoku a 32 úlohy výtoku výtoku a 256 šroubvy výkonní moduly a 512 šroub úkoly, zvažte následující:

Každý nadřízený, což je pracovní uzel, má proces virtuálního počítače Java (JVM) jednoho pracovního procesu. Tento proces JVM spravuje 4 závity výtoku a 64 závitů šroubů. V rámci každého vlákna jsou úlohy spouštěny postupně. S předchozí konfigurací má každý podproces výtoku 1 úkol a každý závit šroubu má 2 úkoly.

V Storm, zde jsou různé součásti zapojené, a jak ovlivňují úroveň paralelismu máte:
* Hlavní uzel (nazvaný Nimbus in Storm) se používá k odesílání a správě úloh. Tyto uzly nemají žádný vliv na stupeň paralelismu.
* Uzly nadřízeného. V HDInsight to odpovídá pracovní uzel virtuálního počítače Azure.
* Pracovní úkoly jsou storm procesy spuštěné ve virtuálních discích. Každý pracovní úkol odpovídá instanci JVM. Storm distribuuje počet pracovních procesů, které zadáte do pracovních uzlů co nejrovnoměrněji.
* Instance vykonavatelů výtoku a šroubů. Každá instance vykonavatele odpovídá podprocesu spuštěného v rámci pracovníků (JVMs).
* Storm úkoly. Jedná se o logické úlohy, které každé z těchto vláken spustit. To nemění úroveň paralelismu, takže byste měli vyhodnotit, zda potřebujete více úkolů na vykonavatele nebo ne.

### <a name="get-the-best-performance-from-data-lake-storage-gen2"></a>Získejte nejlepší výkon z data lake storage gen2

Při práci s Data Lake Storage Gen2 získáte nejlepší výkon, pokud uděláte následující:
* Svezete své malé připojení do větších velikostí.
* Proveďte tolik souběžných požadavků, kolik můžete. Vzhledem k tomu, že každý závit šroubu dělá blokování čtení, chcete mít někde v rozsahu 8-12 závitů na jádro. To udržuje NIC a CPU dobře využité. Větší virtuální ho disponopou, který umožňuje více souběžných požadavků.  

### <a name="example-topology"></a>Příklad topologie

Předpokládejme, že máte cluster 8 pracovních uzlů s virtuálním počítačem Azure D13v2. Tento virtuální virtuální měnový virtuální soud má 8 jader, takže mezi 8 pracovními uzly máte celkem 64 jader.

Řekněme, že uděláme 8 šroubových závitů na jádro. Vzhledem k tomu, 64 jader, to znamená, že chceme 512 celkem šroub vykonavatel instance (to znamená, že závity). V tomto případě řekněme, že začneme s jedním JVM na virtuální mase a hlavně použít souběžnost podprocesu v rámci JVM k dosažení souběžnosti. To znamená, že potřebujeme 8 pracovních úkolů (jeden pro každý virtuální počítač Azure) a 512 vykonavatelů šroubů. Vzhledem k této konfiguraci storm pokusí distribuovat pracovníky rovnoměrně mezi uzly pracovníka (označované také jako uzly nadřízené), což každému pracovnímu uzlu 1 JVM. Nyní v rámci supervizorů, Storm se snaží rozdělit vykonavatele rovnoměrně mezi orgány dohledu, což každý nadřízený (to znamená, JVM) 8 podprocesů každý.

## <a name="tune-additional-parameters"></a>Vyladění dalších parametrů
Poté, co máte základní topologii, můžete zvážit, zda chcete vyladit některý z parametrů:
* **Počet JVM na pracovní uzel.** Pokud máte velkou datovou strukturu (například vyhledávací tabulku), kterou hostujete v paměti, každý JVM vyžaduje samostatnou kopii. Alternativně můžete použít strukturu dat v mnoha vláknech, pokud máte méně JVMs. Pro šroub vstupně-o, počet JVMne neznamená, že velký rozdíl jako počet závitů přidaných přes tyto JVMs. Pro jednoduchost je vhodné mít jeden JVM na pracovníka. V závislosti na tom, co váš šroub dělá, nebo jaké zpracování aplikace, které požadujete, i když, možná budete muset změnit toto číslo.
* **Počet vykonavatelů výtoku.** Vzhledem k tomu, že předchozí příklad používá šrouby pro zápis do data lake storage Gen2, počet výtoků není přímo relevantní pro výkon šroubu. V závislosti na množství zpracování nebo vstupně-in děje v hubici, je však vhodné naladit hubice pro nejlepší výkon. Ujistěte se, že máte dostatek hubic, aby bylo možné udržet šrouby obsazeno. Výstupní sazby výtoku by měly odpovídat propustnosti šroubů. Skutečná konfigurace závisí na výtoku.
* **Počet úkolů.** Každý šroub běží jako jeden závit. Další úkoly na šroub neposkytují žádné další souběžnosti. Jediný čas, kdy jsou přínosem je, pokud váš proces potvrzení n-tice trvá velkou část času spuštění šroubu. Je vhodné seskupit mnoho n-tic do většího dodatku před odesláním potvrzení ze šroubu. Takže ve většině případů více úkolů neposkytuje žádné další výhody.
* **Místní nebo náhodné seskupení.** Pokud je toto nastavení povoleno, jsou řazené kolekce členů odesílány do šroubů v rámci stejného pracovního procesu. To snižuje meziprocesovou komunikaci a síťová volání. To se doporučuje pro většinu topologie.

Tento základní scénář je dobrým výchozím bodem. Otestujte s vlastními daty, abyste vylepšili předchozí parametry, abyste dosáhli optimálního výkonu.

## <a name="tune-the-spout"></a>Nalaďte hubici

Můžete upravit následující nastavení pro vyladění výtoku.

- **Časový čas řazené kolekce členů: topology.message.timeout.secs**. Toto nastavení určuje dobu, kterou zpráva potřebuje k dokončení a přijetí potvrzení, než je považována za neúspěšnou.

- **Maximální počet paměti na pracovní proces: worker.childopts**. Toto nastavení umožňuje zadat další parametry příkazového řádku pro pracovníky jazyka Java. Nejčastěji používané nastavení je zde XmX, který určuje maximální paměť přidělenou haldě JVM.

- **Maximální výtok čeká na vyřízení: topology.max.spout.pending**. Toto nastavení určuje počet řazených kolekcí členů, které mohou být v letu (dosud potvrzena ve všech uzlech v topologii) na vlákno výtoku kdykoli.

  Dobrý výpočet udělat, je odhadnout velikost každého z vašich n-tic. Pak zjistit, kolik paměti jeden výtok vlákno má. Celková paměť přidělená vláknu, dělená touto hodnotou, by měla poskytnout horní mez parametru max hubice čekající na vyřízení.

Výchozí datový chod úložiště dat Gen2 Storm šroub má parametr zásady synchronizace velikosti (fileBufferSize), který lze použít k vyladění tohoto parametru.

V topologie náročné na vstupně-in, je vhodné mít každý šroub vlákno zápis do vlastního souboru a nastavit zásady střídání souborů (fileRotationSize). Když soubor dosáhne určité velikosti, datový proud se automaticky vyprázdní a do ní se zapíše nový soubor. Doporučená velikost souboru pro otáčení je 1 GB.

## <a name="monitor-your-topology-in-storm"></a>Sledování topologie v Stormu  
Během spuštění topologie ji můžete sledovat v uživatelském rozhraní bouře. Zde jsou hlavní parametry, na které se můžete podívat:

* **Celková latence spuštění procesu.** Jedná se o průměrnou dobu, po kterou má být jedna n-tice emitována výtokem, zpracována šroubem a potvrzena.

* **Celková latence procesu šroubu.** Toto je průměrná doba strávená n-tice na šroubu, dokud neobdrží potvrzení.

* **Celková latence spuštění šroubu.** Toto je průměrná doba strávená šroubem v metodě spuštění.

* **Počet selhání.** To se týká počtu řazených kolekcí členů, které se nepodařilo plně zpracovat před jejich časovým výpadkem.

* **Kapacita.** Toto je míra toho, jak je váš systém zaneprázdněn. Pokud je toto číslo 1, vaše šrouby pracují tak rychle, jak mohou. Pokud je menší než 1, zvyšte paralelismus. Pokud je větší než 1, snižte paralelismus.

## <a name="troubleshoot-common-problems"></a>Poradce při potížích
Zde je několik běžných scénářů řešení potíží.
* **Mnoho n-tic jsou vypršení časového limitu.** Podívejte se na každý uzel v topologii k určení, kde je kritický bod. Nejčastějším důvodem je, že šrouby nejsou schopny držet krok s výtoky. To vede k tuchu ucpávání vnitřní vyrovnávací paměti při čekání na zpracování. Zvažte zvýšení hodnoty časového limitu nebo snížení maximální výtok čeká.

* **Existuje vysoká celková latence spuštění procesu, ale nízká latence procesu šroubu.** V tomto případě je možné, že řazené kolekce členů nejsou dostatečně rychle potvrzeny. Zkontrolujte, zda je k dispozici dostatečný počet poznačtek. Další možností je, že čekají ve frontě příliš dlouho, než je šrouby začnou zpracovávat. Snižte maximální výtok čekající na vyřízení.

* **Je vysoká latence spuštění šroubu.** To znamená, že metoda execute() šroubu trvá příliš dlouho. Optimalizujte kód nebo se podívejte na velikosti zápisu a vyprázdnění chování.

### <a name="data-lake-storage-gen2-throttling"></a>Omezení úložiště datového jezera Gen2
Pokud dosáhnete omezení šířky pásma poskytované Data Lake Storage Gen2, může se zobrazit selhání úloh. Zkontrolujte protokoly úloh, zda nezaznamenávají chyby omezení. Paralelismus můžete snížit zvětšením velikosti kontejneru.    

Chcete-li zkontrolovat, zda jste stále omezení, povolte protokolování ladění na straně klienta:

1. V **Ambari** > **Storm** > **Config** > **Advanced storm-worker-log4j**, změňte ** &lt;kořenovou úroveň="info"&gt; ** na ** &lt;kořenovou úroveň="ladění"&gt;**. Restartujte všechny uzly nebo službu, aby se konfigurace projevila.
2. Monitorujte protokoly topologie bouře na pracovních uzlech (pod&lt;/var/log/storm/worker-artifacts/ TopologyName&gt;/&lt;port&gt;/worker.log) pro výjimky omezení úložiště datového jezera Gen2.

## <a name="next-steps"></a>Další kroky
Další ladění výkonu pro Storm lze odkazovat v [tomto blogu](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/).

Další příklad ke spuštění najdete [v tomto tématu na GitHubu](https://github.com/hdinsight/storm-performance-automation).
