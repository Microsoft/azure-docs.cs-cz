---
title: 'Rychlý Start: Vytvoření clusteru interaktivních dotazů pomocí šablony – Azure HDInsight'
description: V tomto rychlém startu se dozvíte, jak pomocí šablony Správce prostředků vytvořit interaktivní cluster dotazů ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: 969aadbe342dbfd345930082d56e0af93eea37d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91854371"
---
# <a name="quickstart-create-interactive-query-cluster-in-azure-hdinsight-using-arm-template"></a>Rychlý Start: Vytvoření clusteru interaktivních dotazů ve službě Azure HDInsight pomocí šablony ARM

V tomto rychlém startu použijete šablonu Azure Resource Manager (šablona ARM) k vytvoření clusteru [interaktivních dotazů](./apache-interactive-query-get-started.md) v Azure HDInsight. Interaktivní dotaz (označovaný také jako Apache Hive LLAP nebo [Analytical Processing s nízkou latencí](https://cwiki.apache.org/confluence/display/Hive/LLAP)) je [typ clusteru](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type)Azure HDInsight.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-interactive-hive/).

:::code language="json" source="~/quickstart-templates/101-hdinsight-interactive-hive/azuredeploy.json":::

V šabloně jsou definované dva prostředky Azure:

* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): vytvořte účet Azure Storage.
* [Microsoft. HDInsight/cluster](/azure/templates/microsoft.hdinsight/clusters): Vytvořte cluster HDInsight.

### <a name="deploy-the-template"></a>Nasazení šablony

1. Kliknutím na tlačítko **nasadit do Azure** níže se přihlaste k Azure a otevřete šablonu ARM.

    [![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json)

1. Zadejte nebo vyberte tyto hodnoty:

    |Vlastnost |Popis |
    |---|---|
    |Předplatné|V rozevíracím seznamu vyberte předplatné Azure, které se používá pro cluster.|
    |Skupina prostředků|V rozevíracím seznamu vyberte existující skupinu prostředků nebo vyberte **vytvořit novou**.|
    |Umístění|Hodnota bude automaticky vyplněna umístěním použitým pro skupinu prostředků.|
    |Název clusteru|Zadejte globálně jedinečný název. Pro tuto šablonu použijte jenom malá písmena a čísla.|
    |Uživatelské jméno přihlášení clusteru|Zadejte uživatelské jméno, výchozí nastavení je **admin**.|
    |Heslo přihlášení clusteru|Zadejte heslo. Heslo musí mít minimálně 10 znaků a musí obsahovat aspoň jedno číslo, jedno velké písmeno a jedno malé písmeno, jeden jiný než alfanumerický znak (kromě znaků). |
    |Uživatelské jméno SSH|Zadejte uživatelské jméno, výchozí hodnota je sshuser|
    |Heslo SSH|Zadejte heslo.|

    ![Nasazení Správce prostředků šablon HBA](./media/quickstart-resource-manager-template/resource-manager-template-hive.png)

1. Přečtěte si podmínky **a ujednání**. Pak vyberte Souhlasím **s výše uvedenými podmínkami a ujednáními a**pak na **koupit**. Obdržíte oznámení, že vaše nasazení probíhá. Vytvoření clusteru trvá přibližně 20 minut.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Po vytvoření clusteru obdržíte oznámení o **úspěšném nasazení** s odkazem **na prostředek přejít na prostředek** . Na stránce skupiny prostředků se zobrazí seznam nového clusteru HDInsight a výchozí úložiště přidružené ke clusteru. Každý cluster má účet [Azure Blob Storage](../hdinsight-hadoop-use-blob-storage.md) , [Azure Data Lake Storage Gen1](../hdinsight-hadoop-use-data-lake-storage-gen1.md)nebo  [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) závislost. Označuje se jako výchozí účet úložiště. Cluster HDInsight a jeho výchozí účet úložiště musí být společně umístěné ve stejné oblasti Azure. Odstraněním clusterů nedojde k odstranění účtu úložiště.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu možná budete chtít cluster odstranit. Ve službě HDInsight jsou vaše data uložená v Azure Storage, takže můžete cluster bezpečně odstranit, pokud se nepoužívá. Účtují se vám také poplatky za cluster HDInsight, a to i v případě, že se už nepoužívá. Vzhledem k tomu, že se poplatky za cluster mnohokrát účtují rychleji než poplatky za úložiště, má ekonomický smysl odstraňovat clustery, když se nepoužívají.

Z Azure Portal přejděte do svého clusteru a vyberte **Odstranit**.

[Odstranit Správce prostředků šablon HBA](./media/quickstart-resource-manager-template/azure-portal-delete-hive.png)

Můžete také výběrem názvu skupiny prostředků otevřít stránku skupiny prostředků a pak vybrat **Odstranit skupinu prostředků**. Odstraněním skupiny prostředků odstraníte cluster HDInsight i výchozí účet úložiště.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit interaktivní cluster dotazů v HDInsight pomocí šablony ARM. V dalším článku se naučíte, jak používat Apache Zeppelin ke spouštění dotazů Apache Hive.

> [!div class="nextstepaction"]
> [Spouštění dotazů Apache Hive ve službě Azure HDInsight s Apache Zeppelin](./hdinsight-connect-hive-zeppelin.md)
