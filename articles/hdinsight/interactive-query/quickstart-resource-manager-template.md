---
title: 'Úvodní příručka: Vytvoření clusteru interaktivních dotazů pomocí šablony – Azure HDInsight'
description: Tento rychlý start ukazuje, jak pomocí šablony Správce prostředků vytvořit cluster interaktivních dotazů v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: 02dbb129fa32ce72a94a098c56b0a81186783ffd
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79485815"
---
# <a name="quickstart-create-interactive-query-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Úvodní příručka: Vytvoření clusteru interaktivních dotazů v Azure HDInsight pomocí šablony Správce prostředků

V tomto rychlém startu použijete šablonu Azure Resource Manager k vytvoření clusteru [interaktivních dotazů](./apache-interactive-query-get-started.md) v Azure HDInsight. Interaktivní dotaz (nazývaný také Apache Hive LLAP nebo [analytické zpracování s nízkou latencí)](https://cwiki.apache.org/confluence/display/Hive/LLAP)je typ [clusteru](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type)Azure HDInsight .

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="create-an-interactive-query-cluster"></a>Vytvoření clusteru interaktivních dotazů

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure QuickStart](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-interactive-hive).

:::code language="json" source="~/quickstart-templates/101-hdinsight-interactive-hive/azuredeploy.json" range="1-158":::


V šabloně jsou definovány dva prostředky Azure:

* [Microsoft.Storage/storageAccounts:](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts)vytvořte účet úložiště Azure.
* [Microsoft.HDInsight/cluster:](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters)vytvořte cluster HDInsight.

### <a name="deploy-the-template"></a>Nasazení šablony

1. Kliknutím na tlačítko **Nasadit do Azure** se přihlaste do Azure a otevřete šablonu Správce prostředků.

    [![Nasazení do Azure](./media/quickstart-resource-manager-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json)

1. Zadejte nebo vyberte tyto hodnoty:

    |Vlastnost |Popis |
    |---|---|
    |Předplatné|V rozevíracím seznamu vyberte předplatné Azure, které se používá pro cluster.|
    |Skupina prostředků|V rozevíracím seznamu vyberte existující skupinu prostředků nebo vyberte **Vytvořit nový**.|
    |Umístění|Hodnota se automaticky naplní umístěním použitým pro skupinu prostředků.|
    |Název clusteru|Zadejte globálně jedinečný název. Pro tuto šablonu používejte pouze malá písmena a čísla.|
    |Uživatelské jméno přihlášení clusteru|Zadejte uživatelské jméno, výchozí je **admin**.|
    |Heslo přihlášení clusteru|Zadejte heslo. Heslo musí mít délku alespoň 10 znaků a musí obsahovat alespoň jednu číslici, jedno velké a jedno malé písmeno, jeden nealfanumerický znak (kromě znaků " " ). |
    |Uživatelské jméno SSH|Zadejte uživatelské jméno, výchozí je sshuser|
    |Ssh heslo|Zadejte heslo.|

    ![Šablona Správce prostředků HBase](./media/quickstart-resource-manager-template/resource-manager-template-hive.png)

1. Přečtěte si **podmínky**. Pak vyberte **Souhlasím s podmínkami uvedenými výše**, pak **nákup**. Obdržíte oznámení, že probíhá nasazení. Vytvoření clusteru trvá přibližně 20 minut.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Po vytvoření clusteru obdržíte oznámení **o úspěšném nasazení** s odkazem **přejít na prostředek.** Na stránce skupiny prostředků bude uveden nový cluster HDInsight a výchozí úložiště přidružené k clusteru. Každý cluster má účet [Azure Storage](../hdinsight-hadoop-use-blob-storage.md) nebo závislost na účtu Azure Data [Lake Storage.](../hdinsight-hadoop-use-data-lake-store.md) Označuje se jako výchozí účet úložiště. Cluster HDInsight a jeho výchozí účet úložiště musí být společně umístěny ve stejné oblasti Azure. Odstraněním clusterů účet úložiště neodstraníte.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu můžete cluster odstranit. S HDInsight, vaše data jsou uloženy ve službě Azure Storage, takže můžete bezpečně odstranit clusteru, když není v provozu. Účtuje se vám také cluster HDInsight, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster jsou mnohonásobně vyšší než poplatky za úložiště, má ekonomické smysl odstranit clustery, když nejsou používány.

Na webu Azure navigujte do svého clusteru a vyberte **Odstranit**.

![Šablona Správce prostředků HBase](./media/quickstart-resource-manager-template/azure-portal-delete-hive.png)

Můžete také výběrem názvu skupiny prostředků otevřít stránku skupiny prostředků a pak vybrat **Odstranit skupinu prostředků**. Odstraněním skupiny prostředků odstraníte cluster HDInsight i výchozí účet úložiště.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit cluster interaktivních dotazů v HDInsightpomocí šablony Správce prostředků. V dalším článku se dozvíte, jak pomocí Apache Zeppelin spustit dotazy Apache Hive.

> [!div class="nextstepaction"]
> [Spouštění dotazů Apache Hive v Azure HDInsight s Apache Zeppelin](./hdinsight-connect-hive-zeppelin.md)
