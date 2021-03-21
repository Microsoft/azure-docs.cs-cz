---
title: Použití Grafana ve službě Azure HDInsight
description: Přečtěte si, jak získat přístup k řídicímu panelu Grafana s clustery Apache Hadoop ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/27/2019
ms.openlocfilehash: cd2a09b6c3196302dcb74fd363481706021a4d4d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98940876"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Přístup ke Grafaně ve službě Azure HDInsight

[Grafana](https://grafana.com/) je oblíbený Open Source tvůrce grafů a řídicích panelů. Grafana je funkce bohatá; neumožňuje uživatelům vytvářet přizpůsobitelné řídicí panely a sdílet je, ale také nabízí šablony/skripty řídicí panely, integraci protokolu LDAP, více zdrojů dat a další.

V současné době se v Azure HDInsight Grafana podporuje s typy clusterů Spark, HBA, Kafka a Interactive Query. U clusterů s povolenou sadou Enterprise Security Pack není podporována podpora.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-an-apache-hadoop-cluster"></a>Vytvoření clusteru Apache Hadoop

Další informace najdete v tématu [Vytvoření clusterů Apache Hadoop pomocí Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md). Jako **typ clusteru** vyberte **Spark**, **Kafka**, **HBA** nebo **interaktivní dotaz**.

## <a name="access-the-grafana-dashboard"></a>Přístup k řídicímu panelu Grafana

1. Ve webovém prohlížeči přejděte na místo, `https://CLUSTERNAME.azurehdinsight.net/grafana/` kde název_clusteru je název vašeho clusteru.

1. Zadejte přihlašovací údaje uživatele clusteru Hadoop.

1. Zobrazí se řídicí panel Grafana, který bude vypadat jako v tomto příkladu:

    ![Webový řídicí panel HDInsight Grafana](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "Řídicí panel HDInsight Grafana")

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte cluster, který jste vytvořili, pomocí následujících kroků:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Do **vyhledávacího** pole v horní části zadejte **HDInsight**.

1. V části **služby** vyberte **clustery HDInsight** .

1. V seznamu clusterů HDInsight, které se zobrazí, vyberte **...** vedle clusteru, který jste vytvořili.

1. Vyberte **Odstranit**. Vyberte **Ano**.

## <a name="next-steps"></a>Další kroky

Další informace o analýze dat pomocí HDInsight najdete v následujících článcích:

* [Použijte Apache Hive se službou HDInsight](../hadoop/hdinsight-use-hive.md).

* [Použití MapReduce se službou HDInsight](../hadoop/hdinsight-use-mapreduce.md).

* Začněte [používat nástroje Visual Studio Hadoop pro HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
