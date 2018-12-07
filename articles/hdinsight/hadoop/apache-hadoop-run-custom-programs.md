---
title: Spuštění vlastních programů MapReduce – Azure HDInsight
description: Kdy a jak se spuštění vlastních programů MapReduce v HDInsight.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: ashishth
ms.openlocfilehash: a4a94ac4deee90bf0aea5fafbddff0105680cb4b
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53013147"
---
# <a name="run-custom-mapreduce-programs"></a>Spuštění vlastních programů MapReduce

Apache systémy velké objemy dat založenému na Hadoopu, jako je HDInsight umožňují zpracování dat pomocí široké škály nástrojů a technologií. Následující tabulka popisuje hlavní výhody a důležité informace pro každý z nich.

| Mechanismus dotazu | Výhody | Požadavky |
| --- | --- | --- |
| **Apache Hive pomocí HiveQL** | <ul><li>Vynikající řešení pro dávkové zpracování a analýzu velkých objemů neměnnými daty, pro shrnutí dat a pro na dotazování na vyžádání. Využívá známou syntaxi podobném SQL.</li><li>Slouží k vytvoření trvalého tabulky dat, která lze snadno rozdělit na oddíly a indexovat.</li><li>Několik externích tabulek a zobrazení možné vytvářet přes stejná data.</li><li>Implementace jednoduchého datového skladu, která poskytuje rozsáhlé možnosti škálování a odolnost proti chybám pro ukládání a zpracování dat podporuje.</li></ul> | <ul><li>Vyžaduje mít alespoň nějaké identifikovatelné struktury zdrojová data.</li><li>Není vhodný pro dotazy v reálném čase a aktualizace na úrovni řádků. Nejlíp se používá pro dávkových úloh Hive prostřednictvím rozsáhlých datových sad.</li><li>Nemusí být schopna provádět některé typy zpracování složitých úloh.</li></ul> |
| **Apache Pig pomocí Pig Latin** | <ul><li>Vynikající řešení pro manipulace s daty jako nastaví, sloučení a filtrování datových sad, používání funkcí na záznamy nebo skupinami záznamů a definování sloupců, podle hodnoty seskupení nebo převedení sloupce na řádky restrukturalizaci data.</li><li>Přístup na základě pracovní postup může použít jako posloupnost operací s daty.</li></ul> | <ul><li>Uživatelé SQL možná zjistíte, že Pig Latin je méně známé a obtížnější než HiveQL.</li><li>Výchozí výstup je obvykle textový soubor a proto může být obtížnější pro použití s vizualizačních nástrojů, jako je například aplikace Excel. Obvykle bude vrstvy tabulky Hive výstupu.</li></ul> |
| **Vlastní mapa/zmenšit** | <ul><li>Poskytuje plnou kontrolu nad mapy a snížit fází a spuštění.</li><li>To umožňuje vytvářet dotazy optimalizovat pro dosažení maximálního výkonu z clusteru, nebo chcete-li minimalizovat zatížení serverů a sítí.</li><li>Součástí je možné psát v celou řadu známých jazycích.</li></ul> | <ul><li>Je obtížnější než při použití Pigu a Hivu vzhledem k tomu musíte vytvořit vlastní mapy a snížit komponenty.</li><li>Procesy, které vyžadují propojení sady dat jsou obtížnější provádět.</li><li>I když jsou k dispozici rozhraní pro testování, ladění kódu je složitější než normální aplikace protože kód se spustí jako úlohu služby batch pod kontrolou Plánovač úloh systému Hadoop.</li></ul> |
| **Apache HCatalog** | <ul><li>To abstrahuje podrobnosti cesty úložiště, usnadnění správy a odstraňuje potřebu uživatelům vědět, kde jsou uložená data.</li><li>Umožňuje oznámení o události, například dostupnost dat, povolení dalších nástrojů, jako je Oozie rozpoznat, kdy došlo k operace.</li><li>Poskytuje relační zobrazení dat, včetně dělení podle klíče a usnadňuje data access.</li></ul> | <ul><li>Podporuje RCFile CSV text, textu JSON, SequenceFile a ORC formáty souborů ve výchozím nastavení, ale možná budete muset napsat vlastní SerDe pro ostatní formáty.</li><li>Hcatalogu není bezpečná pro vlákno.</li><li>Při použití HCatalog zavaděče v skriptů Pig, platí určitá omezení pro datové typy sloupců. Další informace najdete v tématu [HCatLoader datové typy](https://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) v dokumentaci Apache HCatalog.</li></ul> |

Obvykle použijete nejjednodušší přístupů, které mohou poskytnout výsledky, které potřebujete. Například je možné dosáhnout pomocí Hive právě tyto výsledky, ale pro složitější scénáře budete muset použít Pig, nebo dokonce napsat vlastní mapy a snížit komponenty. Můžete také rozhodnout po experimentování s Hive a Pig, že vlastní mapa a snížit komponent může poskytovat lepší výkon, neboť umožňuje doladit a optimalizovat zpracování.

## <a name="custom-mapreduce-components"></a>Vlastní mapa/snížit komponenty

Snížit/mapy kódu se skládá z dvě samostatné funkce, které jsou implementovány jako **mapy** a **snížit** komponenty. **Mapy** komponenta je spustit souběžně na více uzlech clusteru, každý uzel použití mapování na uzlu vlastní podmnožinu dat. **Snížit** komponenty kompletuje a shrnuje výsledky z všechny funkce mapy. Další informace o těchto dvou komponentách najdete v tématu [použití MapReduce se v clusteru Hadoop v HDInsight](hdinsight-use-mapreduce.md).

Ve většině scénářů HDInsight zpracování je teď jednodušší a efektivnější používat vyšší úroveň abstrakce například Pig nebo Hive. Můžete také vytvořit vlastní mapy a snížit komponenty pro použití v rámci skriptů Hive k provádění složitějších zpracování.

Vlastní mapa/snížit komponenty jsou obvykle napsány v jazyce Java. Hadoop poskytuje datové proudy rozhraní, které také umožňuje součástem pro použití, které jsou vyvíjeny v jiných jazycích, jako například C#, F#, Visual Basic, Python a JavaScript.

* Návod na vývoj vlastních programů MapReduce v Javě, naleznete v tématu [programů vývoj Java MapReduce pro Hadoop v HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).
* Příklad pomocí Pythonu najdete v tématu [Python vývoj programů MapReduce se streamováním pro HDInsight](apache-hadoop-streaming-python.md).

Zvažte možnost vytvořit vlastní mapy a snížit součásti byly splněny následující podmínky:

* Je potřeba zpracovávat data, která je zcela nestrukturovaných analýzou dat a použití vlastní logiku za účelem získání strukturovaných informací z něj.
* Chcete provádět složité úlohy, které je obtížné (či nemožné) vyjádřit v Pig nebo Hive bez nutnosti uchýlit se k vytváření UDF. Například můžete potřebovat použít externí službu geokódování k převodu zeměpisné šířky a délky souřadnice nebo IP adresy ve zdrojových datech názvy zeměpisné umístění.
* Chcete znovu použít váš stávající kód .NET, Python nebo JavaScript součástí mapy nebo snížit pomocí Hadoop, streamování rozhraní.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Odeslat a spustit vlastní program MapReduce

Nejběžnější programů MapReduce jsou napsané v jazyce Java a kompilováno do souboru jar.

1. Po vyvinutý, zkompilován a testovat MapReduce program, použijte `scp` příkazu k odeslání souboru jar k hlavnímu uzlu.

    ```bash
    scp mycustomprogram.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Nahraďte **uživatelské jméno** pomocí uživatelského účtu SSH pro váš cluster. Nahraďte **CLUSTERNAME** s názvem clusteru. Pokud jste použili heslo k zabezpečení účtu SSH, zobrazí se výzva k zadání hesla. Pokud jste použili certifikát, budete možná muset použít `-i` parametr k určení souboru privátního klíče.

2. Připojení ke clusteru pomocí [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Z relace SSH spusťte program MapReduce přes YARN.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    Tento příkaz odešle úlohu MapReduce do YARN. Vstupní soubor je `/example/data/sample.log`, a výstupní adresář je `/example/data/logoutput`. Vstupní soubor a všechny výstupní soubory jsou uloženy do výchozího úložiště pro cluster.

## <a name="next-steps"></a>Další postup

* [Použití jazyka C# s MapReduce, streaming na platformě Hadoop v HDInsight](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Vývoj programů Java MapReduce pro Hadoop v HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Vývoj programů MapReduce se streamováním pro HDInsight v Pythonu](apache-hadoop-streaming-python.md)
* [Vytvoření aplikací Spark pro cluster služby HDInsight pomocí sady Azure Toolkit pro Eclipse](../spark/apache-spark-eclipse-tool-plugin.md)
* [Použití Pythonu uživatelem definované funkce (UDF) s Hivem a Pig v HDInsight](python-udf-hdinsight.md)
