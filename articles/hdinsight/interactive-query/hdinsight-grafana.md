---
title: Použití Grafana ve službě Azure HDInsight
description: Přečtěte si, jak získat přístup k řídicímu panelu Grafana s clustery Apache Hadoop ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/27/2019
ms.openlocfilehash: 81fd3b368f9405192c164ed7a0638caad0cd75fc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869748"
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

    :::image type="content" source="./media/hdinsight-grafana/hdinsight-grafana-dashboard.png " alt-text="Webový řídicí panel HDInsight Grafana" border="true":::

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
