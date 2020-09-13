---
title: Spouštění vlastních programů MapReduce – Azure HDInsight
description: Kdy a jak spouštět vlastní programy Apache MapReduce v clusterech Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 1dcc2a944fc527e4cbc8c7c1072503377ecb5798
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2020
ms.locfileid: "89505263"
---
# <a name="run-custom-mapreduce-programs"></a>Spuštění vlastních programů MapReduce

Apache Hadoop systémy velkých objemů dat, jako je HDInsight, umožňují zpracování dat pomocí široké škály nástrojů a technologií. Následující tabulka popisuje hlavní výhody a požadavky pro každé z nich.

| Mechanismus dotazů | Výhody | Požadavky |
| --- | --- | --- |
| **Apache Hive pomocí HiveQL** | <ul><li>Skvělé řešení pro dávkové zpracování a analýzu velkých objemů neproměnlivých dat, pro shrnutí dat a pro dotazování na vyžádání. Používá známou syntaxi jako SQL.</li><li>Dá se použít k vytvoření trvalých tabulek dat, které je možné snadno rozdělit a indexovat.</li><li>Pro stejná data lze vytvořit více externích tabulek a zobrazení.</li><li>Podporuje jednoduchou implementaci datového skladu, která poskytuje rozsáhlé možnosti škálování na více instancí a odolnost proti chybám pro ukládání a zpracování dat.</li></ul> | <ul><li>Vyžaduje, aby zdrojová data měla aspoň určitou identifikovatelnou strukturu.</li><li>Není vhodný pro dotazy a aktualizace na úrovni řádků v reálném čase. Je nejvhodnější pro dávkové úlohy pro velké sady dat.</li><li>Nemusí být schopni provést některé typy složitých úloh zpracování.</li></ul> |
| **Apache vepřové prase s latinkou** | <ul><li>Vynikající řešení pro manipulaci s daty jako sady, sloučení a filtrování datových sad, použití funkcí u záznamů nebo skupin záznamů a pro restrukturalizaci dat definováním sloupců, seskupením hodnot nebo převodem sloupců na řádky.</li><li>Může použít přístup na základě pracovního postupu jako posloupnost operací s daty.</li></ul> | <ul><li>Uživatelé SQL můžou najít pro vepřové prostředí latinku méně obeznámené a obtížnější je používat než HiveQL.</li><li>Výchozím výstupem je obvykle textový soubor a proto může být obtížné ho použít s nástroji vizualizace, jako je Excel. Obvykle budete navrstvit tabulku podregistru na výstup.</li></ul> |
| **Vlastní mapování/zmenšení** | <ul><li>Poskytuje plnou kontrolu nad rozvržením a snížením fází a provádění.</li><li>Umožňuje optimalizovat dotazy pro dosažení maximálního výkonu clusteru nebo pro minimalizaci zatížení serverů a sítě.</li><li>Komponenty lze zapsat v řadě známých jazyků.</li></ul> | <ul><li>Je obtížnější, než použití prasete nebo podregistru, protože je nutné vytvořit vlastní mapu a omezit komponenty.</li><li>Procesy, které vyžadují připojení sad dat, jsou obtížnější k implementaci.</li><li>I když jsou k dispozici testovací rozhraní, ladění kódu je složitější než normální aplikace, protože kód se spouští jako úloha služby Batch pod ovládacím prvkem plánovače úloh Hadoop.</li></ul> |
| **Apache HCatalog** | <ul><li>V této části najdete informace o cestě k úložišti, což usnadňuje správu a odstraňuje nutnost uživatelů, kteří znají, kde jsou data uložená.</li><li>Umožňuje oznámení o událostech, jako je například dostupnost dat, což umožňuje jiným nástrojům, jako je například Oozie, rozpoznat, kdy došlo k operacím.</li><li>Zpřístupňuje relační zobrazení dat, včetně dělení podle klíče, a usnadňuje přístup k datům.</li></ul> | <ul><li>Ve výchozím nastavení podporuje formáty souborů RCFile, CSV text, JSON text, SequenceFile a ORC, ale možná budete muset napsat vlastní SerDe pro jiné formáty.</li><li>HCatalog není bezpečný pro přístup z více vláken.</li><li>Existují určitá omezení pro datové typy pro sloupce při použití zavaděče HCatalog ve skriptech prasete. Další informace najdete v tématu [HCatLoader data types](https://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) v dokumentaci Apache HCatalog.</li></ul> |

Obvykle použijete nejjednodušší z těchto přístupů, které vám poskytnou požadované výsledky. Například může být možné dosáhnout těchto výsledků jenom pomocí podregistru, ale u složitějších scénářů možná budete muset použít prase nebo dokonce napsat vlastní mapu a omezit komponenty. Můžete se také rozhodnout, že po experimentování s podregistru nebo vepřovým sádlem může tato vlastní mapa a omezení komponent poskytovat lepší výkon tím, že vám umožní doladit a optimalizovat zpracování.

## <a name="custom-mapreduce-components"></a>Vlastní mapa/omezení součástí

Mapování/snížení kódu se skládá ze dvou samostatných funkcí implementovaných jako **map** a **omezení** součástí. Součást **mapy** je spuštěna paralelně na více uzlech clusteru, každý uzel, který používá mapování na vlastní podmnožinu dat uzlu. Funkce **zmenšování** seřazení a shrnutí výsledků ze všech funkcí mapy. Další informace o těchto dvou součástech najdete v tématu [použití MapReduce v systému Hadoop ve službě HDInsight](hdinsight-use-mapreduce.md).

Ve většině scénářů zpracování HDInsight je jednodušší a efektivnější používat abstrakce vyšší úrovně, jako je například prase nebo podregistr. Můžete také vytvořit vlastní mapu a omezit komponenty pro použití v rámci skriptů podregistru a provádět tak sofistikované zpracování.

Vlastní mapování/snížení součástí se obvykle napisují v jazyce Java. Hadoop poskytuje rozhraní pro streamování, které umožňuje také použití komponent vyvinutých v jiných jazycích, jako je C#, F #, Visual Basic, Python a JavaScript.

* Návod pro vývoj vlastních programů Java MapReduce najdete v tématu [vývoj programů Java MapReduce pro Hadoop v HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

Zvažte vytvoření vlastní mapy a omezení součástí těchto podmínek:

* Je nutné zpracovat data, která jsou zcela nestrukturovaná, pomocí analýzy dat a pomocí vlastní logiky získat z ní strukturované informace.
* Chcete provádět složité úkoly, které jsou obtížné (nebo nemusí) vyjádřit se v prase nebo v podregistru, aniž byste museli vytvářet disk UDF. Například může být nutné použít externí geografickou službu pro převod zeměpisných a zeměpisných souřadnic nebo IP adres ve zdrojových datech na názvy zeměpisných poloh.
* Chcete znovu použít stávající kód .NET, Python nebo JavaScript v části map/zmenšovat komponenty pomocí rozhraní pro streamování Hadoop.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Nahrání a spuštění vlastního programu MapReduce

Nejběžnější programy MapReduce jsou napsané v jazyce Java a kompilovány do souboru jar.

1. Po vývoji, kompilování a otestování programu MapReduce použijte `scp` příkaz k nahrání souboru jar do hlavnímu uzlu.

    ```cmd
    scp mycustomprogram.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Položku název_clusteru nahraďte názvem clusteru. Pokud jste použili heslo k zabezpečení účtu SSH, budete vyzváni k zadání hesla. Pokud jste použili certifikát, možná budete muset použít `-i` parametr k určení souboru privátního klíče.

1. Připojte se ke clusteru pomocí [příkazu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) . Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Z relace SSH spusťte program MapReduce prostřednictvím PŘÍZe.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    Tento příkaz odešle úlohu MapReduce do PŘÍZe. Vstupní soubor je `/example/data/sample.log` a výstupní adresář je `/example/data/logoutput` . Vstupní soubor a všechny výstupní soubory jsou uloženy do výchozího úložiště pro cluster.

## <a name="next-steps"></a>Další kroky

* [Použití jazyka C# s MapReduce streamingmi na Apache Hadoop ve službě HDInsight](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Vývoj programů Java MapReduce pro Apache Hadoop v HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Použití Azure Toolkit for Eclipse k vytvoření Apache Spark aplikací pro cluster HDInsight](../spark/apache-spark-eclipse-tool-plugin.md)
* [Použití uživatelem definovaných funkcí Pythonu (UDF) s Apache Hive a Apache prasetem v HDInsight](python-udf-hdinsight.md)
