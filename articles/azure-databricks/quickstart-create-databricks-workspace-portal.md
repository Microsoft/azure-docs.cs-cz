---
title: Spuštění úlohy Sparku na Azure Databricks pomocí Azure Portal
description: V tomto rychlém startu se dozvíte, jak použít Azure Portal k vytvoření pracovního prostoru Azure Databricks, Apache Sparkho clusteru a spuštění úlohy Sparku.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: 1cc6d6236b940cf898651bdfdd47f4db1ac53851
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274165"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>Rychlý Start: spuštění úlohy Sparku na Azure Databricks pomocí Azure Portal

V tomto rychlém startu použijete Azure Portal k vytvoření pracovního prostoru Azure Databricks s Apache Sparkm clusterem. Spouštíte úlohu v clusteru a použijete vlastní grafy k vytváření sestav v reálném čase z dat o zabezpečení Boston.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .

## <a name="sign-in-to-the-azure-portal"></a>Přihlaste se k Azure Portal

Přihlaste se k [Azure Portal](https://portal.azure.com).

> [!Note]
> Tento kurz se nedá provést pomocí **předplatného Azure free zkušební verze**.
> Pokud máte bezplatný účet, přejděte na svůj profil a změňte si předplatné na **průběžné platby**. Další informace najdete v tématu [bezplatný účet Azure](https://azure.microsoft.com/free/). Pak [odeberte limit útraty](https://docs.microsoft.com/azure/billing/billing-spending-limit#remove-the-spending-limit-in-account-center)a [požádejte o zvýšení kvóty](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) pro vCPU ve vaší oblasti. Když vytváříte pracovní prostor Azure Databricks, můžete vybrat cenovou úroveň **DBU (Premium-14-days)** a poskytnout tak přístup k pracovnímu prostoru zdarma Premium Azure Databricks DBU po dobu 14 dnů.

## <a name="create-an-azure-databricks-workspace"></a>Vytvoření pracovního prostoru Azure Databricks

V této části vytvoříte Azure Databricks pracovní prostor pomocí Azure Portal.

1. V Azure Portal vyberte **vytvořit prostředek** > **Analytics** > **Azure Databricks**.

    ![Datacihly na Azure Portal](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "datacihly na Azure Portal")

2. V části **služba Azure Databricks**zadejte hodnoty pro vytvoření pracovního prostoru datacihly.

    ![Vytvoření pracovního prostoru Azure Databricks](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "vytvoření pracovního prostoru Azure Databricks")

    Zadejte následující hodnoty:
    
    |Vlastnost  |Popis  |
    |---------|---------|
    |**Název pracovního prostoru**     | Zadejte název pracovního prostoru datacihly.        |
    |**Formě**     | V rozevíracím seznamu vyberte své předplatné Azure.        |
    |**Skupina prostředků**     | Určete, zda chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Další informace najdete v tématu [Přehled skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md). |
    |**Poloha**     | Vyberte **západní USA 2**. Další dostupné oblasti najdete v tématu [služby Azure dostupné v jednotlivých oblastech](https://azure.microsoft.com/regions/services/).        |
    |**Cenová úroveň**     |  Vyberte si mezi **standardem**, **Premium**nebo **zkušební verzí**. Další informace o těchto úrovních najdete na stránce s [cenami pro datacihly](https://azure.microsoft.com/pricing/details/databricks/).       |

    Vyberte **Připnout na řídicí panel** a potom klikněte na **vytvořit**.

4. Vytváření pracovního prostoru trvá několik minut. Při vytváření pracovního prostoru můžete zobrazit stav nasazení v části **oznámení**.

    Dlaždice nasazení datacihly ![dlaždice](./media/quickstart-create-databricks-workspace-portal/databricks-deployment-tile.png "nasazení datacihly")

## <a name="create-a-spark-cluster-in-databricks"></a>Vytvoření clusteru Spark v datacihlách

> [!NOTE]
> Pokud chcete před vytvořením clusteru použít bezplatný Azure Databricks účet, přejděte do svého profilu a změňte předplatné na **průběžné platby**. Další informace najdete v tématu [bezplatný účet Azure](https://azure.microsoft.com/free/).

1. V Azure Portal přejděte do pracovního prostoru datacihly, který jste vytvořili, a pak klikněte na **Spustit pracovní prostor**.

2. Budete přesměrováni na portál Azure Databricks. Na portálu klikněte na **nový cluster**.

    ![Datacihly v Azure](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "datacihlách v Azure")

3. Na stránce **nový cluster** zadejte hodnoty pro vytvoření clusteru.

    ![Vytvoření clusteru datacihly Spark v Azure](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Vytvoření clusteru datacihly Spark v Azure")

    Přijměte všechny ostatní výchozí hodnoty kromě následujících:

   * Zadejte název clusteru.
   * V tomto článku vytvořte cluster s modulem runtime **5,2** .
   * Ujistěte se, že jste zaškrtli políčko **ukončit po \_ @ no__t – 2 minuty nečinnosti** . Zadejte dobu (v minutách), po kterou se má cluster ukončit, pokud se cluster nepoužívá.
    
     Vyberte **vytvořit cluster**. Po spuštění clusteru můžete ke clusteru připojit poznámkové bloky a spouštět úlohy Spark.

Další informace o vytváření clusterů najdete [v tématu Vytvoření clusteru Spark v Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

## <a name="run-a-spark-sql-job"></a>Spuštění úlohy Spark SQL

Pomocí následujících kroků vytvořte v datacihlách Poznámkový blok, nakonfigurujte si Poznámkový blok pro čtení dat z otevřených datových sad Azure a pak na těchto datech spusťte úlohu Spark SQL.

1. V levém podokně vyberte **Azure Databricks**. V části **běžné úlohy**vyberte **Nový Poznámkový blok**.

    ![Vytvoření poznámkového bloku v datacihlách](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "vytvoření poznámkového bloku v datacihlech")

2. V dialogovém okně **vytvořit Poznámkový blok** zadejte název, vyberte **Python** jako jazyk a vyberte cluster Spark, který jste vytvořili dříve.

    ![Vytvoření poznámkového bloku v datacihlách](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "vytvoření poznámkového bloku v datacihlech")

    Vyberte **vytvořit**.

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

5. Zobrazí se tabulkový výstup podobný následujícímu snímku obrazovky (zobrazují se pouze některé sloupce):

    ![Ukázková]data –(./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "UKÁZKOVá data JSON")

6. Teď vytvoříte vizuální reprezentaci těchto dat, abyste viděli, kolik bezpečnostních událostí se nahlásilo pomocí aplikace občané pro připojení aplikace a města pracovního procesu města místo jiných zdrojů. V dolní části tabulkového výstupu vyberte ikonu **pruhového grafu** a potom klikněte na **Možnosti grafu**.

    Vytvořit pruhový(./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "graf") ![Vytvoření pruhového grafu]

8. V části **přizpůsobit vykreslení**přetáhněte hodnoty, jak je znázorněno na snímku obrazovky.

    Přizpůsobení(./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "pruhového") grafu přizpůsobení ![výsečového grafu]

   * Nastavte **klíče** na **zdroj**.
   * Nastavte **hodnoty** na **< \id >** .
   * Nastavte **agregaci** na **Count**.
   * Nastavte **typ zobrazení** na **výsečový graf**.

     Klikněte na **použít**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto článku můžete cluster ukončit. Provedete to tak, že z pracovního prostoru Azure Databricks v levém podokně vyberete **clustery**. U clusteru, který chcete ukončit, přesuňte kurzor na tři tečky pod sloupcem **Actions (akce** ) a vyberte ikonu **ukončit** .

![Zastavení clusteru datacihly](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "zastavení clusteru datacihly")

Pokud cluster neukončíte ručně, zastaví se automaticky a za předpokladu, že jste při vytváření clusteru zaškrtli políčko **ukončit po \_ @ no__t – 2 minuty nečinnosti** . V takovém případě se cluster automaticky zastaví, pokud byl po určenou dobu neaktivní.

## <a name="next-steps"></a>Další kroky

V tomto článku jste v Azure Databricks vytvořili cluster Spark a spustili jste úlohu Sparku s využitím dat z Azure Open DataSet. Můžete se také podívat na [zdroje dat Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) a Naučte se, jak importovat data z jiných zdrojů dat do Azure Databricks. V dalším článku se dozvíte, jak provést operaci ETL (extrakce, transformace a načítání dat) pomocí Azure Databricks.

> [!div class="nextstepaction"]
>[Extrakce, transformace a načtení dat pomocí Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
