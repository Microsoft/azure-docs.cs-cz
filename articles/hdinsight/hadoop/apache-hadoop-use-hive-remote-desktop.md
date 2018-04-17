---
title: Použijte Hadoop Hive a Vzdálená plocha v HDInsight - Azure | Microsoft Docs
description: Zjistěte, jak se připojit ke clusteru Hadoop v HDInsight pomocí vzdálené plochy a pak spouštění dotazů Hive pomocí rozhraní příkazového řádku Hive.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 8c228e35-d58a-4f22-917a-1d20c9da89b4
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 70eab088ce87d8a62d3f258b70aaec5e2e147d0e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="use-hive-with-hadoop-on-hdinsight-with-remote-desktop"></a>Použijte Hive s Hadoop v HDInsight pomocí vzdálené plochy
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

V tomto článku zjistěte, jak se připojit ke clusteru HDInsight pomocí vzdálené plochy a pak spouštění dotazů Hive pomocí Hive rozhraní příkazového řádku (CLI).

> [!IMPORTANT]
> Vzdálená plocha je dostupná pouze na clustery HDInsight, které používají Windows jako operační systém. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> HDInsight 3.4 nebo větší, projděte si téma [používání Hive s HDInsight a Beeline](apache-hadoop-use-hive-beeline.md) informace o spouštění dotazů Hive přímo na clusteru z příkazového řádku.

## <a id="prereq"></a>Požadavky
Pokud chcete provést kroky v tomto článku, budete potřebovat následující:

* Cluster HDInsight se systémem Windows (Hadoop v HDInsight)
* Klientský počítač se systémem Windows 10, Windows 8 nebo Windows 7

## <a id="connect"></a>Připojit pomocí vzdálené plochy
Povolení vzdálené plochy pro HDInsight cluster a pak připojit pomocí pokynů uvedených v [připojení ke clusterům HDInsight pomocí protokolu RDP](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="hive"></a>Použijte příkaz Hive
Když připojíte k ploše pro HDInsight cluster, použijte následující postup pro práci s Hive:

1. Z plochy HDInsight, spusťte **Hadoop příkazového řádku**.
2. Zadejte následující příkaz, který spouští rozhraní příkazového řádku Hive:

        %hive_home%\bin\hive

    Pokud bylo zahájeno rozhraní příkazového řádku, zobrazí se řádku Hive rozhraní příkazového řádku: `hive>`.
3. Pomocí rozhraní příkazového řádku, zadejte následující příkazy, čímž umožňuje vytvořit novou tabulku s názvem **log4jLogs** pomocí ukázkových dat:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Tyto příkazy provádět následující akce:

   * **VYŘAĎTE tabulku**: Odstraní tabulku a datový soubor, pokud tabulka již existuje.
   * **Vytvoření externí tabulky**: vytvoří novou tabulku "externí" v Hive. Externí tabulky uložit pouze definici tabulky Hive (data je ponechán v původním umístění).

     > [!NOTE]
     > Externí tabulky by měl být použit při očekáváte, že v základních datech aktualizovat externího zdroje (například procesu nahrávání automatizované dat), nebo jiná operace MapReduce, ale chcete, aby dotazy Hive používat nejnovější data.
     >
     > Vyřazení externí tabulku nemá **není** odstranit data, jenom definici tabulky.
     >
     >
   * **Řádek formátu**: informuje Hive formátování data. V takovém případě polí v každém protokolu jsou oddělené mezerou.
   * **ULOŽENÉ umístění textový soubor AS**: informuje Hive, kde je data uložená (adresář, příklad nebo data) a která je uložena jako text.
   * **Vyberte**: počet všech řádků vybere kde sloupec **t4** obsahuje hodnotu **[Chyba]**. To by měla vrátit hodnotu **3** vzhledem k tomu, že existují tři řádky, které obsahují tuto hodnotu.
   * **INPUT__FILE__NAME jako '%.log'** -informuje Hive, který jsme by měl vrátit pouze data ze souborů končící na. log. To brání hledání k sample.log souboru, který obsahuje data a udržuje ho z vrací data z jiných příkladu datové soubory, které neodpovídají schématu, které jsme definovali.
4. Použít následující příkazy k vytvoření nové "interní" tabulku s názvem **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Tyto příkazy provádět následující akce:

   * **Vytvoření tabulka není v případě existuje**: vytvoří tabulku, pokud ještě neexistuje. Protože **externí** – klíčové slovo nepoužívá, jde o interní tabulku, která je uložena v datovém skladu Hive a je zcela spravuje Hive.

     > [!NOTE]
     > Na rozdíl od **externí** tabulky, vyřazení interní tabulku taky odstraní zadaná data.
     >
     >
   * **ULOŽENÉ ORC AS**: ukládá data ve sloupcovém formátu (ORC) optimalizované řádek. Toto je vysoce optimalizovaný a efektivní formát pro ukládání dat Hive.
   * **PŘEPSAT INSERT... Vyberte**: vybere řádky z **log4jLogs** tabulku, která obsahují **[Chyba]**, pak vkládá data do **errorLogs** tabulky.

     Chcete-li ověřit, že pouze řádky, které obsahují **[Chyba]** ve sloupci t4 byly uloženy do **errorLogs** tabulky, použijte následující příkaz a vrátí všechny řádky z **errorLogs**:

       Vybrat * z errorLogs;

     Tří řádků dat má být vrácen, všechny obsahující **[Chyba]** v t4 sloupce.

## <a id="summary"></a>Shrnutí
Jak je vidět příkaz Hive poskytuje snadný způsob, jak interaktivně spouštění dotazů Hive v clusteru HDInsight, monitorovat stav úlohy a načíst výstup.

## <a id="nextsteps"></a>Další kroky
Obecné informace o Hive v HDInsight:

* [Použijte Hive s Hadoop v HDInsight](hdinsight-use-hive.md)

Informace o jiných způsobech můžete pracovat s Hadoop v HDInsight:

* [Použijte Pig s Hadoop v HDInsight](hdinsight-use-pig.md)
* [Používání nástroje MapReduce s Hadoop v HDInsight](hdinsight-use-mapreduce.md)

Pokud používáte s Hive Tez, najdete v následujících dokumentech pro ladění informace:

* [Pomocí uživatelského rozhraní Tez na HDInsight se systémem Windows](../hdinsight-debug-tez-ui.md)
* [Použití zobrazení Ambari Tez na HDInsight se systémem Linux](../hdinsight-debug-ambari-tez-view.md)

[1]:apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx
