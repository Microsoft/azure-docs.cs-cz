---
title: Spuštění vlastních programů MapReduce – Azure HDInsight
description: Kdy a jak spustit vlastní programy Apache MapReduce v clusterech Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 78623f738285e781cb561a3844db8fbf37226929
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645017"
---
# <a name="run-custom-mapreduce-programs"></a>Spuštění vlastních programů MapReduce

Velké datové systémy založené na Apache Hadoop, jako je HDInsight, umožňují zpracování dat pomocí široké škály nástrojů a technologií. Následující tabulka popisuje hlavní výhody a důležité informace pro každou z nich.

| Mechanismus dotazů | Výhody | Požadavky |
| --- | --- | --- |
| **Apache Hive pomocí HiveQL** | <ul><li>Vynikající řešení pro dávkové zpracování a analýzu velkého množství neměnných dat, pro shrnutí dat a pro dotazování na vyžádání. Používá známou syntaxi podobné SQL.</li><li>Lze jej použít k vytvoření trvalé tabulky dat, které lze snadno rozdělit a indexovat.</li><li>Přes stejná data lze vytvořit více externích tabulek a zobrazení.</li><li>Podporuje jednoduchou implementaci datového skladu, která poskytuje možnosti masivníškálování a odolnosti proti chybám pro ukládání a zpracování dat.</li></ul> | <ul><li>Vyžaduje, aby zdrojová data měla alespoň nějakou identifikovatelnou strukturu.</li><li>Není vhodný pro dotazy v reálném čase a aktualizace na úrovni řádků. Nejlépe se používá pro dávkové úlohy přes velké sady dat.</li><li>Nemusí být schopen provádět některé typy složitých úloh zpracování.</li></ul> |
| **Apache Prase pomocí Pig Latin** | <ul><li>Vynikající řešení pro manipulaci s daty jako sady, slučování a filtrování datových sad, použití funkcí na záznamy nebo skupiny záznamů a pro restrukturalizaci dat definováním sloupců, seskupením hodnot nebo převodem sloupců na řádky.</li><li>Může použít přístup založený na pracovním postupu jako posloupnost operací s daty.</li></ul> | <ul><li>Sql uživatelé mohou najít Pig Latin je méně známé a obtížnější než HiveQL.</li><li>Výchozí výstup je obvykle textový soubor, a proto může být obtížnější používat s vizualizačními nástroji, jako je Excel. Obvykle budete vrstvit tabulku Hive nad výstupem.</li></ul> |
| **Vlastní mapa/redukce** | <ul><li>Poskytuje plnou kontrolu nad mapou a snížit fáze a provádění.</li><li>Umožňuje optimalizovat dotazy tak, aby bylo dosaženo maximálního výkonu z clusteru nebo aby se minimalizovalo zatížení serverů a sítě.</li><li>Komponenty mohou být napsány v řadě známých jazyků.</li></ul> | <ul><li>Je to těžší než použití Pig nebo Hive, protože musíte vytvořit vlastní mapu a snížit komponenty.</li><li>Procesy, které vyžadují spojení sad dat je obtížnější implementovat.</li><li>I když jsou k dispozici testovací architektury, ladicí kód je složitější než normální aplikace, protože kód je spuštěn jako dávková úloha pod kontrolou plánovače úloh Hadoop.</li></ul> |
| **Apache HCatalog** | <ul><li>Abstrahuje podrobnosti cesty úložiště, usnadňuje správu a odstraňuje potřebu uživatelů vědět, kde jsou data uložena.</li><li>Umožňuje oznámení událostí, jako je dostupnost dat, což umožňuje jiným nástrojům, jako je Oozie zjistit, kdy došlo k operacím.</li><li>Poskytuje relační zobrazení dat, včetně dělení podle klíče a usnadňuje přístup k datům.</li></ul> | <ul><li>Ve výchozím nastavení podporuje formáty souborů RCFile, CSV, JSON, SequenceFile a ORC, ale možná budete muset napsat vlastní SerDe pro jiné formáty.</li><li>HCatalog není bezpečný pro přístup z více vláken.</li><li>Existují určitá omezení datových typů pro sloupce při použití zavaděče HCatalog ve skriptech Pig. Další informace naleznete v [tématu HCatLoader Datové typy](https://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) v dokumentaci Apache HCatalog.</li></ul> |

Obvykle použijete nejjednodušší z těchto přístupů, které mohou poskytnout výsledky, které požadujete. Například můžete být schopni dosáhnout těchto výsledků pomocí pouze Hive, ale pro složitější scénáře budete muset použít Pig, nebo dokonce napsat vlastní mapu a snížit komponenty. Můžete se také rozhodnout, po experimentování s Hive nebo Pig, že vlastní mapa a snížit komponenty může poskytnout lepší výkon tím, že vám umožní doladit a optimalizovat zpracování.

## <a name="custom-mapreduce-components"></a>Vlastní mapování/zmenšení komponent

Kód Map/reduce se skládá ze dvou samostatných funkcí implementovaných jako **mapa** a **snížení** komponent. Součást **mapy** je spuštěna paralelně na více uzlech clusteru, přičemž každý uzel použije mapování na vlastní podmnožinu dat uzlu. Komponenta **reduce** shromažďuje a shrnuje výsledky ze všech mapových funkcí. Další informace o těchto dvou součástech naleznete [v tématu Použití mapreduce v Hadoop na HDInsight](hdinsight-use-mapreduce.md).

Ve většině scénářů zpracování HDInsight je jednodušší a efektivnější použít abstrakce vyšší úrovně, jako je Pig nebo Hive. Můžete také vytvořit vlastní mapu a snížit součásti pro použití ve skriptech Hive k provedení složitějšího zpracování.

Vlastní součásti mapy nebo snížení jsou obvykle napsány v Jazyce Java. Hadoop poskytuje rozhraní pro streamování, které také umožňuje použití komponent, které jsou vyvinuty v jiných jazycích, jako je C#, F#, Visual Basic, Python a JavaScript.

* Návod k vývoji vlastních programů Java MapReduce najdete v [tématu Vývoj programů Java MapReduce pro Hadoop na WEBU HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

Zvažte vytvoření vlastní mapy a zmenšení komponent pro následující podmínky:

* Je třeba zpracovat data, která je zcela nestrukturované analýzou dat a pomocí vlastní logiky získat strukturované informace z nich.
* Chcete provádět složité úkoly, které je obtížné (nebo nemožné) vyjádřit v Pig nebo Hive bez použití k vytvoření UDF. Může být například nutné použít externí službu geokódování k převodu souřadnic zeměpisné šířky a délky nebo ADRES IP ve zdrojových datech na názvy zeměpisných umístění.
* Chcete znovu použít existující kód .NET, Python nebo JavaScript v mapových/zmenšených komponentách pomocí rozhraní streamování Hadoop.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Nahrání a spuštění vlastního programu MapReduce

Nejběžnější Programy MapReduce jsou napsány v Jazyce Java a kompilovány do souboru jar.

1. Po vývoji, kompilaci a testování programu MapReduce použijte `scp` příkaz k nahrání souboru jar do headnode.

    ```cmd
    scp mycustomprogram.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Nahraďte název clusteru názvem clusteru. Pokud jste k zabezpečení účtu SSH použili heslo, budete vyzváni k zadání hesla. Pokud jste použili certifikát, bude `-i` pravděpodobně nutné použít parametr k určení souboru soukromého klíče.

1. Pomocí [příkazu ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) se připojte ke clusteru. Upravte níže uvedený příkaz nahrazením názvu clusteru názvem clusteru a zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Z relace SSH spusťte program MapReduce prostřednictvím YARN.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    Tento příkaz odešle úlohu MapReduce do yarn. Vstupní soubor `/example/data/sample.log`je a výstupní `/example/data/logoutput`adresář je . Vstupní soubor a všechny výstupní soubory jsou uloženy do výchozího úložiště pro cluster.

## <a name="next-steps"></a>Další kroky

* [Použití Jazyka C# s mapovým streamováním MapReduce na Apache Hadoop v HDInsightu](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Vývoj Java MapReduce programy pro Apache Hadoop na HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Použití sady Nástrojů Azure pro Eclipse k vytváření aplikací Apache Spark pro cluster HDInsight](../spark/apache-spark-eclipse-tool-plugin.md)
* [Použití uživatelem definovaných funkcí Pythonu (UDF) s Apache Hive a Apache Pig v HDInsightu](python-udf-hdinsight.md)
