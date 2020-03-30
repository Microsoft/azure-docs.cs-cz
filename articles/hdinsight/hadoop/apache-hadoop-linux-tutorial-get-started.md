---
title: 'Úvodní příručka: Vytvoření clusteru Apache Hadoop v Azure HDInsight pomocí šablony Správce prostředků'
description: V tomto rychlém startu vytvoříte cluster Apache Hadoop v Azure HDInsight pomocí šablony Správce prostředků
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: ae0f29b8085bd9637f527f2a58229dd89ce6933b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80064674"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Úvodní příručka: Vytvoření clusteru Apache Hadoop v Azure HDInsight pomocí šablony Správce prostředků

V tomto rychlém startu použijete šablonu Azure Resource Manager k vytvoření clusteru [Apache Hadoop](./apache-hadoop-introduction.md) v Azure HDInsight. Hadoop byl původní open-source framework pro distribuované zpracování a analýzu velkých objemů dat na klastrech. Ekosystém Hadoop zahrnuje související software a nástroje, včetně Apache Hive, Apache HBase, Spark, Kafka a mnoho dalších.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]
  
Aktuálně se HDInsight dodává se [sedmi různými typy clusteru](../hdinsight-overview.md#cluster-types-in-hdinsight). Každý typ clusteru podporuje odlišnou sadu komponent. Všechny typy clusteru podporují Hive. Seznam podporovaných součásti v HDInsight naleznete v tématu [Co je nového ve verzích clusterů Hadoop poskytovaných v HDInsight?](../hdinsight-component-versioning.md)  

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="create-an-apache-hadoop-cluster"></a>Vytvoření clusteru Apache Hadoop

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure QuickStart](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password).

:::code language="json" source="~/quickstart-templates/101-hdinsight-linux-ssh-password/azuredeploy.json" range="1-148":::


V šabloně jsou definovány dva prostředky Azure:

* [Microsoft.Storage/storageAccounts:](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts)vytvořte účet úložiště Azure.
* [Microsoft.HDInsight/cluster:](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters)vytvořte cluster HDInsight.

### <a name="deploy-the-template"></a>Nasazení šablony

1. Kliknutím na tlačítko **Nasadit do Azure** se přihlaste do Azure a otevřete šablonu Správce prostředků.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Zadejte nebo vyberte tyto hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Předplatné|V rozevíracím seznamu vyberte předplatné Azure, které se používá pro cluster.|
    |Skupina prostředků|V rozevíracím seznamu vyberte existující skupinu prostředků nebo vyberte **Vytvořit nový**.|
    |Umístění|Hodnota se automaticky naplní umístěním použitým pro skupinu prostředků.|
    |Název clusteru|Zadejte globálně jedinečný název. Pro tuto šablonu používejte pouze malá písmena a čísla.|
    |Typ clusteru | Vyberte **hadoop**. |
    |Uživatelské jméno přihlášení clusteru|Zadejte uživatelské jméno, výchozí je **admin**.|
    |Heslo přihlášení clusteru|Zadejte heslo. Heslo musí mít délku alespoň 10 znaků a musí obsahovat alespoň jednu číslici, jedno velké a jedno malé písmeno, jeden nealfanumerický znak (kromě znaků " " ). |
    |Uživatelské jméno SSH|Zadejte uživatelské jméno, výchozí je **sshuser**|
    |Ssh heslo|Zadejte heslo.|

    Některé vlastnosti jsou v šabloně pevně kódované.  Takové hodnoty můžete konfigurovat v šabloně. Další vysvětlení těchto vlastností naleznete v [tématu Vytvoření clusterů Apache Hadoop v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

    > [!NOTE]  
    > Zadané hodnoty musí být jedinečné a měly by splňovat pokyny pro pojmenování. Šablona neprovádí ověřovací kontroly. Pokud se zadané hodnoty již používají nebo pokud nesplňují příslušné pokyny, po odeslání šablony se zobrazí chyba.  

    ![HDInsight Linux začíná Resource Manager šablony na portálu](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Nasazení clusteru Hadoop ve službě HDInsight pomocí portálu Azure a šablony správce skupiny prostředků")

1. Přečtěte si **podmínky**. Pak vyberte **Souhlasím s podmínkami uvedenými výše**, pak **nákup**. Obdržíte oznámení, že probíhá nasazení. Vytvoření clusteru trvá přibližně 20 minut.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Po vytvoření clusteru obdržíte oznámení **o úspěšném nasazení** s odkazem **přejít na prostředek.** Na stránce skupiny prostředků bude uveden nový cluster HDInsight a výchozí úložiště přidružené k clusteru. Každý cluster má účet [Azure Storage](../hdinsight-hadoop-use-blob-storage.md) nebo závislost na účtu Azure Data [Lake Storage.](../hdinsight-hadoop-use-data-lake-store.md) Označuje se jako výchozí účet úložiště. Cluster HDInsight a jeho výchozí účet úložiště musí být společně umístěny ve stejné oblasti Azure. Odstraněním clusterů účet úložiště neodstraníte.

> [!NOTE]  
> Další metody vytváření clusteru a pochopení vlastností použitých v tomto rychlém startu naleznete v [tématu Vytvoření clusterů HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu můžete cluster odstranit. S HDInsight, vaše data jsou uloženy ve službě Azure Storage, takže můžete bezpečně odstranit clusteru, když není v provozu. Účtuje se vám také cluster HDInsight, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster jsou mnohonásobně vyšší než poplatky za úložiště, má ekonomické smysl odstranit clustery, když nejsou používány.

> [!NOTE]  
> Pokud *rovnou* pokračujete k dalšímu kurzu, ve kterém se dozvíte, jak spouštět operace ETL s využitím Hadoopu ve službě HDInsight, můžete cluster nechat spuštěný. Je to proto, že v kurzu budete muset znovu vytvořit cluster Hadoop. Pokud ale nebudete hned pokračovat dalším kurzem, musíte teď cluster odstranit.

Na webu Azure navigujte do svého clusteru a vyberte **Odstranit**.

![HDInsight odstranit cluster z portálu](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "HDInsight odstranit cluster z portálu")

Můžete také výběrem názvu skupiny prostředků otevřít stránku skupiny prostředků a pak vybrat **Odstranit skupinu prostředků**. Odstraněním skupiny prostředků odstraníte cluster HDInsight i výchozí účet úložiště.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit cluster Apache Hadoop v HDInsight pomocí šablony Správce prostředků. V dalším článku se dozvíte, jak pomocí Hadoopu ve službě HDInsight provést operaci ETL (extrakce, transformace a načítání).

> [!div class="nextstepaction"]
> [Extrahování, transformace a načítání dat pomocí interaktivního dotazu na hdinsightu](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
