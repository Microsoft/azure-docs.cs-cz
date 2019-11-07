---
title: Spuštění úlohy Sparku na Azure Databricks pomocí Azure Portal
description: Rychlý start ukazuje, jak pomocí portálu Azure vytvořit pracovní prostor služby Azure Databricks a cluster Apache Spark a spustit úlohu Spark.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: 16de2054864a42d978d149353a89d65e8de7d155
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605686"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>Rychlý start: Spuštění úlohy Spark job v Azure Databricks pomocí portálu Azure

V tomto rychlém startu použijete Azure Portal k vytvoření pracovního prostoru Azure Databricks s Apache Sparkm clusterem. Spouštíte úlohu v clusteru a použijete vlastní grafy k vytváření sestav v reálném čase z dat o zabezpečení Boston.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlásit se na [Azure Portal](https://portal.azure.com).

> [!Note]
> Tento kurz se nedá provést pomocí **předplatného Azure free zkušební verze**.
> Pokud máte bezplatný účet, přejděte na svůj profil a změňte si předplatné na **průběžné platby**. Další informace najdete na stránce [bezplatného účtu Azure](https://azure.microsoft.com/free/). Pak [odeberte limit útraty](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)a [požádejte o zvýšení kvóty](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) pro vCPU ve vaší oblasti. Když vytváříte pracovní prostor Azure Databricks, můžete vybrat cenovou úroveň **DBU (Premium-14-days)** a poskytnout tak přístup k pracovnímu prostoru zdarma Premium Azure Databricks DBU po dobu 14 dnů.

## <a name="create-an-azure-databricks-workspace"></a>Vytvoření pracovního prostoru Azure Databricks

V této části vytvoříte pomocí portálu Azure pracovní prostor služby Azure Databricks.

1. Na webu Azure Portal vyberte **Vytvořit prostředek** > **Analýza** > **Azure Databricks**.

    ![Datacihly na Azure Portal](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Datacihly na Azure Portal")

2. V části **Služba Azure Databricks** zadejte hodnoty pro vytvoření pracovního prostoru Databricks.

    ![Vytvoření pracovního prostoru Azure Databricks](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "Vytvoření pracovního prostoru Azure Databricks")

    Zadejte následující hodnoty:
    
    |Vlastnost  |Popis  |
    |---------|---------|
    |**Název pracovního prostoru**     | Zadejte název pracovního prostoru Databricks.        |
    |**Předplatné**     | Z rozevíracího seznamu vyberte své předplatné Azure.        |
    |**Skupina prostředků**     | Určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Další informace naleznete v tématu [Přehled skupin prostředků v Azure](../azure-resource-manager/resource-group-overview.md). |
    |**Umístění**     | Vyberte **Západní USA 2**. Další dostupné oblasti najdete v tématu [Dostupné služby Azure podle oblastí](https://azure.microsoft.com/regions/services/).        |
    |**Cenová úroveň**     |  Vyberte si mezi **standardem**, **Premium**nebo **zkušební verzí**. Další informace o těchto úrovních najdete na [stránce s cenami za Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |
    |**Virtual Network**     |  Vyberte, pokud chcete nasadit Azure Databricks pracovní prostor ve vašem vlastním Virtual Network (VNet). Další informace najdete v tématu [nasazení Azure Databricks ve službě Azure Virtual Network (vkládání virtuální sítě)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).        |

    Vyberte **Vytvořit**.

4. Vytvoření pracovního prostoru trvá několik minut. Při vytváření pracovního prostoru můžete zobrazit stav nasazení v části **oznámení**.

    ![Dlaždice nasazení datacihly](./media/quickstart-create-databricks-workspace-portal/databricks-deployment-tile.png "Dlaždice nasazení datacihly")

## <a name="create-a-spark-cluster-in-databricks"></a>Vytvoření clusteru Spark ve službě Databricks

> [!NOTE]
> Pokud chcete k vytvoření clusteru Azure Databricks použít bezplatný účet, přejděte na svůj profil a změňte své předplatné na **Průběžné platby**. Další informace najdete na stránce [bezplatného účtu Azure](https://azure.microsoft.com/free/).

1. Na webu Azure Portal přejděte do pracovního prostoru Databricks, který jste vytvořili, a klikněte na **Spustit pracovní prostor**.

2. Budete přesměrováni na portál Azure Databricks. Na portálu klikněte na **nový cluster**.

    ![Datacihly v Azure](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Datacihly v Azure")

3. Na stránce **New cluster** (Nový cluster) zadejte hodnoty pro vytvoření clusteru.

    ![Vytvoření clusteru datacihly Spark v Azure](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Vytvoření clusteru datacihly Spark v Azure")

    Přijměte všechny výchozí hodnoty kromě následujících:

   * Zadejte název clusteru.
   * V tomto článku vytvořte cluster s modulem runtime **5,3** .
   * Nezapomeňte zaškrtnout políčko **Terminate after \_\_ minutes of inactivity** (Ukončit po __ minutách neaktivity). Zadejte dobu (v minutách), po které se má ukončit činnost clusteru, pokud se cluster nepoužívá.
    
     Vyberte **Vytvořit cluster**. Po spuštění clusteru můžete ke clusteru připojit poznámkové bloky a spouštět úlohy Spark.

Další informace o vytváření clusterů najdete v tématu [Vytvoření clusteru Spark v Azure Databricks](/azure/databricks/clusters/create).

## <a name="run-a-spark-sql-job"></a>Spuštění úlohy Spark SQL

Pomocí následujících kroků vytvořte v datacihlách Poznámkový blok, nakonfigurujte si Poznámkový blok pro čtení dat z otevřených datových sad Azure a pak na těchto datech spusťte úlohu Spark SQL.

1. V levém podokně vyberte **Azure Databricks**. V části **běžné úlohy**vyberte **Nový Poznámkový blok**.

    ![Vytvoření poznámkového bloku v datacihlech](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Vytvoření poznámkového bloku v datacihlech")

2. V dialogovém okně **vytvořit Poznámkový blok** zadejte název, vyberte **Python** jako jazyk a vyberte cluster Spark, který jste vytvořili dříve.

    ![Vytvoření poznámkového bloku v datacihlech](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Vytvoření poznámkového bloku v datacihlech")

    Vyberte **Vytvořit**.

3. V tomto kroku vytvoříte datový rámec Spark dataframe s daty o zabezpečení Boston z [Azure Open DataSets](https://azure.microsoft.com/services/open-datasets/catalog/boston-safety-data/#AzureDatabricks)a použijete SQL k dotazování dat.

   Následující příkaz nastaví informace o přístupu k Azure Storage. Vložte tento kód PySpark do první buňky a stisknutím **SHIFT + ENTER** kód spusťte.

   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "citydatacontainer"
   blob_relative_path = "Safety/Release/city=Boston"
   blob_sas_token = r"?st=2019-02-26T02%3A34%3A32Z&se=2119-02-27T02%3A34%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=XlJVWA7fMXCSxCKqJm8psMOh0W4h7cSYO28coRqF2fs%3D"
   ```

   Následující příkaz umožňuje službě Spark číst z úložiště objektů BLOB vzdáleně. Vložte tento kód PySpark do další buňky a stisknutím **SHIFT + ENTER** kód spusťte.

   ```python
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
   print('Remote blob path: ' + wasbs_path)
   ```

   Následující příkaz vytvoří datový rámec. Vložte tento kód PySpark do další buňky a stisknutím **SHIFT + ENTER** kód spusťte.

   ```python
   df = spark.read.parquet(wasbs_path)
   print('Register the DataFrame as a SQL temporary view: source')
   df.createOrReplaceTempView('source')
   ```

4. Spusťte příkaz SQL, který vrátí prvních 10 řádků dat z dočasného zobrazení s názvem **zdroj**. Vložte tento kód PySpark do další buňky a stisknutím **SHIFT + ENTER** kód spusťte.

   ```python
   print('Displaying top 10 rows: ')
   display(spark.sql('SELECT * FROM source LIMIT 10'))
   ```

5. Zobrazí se tabulkový výstup jako na následujícím snímku obrazovky (zobrazí se jenom některé sloupce):

    ![Ukázková data](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "Ukázková data JSON")

6. Teď vytvoříte vizuální reprezentaci těchto dat, abyste viděli, kolik bezpečnostních událostí se nahlásilo pomocí aplikace občané pro připojení aplikace a města pracovního procesu města místo jiných zdrojů. V dolní části tabulkového výstupu vyberte ikonu **pruhového grafu** a potom klikněte na **Možnosti grafu**.

    ![Vytvořit pruhový graf](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "Vytvořit pruhový graf")

8. V části **Customize Plot** (Přizpůsobit graf) přetáhněte hodnoty, jak ukazuje snímek obrazovky.

    ![Přizpůsobení výsečového grafu](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "Přizpůsobení pruhového grafu")

   * Nastavte **klíče** na **zdroj**.
   * Nastavte **hodnoty** na **< \id >** .
   * V poli **Aggregation** (Agregace) vyberte možnost **COUNT** (Počet).
   * Nastavte **typ zobrazení** na **výsečový graf**.

     Klikněte na **Použít**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto článku můžete cluster ukončit. Pokud to chcete udělat, v levém podokně v pracovním prostoru Azure Databricks vyberte **Clusters** (Clustery). U clusteru, který chcete ukončit, přesuňte kurzor na tři tečky pod sloupcem **Actions** (Akce) a vyberte ikonu **Terminate** (Ukončit).

![Zastavení clusteru datacihly](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Zastavení clusteru datacihly")

Pokud cluster neukončíte ručně, zastaví se automaticky a za předpokladu, že jste při vytváření clusteru zaškrtli políčko **ukončit po \_\_ minut při nečinnosti** . V takovém případě se cluster automaticky zastaví, pokud byl po stanovenou dobu neaktivní.

## <a name="next-steps"></a>Další kroky

V tomto článku jste v Azure Databricks vytvořili cluster Spark a spustili jste úlohu Sparku s využitím dat z Azure Open DataSet. Můžete si také projít článek [Zdroje dat Spark](/azure/databricks/data/data-sources/index.html) a zjistit, jak do Azure Databricks importovat data z jiných zdrojů dat. V dalším článku se dozvíte, jak pomocí Azure Databricks provést operaci ETL (extrakce, transformace a načítání dat).

> [!div class="nextstepaction"]
>[Extrakce, transformace a načítání dat pomocí Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
