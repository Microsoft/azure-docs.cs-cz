---
title: Použití Grafany v Azure HDInsight
description: Zjistěte, jak získat přístup k řídicímu panelu Grafana pomocí clusterů Apache Hadoop v Azure HDInsightu
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: cd515bfd1dc57e78a041ed96686e1ba692bf6d3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082859"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Přístup ke Grafaně ve službě Azure HDInsight

[Grafana](https://grafana.com/) je populární, open-source graf a dashboard builder. Grafana je bohatý na rysy; nejen, že umožňuje uživatelům vytvářet přizpůsobitelné a sdíletelné řídicí panely, ale také nabízí šablony / skriptované řídicí panely, integraci LDAP, více zdrojů dat a další.

V současné době v Azure HDInsight grafana je podporovaná s typy clusterů Spark, HBase, Kafka a interaktivní dotaz. Není podporována pro clustery s povolenou sadou Enterprise Security Pack.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="create-an-apache-hadoop-cluster"></a>Vytvoření clusteru Apache Hadoop

Viz [Vytvoření clusterů Apache Hadoop pomocí portálu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md). Pro **typ clusteru**vyberte **Spark**, **Kafka**, **HBase**nebo **Interaktivní dotaz**.

## <a name="access-the-grafana-dashboard"></a>Přístup k řídicímu panelu Grafana

1. Z webového prohlížeče `https://CLUSTERNAME.azurehdinsight.net/grafana/` přejděte na místo, kde cluster CLUSTERNAME je název clusteru.

1. Zadejte pověření uživatele clusteru Hadoop.

1. Řídicí panel Grafana se zobrazí a vypadá takto:

    ![Webový řídicí panel HDInsight Grafana](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "Řídicí panel HDInsight Grafana")

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete nadále používat tuto aplikaci, odstraňte cluster, který jste vytvořili pomocí následujících kroků:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Do pole **Hledat** v horní části zadejte **HDInsight**.

1. V části **Služby** **vyberte clustery HDInsight** .

1. V seznamu clusterů HDInsight, který se zobrazí, vyberte **...** vedle clusteru, který jste vytvořili.

1. Vyberte **Odstranit**. Vyberte **ano**.

## <a name="next-steps"></a>Další kroky

Další informace o analýze dat pomocí HDInsight najdete v následujících článcích:

* [Použijte Apache Hive s HDInsight](../hadoop/hdinsight-use-hive.md).

* [Použijte MapReduce s HDInsight](../hadoop/hdinsight-use-mapreduce.md).

* [Začínáme používat nástroje Hadoop sady Visual Studio pro HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
