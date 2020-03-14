---
title: 'Rychlý Start: Apache Hadoop & Správce prostředků – Azure HDInsight'
description: V tomto rychlém startu vytvoříte cluster Apache Hadoop ve službě Azure HDInsight pomocí šablony Správce prostředků
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 03/11/2020
ms.openlocfilehash: d3d8b749841e2c58b0999b92e942a79f4e3170ca
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370878"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Rychlý Start: Vytvoření clusteru Apache Hadoop ve službě Azure HDInsight pomocí šablony Správce prostředků

V tomto rychlém startu se dozvíte, jak vytvořit cluster Apache Hadoop v Azure HDInsight pomocí šablony Správce prostředků.

Podobné šablony můžete zobrazit v [šablonách rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular). Odkaz na šablonu najdete [tady](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/allversions).  Cluster můžete také vytvořit pomocí [Azure Portal](apache-hadoop-linux-create-cluster-get-started-portal.md).  

Aktuálně se HDInsight dodává se [sedmi různými typy clusteru](../hdinsight-overview.md#cluster-types-in-hdinsight). Každý typ clusteru podporuje odlišnou sadu komponent. Všechny typy clusteru podporují Hive. Seznam podporovaných součásti v HDInsight najdete v tématu [Co je nového ve verzích clusterů Hadoop poskytovaných v HDInsight?](../hdinsight-component-versioning.md)  

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-hadoop-cluster"></a>Vytvoření clusteru Hadoop

1. Kliknutím na tlačítko **nasadit do Azure** níže se přihlaste k Azure a otevřete šablonu Správce prostředků v Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Zadejte nebo vyberte tyto hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Předplatné|  Vyberte své předplatné Azure. |
    |Skupina prostředků | Vytvořte skupinu prostředků nebo vyberte existující.  Skupina prostředků je kontejner komponent Azure.  V tomto případě skupina prostředků obsahuje cluster HDInsight a závislý účet služby Azure Storage. |
    |Umístění | Vyberte umístění Azure, ve kterém chcete cluster vytvořit.  Pro dosažení lepšího výkonu zvolte co nejbližší umístění. |
    |Název clusteru | Zadejte název clusteru Hadoop. Vzhledem k tomu, že všechny clustery ve službě HDInsight sdílejí stejný obor názvů DNS, musí být tento název jedinečný. Název může obsahovat jenom malá písmena, číslice a spojovníky a musí začínat písmenem.  Před i za každým spojovníkem musí být jiný znak než spojovník.  Název musí mít také délku 3 až 59 znaků. |
    |Typ clusteru | Vyberte **hadoop**. |
    |Přihlašovací jméno a heslo clusteru | Výchozí přihlašovací jméno je **admin**. Heslo musí mít minimálně 10 znaků a musí obsahovat aspoň jedno číslo, jedno velké písmeno a jedno malé písmeno, jeden nealfanumerický znak (kromě znaků ' "' ' \). **Nezadávejte** běžné heslo, jako je „Pass@word1“.|
    |Uživatelské jméno a heslo SSH| Výchozí uživatelské jméno je **sshuser** (uživatelssh).  Uživatelské jméno SSH můžete změnit.  Pro heslo uživatele SSH platí stejné požadavky jako pro přihlašovací heslo clusteru.|

    Některé vlastnosti jsou v šabloně pevně kódované.  Takové hodnoty můžete konfigurovat v šabloně. Další vysvětlení těchto vlastností najdete v tématu [Vytvoření clusterů Apache Hadoop ve službě HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

    > [!NOTE]  
    > Zadané hodnoty musí být jedinečné a měly by splňovat pokyny pro pojmenování. Šablona neprovádí ověřovací kontroly. Pokud se zadané hodnoty již používají nebo pokud nesplňují příslušné pokyny, po odeslání šablony se zobrazí chyba.  

    ![Začínáme s Správce prostředků šablonou na portálu HDInsight Linux](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Nasazení clusteru Hadoop ve službě HDInsight pomocí Azure Portal a šablony správce skupin prostředků")

3. Vyberte Souhlasím **s podmínkami a ujednáními uvedenými nahoře**a pak vyberte **koupit**. Obdržíte oznámení, že vaše nasazení probíhá.  Vytvoření clusteru trvá přibližně 20 minut.

4. Po vytvoření clusteru obdržíte oznámení o **úspěšném nasazení** s odkazem **na skupinu prostředků** .  Na stránce **skupiny prostředků** se zobrazí seznam nového clusteru HDInsight a výchozí úložiště přidružené ke clusteru. Každý cluster má [účet Azure Storage](../hdinsight-hadoop-use-blob-storage.md) nebo závislost [Azure Data Lake Storageho účtu](../hdinsight-hadoop-use-data-lake-store.md) . Označuje se jako výchozí účet úložiště. Cluster HDInsight a jeho výchozí účet úložiště musí být společně umístěné ve stejné oblasti Azure. Odstraněním clusterů nedojde k odstranění účtu úložiště.

> [!NOTE]  
> Další metody vytváření clusterů a porozumění vlastnostem používaným v tomto rychlém startu najdete v tématu [Vytvoření clusterů HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu možná budete chtít cluster odstranit. Ve službě HDInsight jsou vaše data uložená v Azure Storage, takže můžete cluster bezpečně odstranit, pokud se nepoužívá. Účtují se vám také poplatky za cluster HDInsight, a to i v případě, že se už nepoužívá. Vzhledem k tomu, že se poplatky za cluster mnohokrát účtují rychleji než poplatky za úložiště, má ekonomický smysl odstraňovat clustery, když se nepoužívají.

> [!NOTE]  
> Pokud *rovnou* pokračujete k dalšímu kurzu, ve kterém se dozvíte, jak spouštět operace ETL s využitím Hadoopu ve službě HDInsight, můžete cluster nechat spuštěný. Důvodem je to, že v tomto kurzu musíte cluster Hadoop vytvořit znovu. Pokud ale nebudete hned pokračovat dalším kurzem, musíte teď cluster odstranit.

Postup odstranění clusteru a/nebo výchozího účtu úložiště:

1. Vraťte se na kartu prohlížeče s webem Azure Portal. Měli byste být na stránce s přehledem clusteru. Pokud chcete odstranit jenom cluster, ale zachovat výchozí účet úložiště, vyberte **Odstranit**.

    ![Služba HDInsight odstranit cluster z portálu](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "Služba HDInsight odstranit cluster z portálu")

2. Pokud chcete odstranit cluster i výchozí účet úložiště, vyberte název skupiny prostředků (zvýrazněný na předchozím snímku obrazovky) a otevřete stránku skupiny prostředků.

3. Vyberte **Odstranit skupinu prostředků** a odstraňte skupinu prostředků obsahující cluster a výchozí účet úložiště. Upozorňujeme, že odstraněním skupiny prostředků odstraníte účet úložiště. Pokud chcete zachovat účet úložiště, zvolte odstranění samotného clusteru.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit cluster Apache Hadoop v HDInsight pomocí Správce prostředků šablony. V dalším článku se dozvíte, jak pomocí Hadoopu ve službě HDInsight provést operaci ETL (extrakce, transformace a načítání).

> [!div class="nextstepaction"]
> [Extrakce, transformace a načtení dat pomocí interaktivního dotazu ve službě HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)