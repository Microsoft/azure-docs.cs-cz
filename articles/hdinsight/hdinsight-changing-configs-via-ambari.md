---
title: Optimalizace clusterů pomocí Apache Ambari ve službě Azure HDInsight
description: Ke konfiguraci a optimalizaci clusterů Azure HDInsight použijte webové uživatelské rozhraní Apache Ambari.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/04/2020
ms.openlocfilehash: 14268e58296554cedc9488e48b41719421797a57
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933285"
---
# <a name="optimize-clusters-with-apache-ambari-in-azure-hdinsight"></a>Optimalizace clusterů pomocí Apache Ambari ve službě Azure HDInsight

HDInsight poskytuje Apache Hadoop clusterů pro aplikace pro zpracování velkých objemů dat. Správa, monitorování a optimalizace těchto komplexních clusterů s více uzly může být náročné. Apache Ambari je webové rozhraní pro správu a monitorování clusterů HDInsight Linux.

Úvod k používání webového uživatelského rozhraní Ambari najdete v tématu [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md) .

Přihlaste se k Ambari `https://CLUSTERNAME.azurehdidnsight.net` s přihlašovacími údaji clusteru. Úvodní obrazovka zobrazuje řídicí panel přehled.

![Zobrazuje se řídicí panel uživatele Apache Ambari](./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png)

Webové uživatelské rozhraní Ambari se používá ke správě hostitelů, služeb, výstrah, konfigurací a zobrazení. Ambari se nedá použít k vytvoření clusteru HDInsight nebo k upgradu služeb. Nedokáže taky spravovat zásobníky a verze, vyřadit z provozu nebo přeřadit hostitele z provozu nebo přidat služby do clusteru.

## <a name="manage-your-clusters-configuration"></a>Správa konfigurace clusteru

Nastavení konfigurace pomůžou ladit konkrétní službu. Chcete-li upravit nastavení konfigurace služby, vyberte službu z postranního panelu **služby** (vlevo). Pak na stránce s podrobnostmi služby přejděte na kartu **Konfigurace** .

![Boční panel služeb Apache Ambari Services](./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png)

## <a name="modify-namenode-java-heap-size"></a>Úprava velikosti haldy Java NameNode

Velikost haldy Java NameNode závisí na mnoha faktorech, jako je například zatížení clusteru. Také počet souborů a počet bloků. Výchozí velikost 1 GB funguje ve většině clusterů, i když některé úlohy můžou vyžadovat více nebo méně paměti.

Úprava velikosti haldy NameNode Java:

1. Z bočního panelu služeb vyberte **HDFS** a přejděte na kartu **Konfigurace** .

    ![Konfigurace Apache Ambari HDFS](./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png)

1. Najděte nastavení **Velikost haldy Java NameNode**. K zadání a vyhledání konkrétního nastavení můžete použít také textové pole **Filtr** . Vyberte ikonu **pera** vedle názvu nastavení.

    ![Velikost haldy pro Apache Ambari NameNode Java](./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png)

1. Do textového pole zadejte novou hodnotu a stisknutím klávesy **ENTER** změny uložte.

    ![Ambari upravit NameNode haldy Java size1](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png)

1. Velikost haldy Java NameNode se změní na 1 GB ze 2 GB.

    ![Upravená size2 haldy NameNode Java](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Uložte změny kliknutím na zelené tlačítko **Uložit** v horní části obrazovky konfigurace.

    !["Apache Ambari Save Configurations"](./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png)

## <a name="next-steps"></a>Další kroky

* [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [REST API Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Optimalizace Apache HBase](./optimize-hbase-ambari.md)
* [Optimalizace Apache Hivu](./optimize-hive-ambari.md)
* [Optimalizace Apache Pigu](./optimize-pig-ambari.md)
