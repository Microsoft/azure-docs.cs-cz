---
title: 'Rychlý Start: Vytvoření clusteru Apache Hadoop ve službě Azure HDInsight pomocí šablony Správce prostředků'
description: V tomto rychlém startu vytvoříte cluster Apache Hadoop ve službě Azure HDInsight pomocí šablony Správce prostředků
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: fe1a76406c1a8a55996b4c099e04f0925daaa74c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946524"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-arm-template"></a>Rychlý Start: Vytvoření clusteru Apache Hadoop ve službě Azure HDInsight pomocí šablony ARM

V tomto rychlém startu použijete šablonu Azure Resource Manager (šablona ARM) k vytvoření clusteru [Apache Hadoop](./apache-hadoop-introduction.md) ve službě Azure HDInsight. Hadoop byla původní Open-Source platforma pro distribuované zpracování a analýzu velkých objemů dat v clusterech. Ekosystém Hadoop zahrnuje související software a nástroje, včetně Apache Hive, Apache HBA, Spark, Kafka a mnoha dalších.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]
  
Aktuálně se HDInsight dodává se [sedmi různými typy clusteru](../hdinsight-overview.md#cluster-types-in-hdinsight). Každý typ clusteru podporuje odlišnou sadu komponent. Všechny typy clusteru podporují Hive. Seznam podporovaných součásti v HDInsight naleznete v tématu [Co je nového ve verzích clusterů Hadoop poskytovaných v HDInsight?](../hdinsight-component-versioning.md)  

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/).

:::code language="json" source="~/quickstart-templates/101-hdinsight-linux-ssh-password/azuredeploy.json":::

V šabloně jsou definované dva prostředky Azure:

* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): vytvořte účet Azure Storage.
* [Microsoft. HDInsight/cluster](/azure/templates/microsoft.hdinsight/clusters): Vytvořte cluster HDInsight.

## <a name="deploy-the-template"></a>Nasazení šablony

1. Kliknutím na tlačítko **nasadit do Azure** níže se přihlaste k Azure a otevřete šablonu ARM.

    [![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json)

1. Zadejte nebo vyberte tyto hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Předplatné|V rozevíracím seznamu vyberte předplatné Azure, které se používá pro cluster.|
    |Skupina prostředků|V rozevíracím seznamu vyberte existující skupinu prostředků nebo vyberte **vytvořit novou**.|
    |Umístění|Hodnota bude automaticky vyplněna umístěním použitým pro skupinu prostředků.|
    |Název clusteru|Zadejte globálně jedinečný název. Pro tuto šablonu použijte jenom malá písmena a čísla.|
    |Typ clusteru | Vyberte **hadoop**. |
    |Uživatelské jméno přihlášení clusteru|Zadejte uživatelské jméno, výchozí nastavení je **admin**.|
    |Heslo přihlášení clusteru|Zadejte heslo. Heslo musí mít minimálně 10 znaků a musí obsahovat aspoň jedno číslo, jedno velké písmeno a jedno malé písmeno, jeden jiný než alfanumerický znak (kromě znaků). |
    |Uživatelské jméno SSH|Zadejte uživatelské jméno, výchozí hodnota je **sshuser**|
    |Heslo SSH|Zadejte heslo.|

    Některé vlastnosti jsou v šabloně pevně kódované.  Takové hodnoty můžete konfigurovat v šabloně. Další vysvětlení těchto vlastností najdete v tématu [Vytvoření clusterů Apache Hadoop ve službě HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

    > [!NOTE]  
    > Zadané hodnoty musí být jedinečné a měly by splňovat pokyny pro pojmenování. Šablona neprovádí ověřovací kontroly. Pokud se zadané hodnoty již používají nebo pokud nesplňují příslušné pokyny, po odeslání šablony se zobrazí chyba.  

    ![Začínáme s Správce prostředků šablonou na portálu HDInsight Linux](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Nasazení clusteru Hadoop ve službě HDInsight pomocí Azure Portal a šablony správce skupin prostředků")

1. Přečtěte si podmínky **a ujednání**. Pak vyberte Souhlasím **s výše uvedenými podmínkami a ujednáními a** pak na **koupit**. Obdržíte oznámení, že vaše nasazení probíhá. Vytvoření clusteru trvá přibližně 20 minut.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Po vytvoření clusteru obdržíte oznámení o **úspěšném nasazení** s odkazem **na prostředek přejít na prostředek** . Na stránce skupiny prostředků se zobrazí seznam nového clusteru HDInsight a výchozí úložiště přidružené ke clusteru. Každý cluster má účet [Azure Blob Storage](../hdinsight-hadoop-use-blob-storage.md) , [Azure Data Lake Storage Gen1](../hdinsight-hadoop-use-data-lake-storage-gen1.md)nebo  [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) závislost. Označuje se jako výchozí účet úložiště. Cluster HDInsight a jeho výchozí účet úložiště musí být společně umístěné ve stejné oblasti Azure. Odstraněním clusterů nedojde k odstranění účtu úložiště.

> [!NOTE]  
> Další metody vytváření clusterů a porozumění vlastnostem používaným v tomto rychlém startu najdete v tématu [Vytvoření clusterů HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu možná budete chtít cluster odstranit. Ve službě HDInsight jsou vaše data uložená v Azure Storage, takže můžete cluster bezpečně odstranit, pokud se nepoužívá. Účtují se vám také poplatky za cluster HDInsight, a to i v případě, že se už nepoužívá. Vzhledem k tomu, že se poplatky za cluster mnohokrát účtují rychleji než poplatky za úložiště, má ekonomický smysl odstraňovat clustery, když se nepoužívají.

> [!NOTE]  
> Pokud *rovnou* pokračujete k dalšímu kurzu, ve kterém se dozvíte, jak spouštět operace ETL s využitím Hadoopu ve službě HDInsight, můžete cluster nechat spuštěný. Důvodem je to, že v tomto kurzu musíte cluster Hadoop vytvořit znovu. Pokud ale nebudete hned pokračovat dalším kurzem, musíte teď cluster odstranit.

Z Azure Portal přejděte do svého clusteru a vyberte **Odstranit**.

![Služba HDInsight odstranit cluster z portálu](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "Služba HDInsight odstranit cluster z portálu")

Můžete také výběrem názvu skupiny prostředků otevřít stránku skupiny prostředků a pak vybrat **Odstranit skupinu prostředků**. Odstraněním skupiny prostředků odstraníte cluster HDInsight i výchozí účet úložiště.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit cluster Apache Hadoop v HDInsight pomocí šablony ARM. V dalším článku se dozvíte, jak pomocí Hadoopu ve službě HDInsight provést operaci ETL (extrakce, transformace a načítání).

> [!div class="nextstepaction"]
> [Extrakce, transformace a načtení dat pomocí interaktivního dotazu ve službě HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
