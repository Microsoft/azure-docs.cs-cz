---
title: Použití Apache Hivu a Vzdálená plocha v HDInsight – Azure
description: Zjistěte, jak se připojit ke clusteru Hadoop v HDInsight pomocí vzdálené plochy a následné spouštění dotazů Hive pomocí rozhraní příkazového řádku Hive.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 86c1d9adc3b4ff66e2f02617df551e496a5482c8
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53014612"
---
# <a name="use-apache-hive-with-apache-hadoop-on-hdinsight-with-remote-desktop"></a>Použití Apache Hivu s Apache Hadoop v HDInsight pomocí vzdálené plochy
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

V tomto článku najdete informace o připojení ke clusteru HDInsight pomocí vzdálené plochy a následné spouštění dotazů Hive pomocí Hive rozhraní příkazového řádku (CLI).

> [!IMPORTANT]
> Vzdálená plocha je dostupná pouze na clustery HDInsight, používající jako operační systém Windows. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> HDInsight 3.4 nebo větší, přečtěte si téma [použití Hivu se službou HDInsight a Beeline](apache-hadoop-use-hive-beeline.md) informace o spouštění dotazů Hive přímo v clusteru z příkazového řádku.

## <a id="prereq"></a>Požadavky
K dokončení kroků v tomto článku, budete potřebovat následující:

* Cluster Windows systémem HDInsight (Hadoop v HDInsight)
* Klientský počítač používající systém Windows 10, Windows 8 nebo Windows 7

## <a id="connect"></a>Připojte se přes vzdálenou plochu
Povolení vzdálené plochy pro HDInsight cluster a pak k němu připojit pomocí následujících pokynů na adrese [připojit ke clusterům HDInsight pomocí protokolu RDP](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="hive"></a>Použití příkazu Hive
Jakmile budete mít připojené na plochu pro HDInsight cluster, postupujte následovně pro práci s Hive:

1. Z plochy HDInsight, spusťte **příkazového řádku Hadoopu**.
2. Zadejte následující příkaz pro spuštění rozhraní příkazového řádku Hive:

        %hive_home%\bin\hive

    Po spuštění rozhraní příkazového řádku, se zobrazí výzva Hive rozhraní příkazového řádku: `hive>`.
3. Pomocí rozhraní příkazového řádku zadejte následující příkazy, čímž umožňuje vytvořit novou tabulku s názvem **log4jLogs** pomocí ukázkových dat:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Tyto příkazy provádět následující akce:

   * **DROP TABLE**: Odstraní tabulku a datový soubor, pokud tabulka již existuje.
   * **CREATE EXTERNAL TABLE**: vytvoří novou tabulku "externí" v podregistru. Externí tabulky uložte definici tabulky Hive (data zůstane v původním umístění).

     > [!NOTE]
     > Externí tabulky, které má být použit, při očekáváte, že podkladová data aktualizovat pomocí externího zdroje (například procesu nahrávání automatizovaných datových) nebo jiné operaci MapReduce, ale chcete, aby dotazy Hive používat nejnovější data.
     >
     > Vyřazení externí tabulky neodpovídá **není** odstranit data, pouze definici tabulky.
     >
     >
   * **ŘÁDEK formát**: říká Hive formátování data. V tomto případě pole v každém protokolu jsou oddělené mezerou.
   * **ULOŽEN jako textový soubor umístění**: říká Hive, ve kterém se data ukládají (do adresáře příkladu/dat) a, která je uložená jako text.
   * **Vyberte**: vybere počet všech řádků ve kterém sloupci **t4** obsahuje hodnotu **[Chyba]**. To by měl vrátit hodnotu **3** vzhledem k tomu, že existují tři řádky, které obsahují tuto hodnotu.
   * **INPUT__FILE__NAME jako "%.log"** -říká Hive, který jsme by měl vrátit pouze data ze souborů s koncovkou. log. To omezuje vyhledávání na souboru sample.log, který obsahuje data a udržuje ho z vracet data z jiných příklad datové soubory, které neodpovídají schématu, které jsme definovali.
4. Pomocí následujících příkazů vytvořte nové "vnitřní" tabulku s názvem **nepřenesl**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Tyto příkazy provádět následující akce:

   * **Vytvoření tabulky IF NOT EXISTS**: Pokud ještě neexistuje, vytvoří tabulku. Vzhledem k tomu, **externí** – klíčové slovo se nepoužívá, je interní tabulku, která je uložena v datovém skladu Hive a spravuje úplně Hive.

     > [!NOTE]
     > Na rozdíl od **externí** tabulek, vyřadit interní tabulku také odstraní podkladová data.
     >
     >
   * **ULOŽENÉ jako ORC**: ukládá data ve sloupcovém formátu (ORC) optimalizované řádek. Toto je vysoce optimalizovaných a efektivní formát pro ukládání dat Hive.
   * **VLOŽIT PŘEPSÁNÍ... Vyberte**: vybere řádky z **log4jLogs** tabulce, která obsahuje **[Chyba]**, pak vloží data do **nepřenesl** tabulky.

     Chcete-li ověřit, že, které obsahují jenom řádky **[Chyba]** ve sloupci t4 byly uloženy do **nepřenesl** tabulky, použijte následující příkaz vrátí všechny řádky z **nepřenesl**:

       Vybrat * z nepřenesl;

     Tří řádků dat by měla být vrácena, všechny obsahující **[Chyba]** ve sloupci t4.

## <a id="summary"></a>Shrnutí
Jak je vidět, příkaz Hive poskytuje snadný způsob, jak interaktivně spouštění dotazů Hive v clusteru HDInsight, sledovat stav úlohy a načtení výstupu.

## <a id="nextsteps"></a>Další kroky
Obecné informace o Hivu ve službě HDInsight:

* [Použití Hivu s Hadoopem v HDInsight](hdinsight-use-hive.md)

Informace o jiných způsobech, jakými můžete pracovat s Hadoop v HDInsight:

* [Použití Pigu se systémem Hadoop v HDInsight](hdinsight-use-pig.md)
* [Použití MapReduce se systémem Hadoop v HDInsight](hdinsight-use-mapreduce.md)

Pokud používáte pomocí Hive Tez, naleznete v následujících dokumentech pro informace o ladění:

* [Použití uživatelského rozhraní Tez na HDInsight se systémem Windows](../hdinsight-debug-tez-ui.md)
* [Použití zobrazení Ambari Tez na HDInsight založených na Linuxu](../hdinsight-debug-ambari-tez-view.md)

[1]:apache-hadoop-visual-studio-tools-get-started.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx
