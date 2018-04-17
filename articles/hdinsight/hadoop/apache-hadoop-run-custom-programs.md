---
title: Spusťte vlastní programy MapReduce - Azure HDInsight | Microsoft Docs
description: Kdy a jak spouštět vlastní programy MapReduce v HDInsight.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: ashishth
ms.openlocfilehash: 94d199642b409a2fd087ec1543651031a907d09f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="run-custom-mapreduce-programs"></a>Spuštění vlastních programů MapReduce

Systémy založené na Hadoop velkých objemů dat, jako je například HDInsight umožňují zpracování dat pomocí širokou škálu nástroje a technologie. Následující tabulka popisuje hlavní výhody a důležité informace pro každé z nich.

| Mechanismus dotazu | Výhody | Požadavky |
| --- | --- | --- |
| **Hive pomocí HiveQL** | <ul><li>Vynikající řešení pro dávkové zpracování a analýzy velkých objemů dat neměnné pro shrnutí dat a na vyžádání dotazování. Používá známé syntaxe podobné jazyku SQL.</li><li>Slouží k vytvoření trvalé tabulek dat, která lze snadno rozdělena na oddíly a indexovat.</li><li>Více externí tabulky a zobrazení můžete vytvořit přes stejná data.</li><li>Podporuje implementace jednoduchého datového skladu, zajišťující masivní škálování a odolnost proti chybám možnosti pro úložiště dat a zpracování.</li></ul> | <ul><li>To vyžaduje zdrojová data tak, aby měl alespoň některé osobní struktura.</li><li>Není vhodný pro dotazy v reálném čase a řádek úrovně aktualizací. Nejlíp se používá pro úlohy batch přes rozsáhlých datových sad.</li><li>Nemusí být schopna provádět některé typy úloh komplexní zpracování.</li></ul> |
| **Pomocí Pig Latin pig** | <ul><li>Vynikající řešení pro manipulace s daty jako nastaví, sloučení a filtrování datových sad, použití funkce pro skupiny záznamů, nebo záznamy a pro změnu struktury dat tak, že definujete sloupců, podle hodnoty seskupení nebo převedením sloupce na řádky.</li><li>O přístupu na základě pracovní postup může použít jako posloupnost operací na data.</li></ul> | <ul><li>Uživatelé SQL zjistit, že je Pig Latin méně známé a obtížnější než HiveQL.</li><li>Výchozí výstup je obvykle textový soubor a proto může být obtížnější pro použití s vizualizace nástroje, jako je Excel. Obvykle bude vrstvy tabulku Hive prostřednictvím výstup.</li></ul> |
| **Vlastní mapy nebo snížení** | <ul><li>Poskytuje plnou kontrolu nad mapy a snížit fáze a provádění.</li><li>To umožňuje používat dotazy se optimalizace k dosažení maximální výkon z clusteru, nebo chcete-li minimalizovat zatížení serveru a sítě.</li><li>Součástí může být napsán v rozsahu známé jazyků.</li></ul> | <ul><li>Je obtížnější než použití Pig nebo Hive, protože je nutné vytvořit své vlastní mapování a snížit součásti.</li><li>Procesy, které vyžadují připojení sad dat je složité implementace.</li><li>I když jsou k dispozici systémů testování, ladění kódu je mnohem složitější než normální aplikace protože kód běží jako dávkovou úlohu pod kontrolou plánovače úloh Hadoop.</li></ul> |
| **HCatalog** | <ul><li>Ho abstrahuje podrobnosti o cestě úložiště, správy snadněji a odebírání potřebu uživatelům vědět, kde jsou data uložena.</li><li>Umožňuje oznámení o události, jako je dostupnost dat, což jiných nástrojů, například Oozie zjistit, kdy operace došlo.</li><li>To zpřístupňuje relační zobrazení data, včetně rozdělení do oddílů pomocí klíče a usnadňuje data pro přístup k.</li></ul> | <ul><li>Podporuje RCFile CSV text, text JSON, SequenceFile a ORC formáty souborů ve výchozím nastavení, ale budete muset napsat vlastní SerDe pro ostatní formáty.</li><li>HCatalog není bezpečné pro přístup z více vláken.</li><li>Při použití zavaděč HCatalog skriptů Pig platí určitá omezení pro datové typy pro sloupce. Další informace najdete v tématu [HCatLoader datové typy](http://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) v dokumentaci Apache HCatalog.</li></ul> |

Obvykle použijete nejjednodušší přístupů poskytující výsledky, které vyžadujete. Například může být schopní dosáhnout takové výsledky pomocí právě Hive, ale pro složitější scénáře budete muset použít Pig, nebo i zápis své vlastní mapování a snížit součásti. Můžete taky rozhodnout po experimentování se Hive nebo Pig, že vlastní mapy a snížit součásti může poskytovat lepší výkon tím, že se můžete optimalizovat zpracování a systém doladit.

## <a name="custom-mapreduce-components"></a>Vlastní mapy nebo snížit součásti

Mapa nebo snížit kód obsahuje dvě samostatné funkce, které jsou implementované jako **mapy** a **snížit** součásti. **Mapy** součást je spustit souběžně na více uzlech clusteru, každý uzel použití mapování na podmnožinu uzlu vlastní data. **Snížit** součást seřadí a shrnuje výsledky z všechny funkce mapy. Další informace o těchto dvou komponentách najdete v tématu [použití MapReduce v Hadoop v HDInsight](hdinsight-use-mapreduce.md).

Ve většině scénářů zpracování HDInsight je jednodušší a efektivnější použít vyšší úrovni abstrakce například Pig nebo Hive. Můžete také vytvořit vlastní mapy a snížit komponenty pro použití v rámci Hive skripty sofistikovanější zpracování.

Vlastní mapy nebo snížit součásti jsou obvykle napsanou v jazyce Java. Hadoop poskytuje streamování rozhraní, které také umožňuje součásti použije vyvinuté v jiných jazycích, například C#, F #, Visual Basic, Python a JavaScript.

* Návod na vývoj vlastní programy Java MapReduce najdete v tématu [vyvíjet MapReduce Java programy pro Hadoop v HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).
* Příklad používá Python, najdete v sekci [vyvíjet Python streamování MapReduce programy pro HDInsight](apache-hadoop-streaming-python.md).

Zvažte vytvoření své vlastní mapování a snížit součásti byly splněny následující podmínky:

* Je třeba zpracovat data, která je zcela nestrukturovaných analýzou dat a k získání strukturovaných informace z něj pomocí vlastní logiky.
* Chcete provádět komplexní úlohy, které je obtížné (nebo dokonce znemožňují) pro vyjádření v Pig nebo Hive bez nutnosti vytvoření uživatelem definovanou FUNKCI. Například budete muset použít služby externí geografické kódování převést zeměpisnou šířku a zeměpisnou délku souřadnice nebo IP adresy v zdrojová data na názvy zeměpisné umístění.
* Chcete znovu použít váš stávající kód rozhraní .NET, Python nebo JavaScript v mapy nebo snížit součásti pomocí streamování rozhraní Hadoop.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Nahrání a spusťte svůj vlastní program MapReduce

Nejběžnější MapReduce programy jsou napsané v jazyce Java a zkompilovat na soubor jar.

1. Po vyvinuté, kompilovat a otestovat váš program MapReduce, použijte `scp` příkazu nahrajte soubor jar do headnode.

    ```bash
    scp mycustomprogram.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Nahraďte **uživatelské jméno** pomocí uživatelského účtu SSH pro váš cluster. Nahraďte **CLUSTERNAME** se název clusteru. Pokud jste použili heslo k zabezpečení účtu SSH, zobrazí se výzva k zadání hesla. Pokud jste použili certifikát, budete možná muset použít `-i` parametr k určení souboru privátního klíče.

2. Připojit ke clusteru pomocí [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Z relace SSH spusťte program MapReduce prostřednictvím YARN.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    Tento příkaz odešle na YARN úlohy MapReduce. Vstupní soubor je `/example/data/sample.log`, a je k zadanému výstupnímu adresáři `/example/data/logoutput`. Vstupní soubor a všechny výstupní soubory jsou uloženy na výchozí úložiště pro cluster.

## <a name="next-steps"></a>Další postup

* [Použití jazyka C# s MapReduce, streamování systému Hadoop v HDInsight](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Vývoj aplikací Java MapReduce pro Hadoop v HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Vývoj Python streamování MapReduce programy pro HDInsight](apache-hadoop-streaming-python.md)
* [Vytvoření aplikací Spark pro cluster služby HDInsight pomocí nástrojů Azure pro Eclipse](../spark/apache-spark-eclipse-tool-plugin.md)
* [Funkce (UDF) s Hive a Pig definované uživatelem Python použití v HDInsight](python-udf-hdinsight.md)
