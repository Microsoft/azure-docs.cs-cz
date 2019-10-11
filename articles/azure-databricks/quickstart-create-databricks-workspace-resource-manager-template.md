---
title: Vytvoření pracovního prostoru Azure Databricks pomocí Azure Resource Manageru
description: V tomto rychlém startu se dozvíte, jak použít šablonu Azure Resource Manager k vytvoření pracovního prostoru Azure Databricks, vytvoření clusteru Apache Spark a spuštění úlohy Sparku.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/12/2019
ms.openlocfilehash: 5f22c9e7b7f040a0e6b957cbc184720918ce9222
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274154"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-resource-manager-template"></a>Rychlý Start: spuštění úlohy Sparku na Azure Databricks pomocí šablony Azure Resource Manager

V tomto rychlém startu použijete šablonu Azure Resource Manager k vytvoření pracovního prostoru Azure Databricks s Apache Sparkm clusterem. Úlohu spustíte v clusteru a pomocí vlastních grafů můžete vytvořit sestavy v reálném čase z bezplatného nebo placeného využití na základě demografických údajů.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .

## <a name="sign-in-to-the-azure-portal"></a>Přihlaste se k Azure Portal

Přihlaste se k [Azure Portal](https://portal.azure.com). 

> [!Note]
> Tento kurz se nedá provést pomocí **předplatného Azure free zkušební verze**.
> Pokud máte bezplatný účet, přejděte na svůj profil a změňte si předplatné na **průběžné platby**. Další informace najdete v tématu [bezplatný účet Azure](https://azure.microsoft.com/free/). Pak [odeberte limit útraty](https://docs.microsoft.com/azure/billing/billing-spending-limit#remove-the-spending-limit-in-account-center)a [požádejte o zvýšení kvóty](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) pro vCPU ve vaší oblasti. Když vytváříte pracovní prostor Azure Databricks, můžete vybrat cenovou úroveň **DBU (Premium-14-days)** a poskytnout tak přístup k pracovnímu prostoru zdarma Premium Azure Databricks DBU po dobu 14 dnů.

## <a name="create-an-azure-databricks-workspace"></a>Vytvoření pracovního prostoru Azure Databricks

V této části vytvoříte Azure Databricks pracovní prostor pomocí šablony Azure Resource Manager.

1. Kliknutím na následující obrázek otevřete šablonu v Azure Portal.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-databricks-workspace%2Fazuredeploy.json" target="_blank"><img src="./media/quickstart-create-databricks-workspace-resource-manager-template/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Zadejte požadované hodnoty pro vytvoření pracovního prostoru Azure Databricks.

   ![Vytvoření Azure Databricks pracovního prostoru pomocí šablony Azure Resource Manager](./media/quickstart-create-databricks-workspace-resource-manager-template/create-databricks-workspace-using-resource-manager-template.png "Vytvoření Azure Databricks pracovního prostoru pomocí šablony Azure Resource Manager")

   Zadejte následující hodnoty:

   |Vlastnost  |Popis  |
   |---------|---------|
   |**Formě**     | V rozevíracím seznamu vyberte své předplatné Azure.        |
   |**Skupina prostředků**     | Určete, zda chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Další informace najdete v tématu [Přehled skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md). |
   |**Poloha**     | Vyberte **východní USA 2**. Další dostupné oblasti najdete v tématu [služby Azure dostupné v jednotlivých oblastech](https://azure.microsoft.com/regions/services/).        |
   |**Název pracovního prostoru**     | Zadejte název pracovního prostoru datacihly.        |
   |**Cenová úroveň**     |  Vyberte si z **úrovně Standard** nebo **Premium**. Další informace o těchto úrovních najdete na stránce s [cenami pro datacihly](https://azure.microsoft.com/pricing/details/databricks/).       |

3. Vyberte Souhlasím **s podmínkami a ujednáními uvedenými nahoře**, vyberte **Připnout na řídicí panel**a potom klikněte na **koupit**.

4. Vytváření pracovního prostoru trvá několik minut. Při vytváření pracovního prostoru se na pravé straně portálu zobrazí dlaždice **odeslání nasazení pro Azure Databricks** . Možná budete muset přejít na řídicí panel Doprava, aby se dlaždice zobrazila. V horní části obrazovky se zobrazí také indikátor průběhu. Pro průběh můžete sledovat obě oblasti.

   Dlaždice nasazení datacihly ![dlaždice](./media/quickstart-create-databricks-workspace-resource-manager-template/databricks-deployment-tile.png "nasazení datacihly")

## <a name="create-a-spark-cluster-in-databricks"></a>Vytvoření clusteru Spark v datacihlách

1. V Azure Portal přejděte do pracovního prostoru datacihly, který jste vytvořili, a pak klikněte na **Spustit pracovní prostor**.

2. Budete přesměrováni na portál Azure Databricks. Na portálu klikněte na **cluster**.

   ![Datacihly v Azure](./media/quickstart-create-databricks-workspace-resource-manager-template/databricks-on-azure.png "datacihlách v Azure")

3. Na stránce **nový cluster** zadejte hodnoty pro vytvoření clusteru.

   ![Vytvoření clusteru datacihly Spark v Azure](./media/quickstart-create-databricks-workspace-resource-manager-template/create-databricks-spark-cluster.png "Vytvoření clusteru datacihly Spark v Azure")

   Přijměte všechny ostatní výchozí hodnoty kromě následujících:

   * Zadejte název clusteru.
   * V tomto článku vytvořte cluster s modulem runtime **4,0** .
   * Ujistěte se, že jste zaškrtli políčko **ukončit po \_ @ no__t – 2 minuty nečinnosti** . Zadejte dobu (v minutách), po kterou se má cluster ukončit, pokud se cluster nepoužívá.

   Vyberte **vytvořit cluster**. Po spuštění clusteru můžete ke clusteru připojit poznámkové bloky a spouštět úlohy Spark.

Další informace o vytváření clusterů najdete [v tématu Vytvoření clusteru Spark v Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

## <a name="run-a-spark-sql-job"></a>Spuštění úlohy Spark SQL

Než začnete s touto částí, musíte splnit následující předpoklady:

* [Vytvořte účet úložiště objektů BLOB v Azure](../storage/common/storage-quickstart-create-account.md).
* Stáhněte si ukázkový soubor JSON [z GitHubu](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json).
* Nahrajte ukázkový soubor JSON do účtu služby Azure Blob Storage, který jste vytvořili. K nahrání souborů můžete použít [Průzkumník služby Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) .

Proveďte následující úlohy pro vytvoření poznámkového bloku v datacihlech, nakonfigurujte Poznámkový blok pro čtení dat z účtu služby Azure Blob Storage a potom na datech spusťte úlohu Spark SQL.

1. V levém podokně klikněte na **pracovní prostor**. V rozevíracím seznamu **pracovní prostor** klikněte na **vytvořit**a potom klikněte na **Poznámkový blok**.

   ![Vytvoření poznámkového bloku v datacihlách](./media/quickstart-create-databricks-workspace-resource-manager-template/databricks-create-notebook.png "vytvoření poznámkového bloku v datacihlech")

2. V dialogovém okně **vytvořit Poznámkový blok** zadejte název, jako jazyk vyberte **Scala** a vyberte cluster Spark, který jste vytvořili dříve.

   ![Vytvoření poznámkového bloku v datacihlách](./media/quickstart-create-databricks-workspace-resource-manager-template/databricks-notebook-details.png "vytvoření poznámkového bloku v datacihlech")

   Klikněte na **vytvořit**.

3. V tomto kroku přidružte účet Azure Storage k clusteru datacihly Spark. Existují dva způsoby, jak toto přidružení provést. Účet Azure Storage můžete připojit k systému souborů datacihly (DBFS) nebo přímo získat přístup k účtu Azure Storage z aplikace, kterou vytvoříte.

   > [!IMPORTANT]
   >Tento článek používá přístup k **připojení úložiště pomocí DBFS**. Tento přístup zajišťuje, že připojené úložiště bude přidruženo k samotnému systému souborů clusteru. Všechny aplikace, které přistupují ke clusteru, proto můžou používat i přidružené úložiště. Přístup s přímým přístupem je omezený na aplikaci, ze které nakonfigurujete přístup.
   >
   > Pokud chcete použít přístup pro připojení, musíte vytvořit cluster Spark s modulem runtime datacihly verze **4,0**, který jste zvolili v tomto článku.

   V následujícím fragmentu kódu nahraďte `{YOUR CONTAINER NAME}`, `{YOUR STORAGE ACCOUNT NAME}` a `{YOUR STORAGE ACCOUNT ACCESS KEY}` odpovídajícími hodnotami pro váš účet Azure Storage. Vložte fragment kódu do prázdné buňky v poznámkovém bloku a stisknutím klávesy SHIFT + ENTER spusťte buňku kódu.

   * **Připojte účet úložiště pomocí DBFS (doporučeno)** . V tomto fragmentu kódu je Azure Storage cesta k účtu připojená k `/mnt/mypath`. Takže ve všech budoucích případech, kdy přistupujete k Azure Storage účtu, nemusíte mít úplnou cestu. Můžete použít jenom `/mnt/mypath`.

          dbutils.fs.mount(
            source = "wasbs://{YOUR CONTAINER NAME}@{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net/",
            mountPoint = "/mnt/mypath",
            extraConfigs = Map("fs.azure.account.key.{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net" -> "{YOUR STORAGE ACCOUNT ACCESS KEY}"))

   * **Přímý přístup k účtu úložiště**

          spark.conf.set("fs.azure.account.key.{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net", "{YOUR STORAGE ACCOUNT ACCESS KEY}")

     Pokyny k načtení klíče účtu úložiště najdete v tématu [Správa přístupových klíčů k úložišti](../storage/common/storage-account-manage.md#access-keys).

   > [!NOTE]
   > Můžete také použít Azure Data Lake Store s clusterem Spark v Azure Databricks. Pokyny najdete v tématu [použití Data Lake Store s Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html).

4. Spuštěním příkazu SQL vytvořte dočasnou tabulku pomocí dat z ukázkového datového souboru JSON **small_radio_json. JSON**. V následujícím fragmentu kódu Nahraďte zástupné hodnoty názvem kontejneru a názvem účtu úložiště. Vložte fragment kódu do buňky kódu v poznámkovém bloku a stiskněte SHIFT + ENTER. Ve fragmentu kódu `path` označuje umístění ukázkového souboru JSON, který jste nahráli do účtu Azure Storage.

   ```sql
   %sql
   DROP TABLE IF EXISTS radio_sample_data;
   CREATE TABLE radio_sample_data
   USING json
   OPTIONS (
    path "/mnt/mypath/small_radio_json.json"
   )
   ```

   Po úspěšném dokončení příkazu budete mít všechna data ze souboru JSON jako tabulku v clusteru datacihly.

   Příkaz `%sql` jazyka Magic umožňuje spustit kód SQL z poznámkového bloku, i když je Poznámkový blok jiného typu. Další informace najdete v tématu [kombinování jazyků v poznámkovém bloku](https://docs.azuredatabricks.net/user-guide/notebooks/index.html#mixing-languages-in-a-notebook).

5. Pojďme se podívat na snímek ukázkových dat JSON, abyste lépe pochopili dotaz, který spouštíte. Vložte následující fragment kódu do buňky kódu a stiskněte klávesy **SHIFT + ENTER**.

   ```sql
   %sql
   SELECT * from radio_sample_data
   ```

6. Zobrazí se tabulkový výstup podobný následujícímu snímku obrazovky (zobrazují se pouze některé sloupce):

   ![Ukázková](./media/quickstart-create-databricks-workspace-resource-manager-template/databricks-sample-csv-data.png "data JSON ukázek") dat JSON

   Kromě dalších podrobností vzorová data zachycují pohlaví posluchačů rádiového kanálu (název sloupce, **pohlaví**) a zda je jejich předplatné bezplatné nebo placené (název sloupce, **úroveň**).

7. Teď vytvoříte vizuální znázornění těchto dat, která se zobrazí pro jednotlivá pohlaví, kolik uživatelů má bezplatné účty a kolik je placených předplatitelů. V dolní části tabulkového výstupu klikněte na ikonu **pruhového grafu** a potom klikněte na **Možnosti grafu**.

   Vytvořit pruhový(./media/quickstart-create-databricks-workspace-resource-manager-template/create-plots-databricks-notebook.png "graf") ![Vytvoření pruhového grafu]

8. V části **přizpůsobit vykreslení**přetáhněte hodnoty, jak je znázorněno na snímku obrazovky.

   Přizpůsobení pruhového(./media/quickstart-create-databricks-workspace-resource-manager-template/databricks-notebook-customize-plot.png "grafu přizpůsobení") ![pruhového grafu]

   * Nastavte **klíče** na **pohlaví**.
   * Nastavte **seskupení řad** na **úroveň**.
   * Nastavte **hodnoty** na **úroveň**.
   * Nastavte **agregaci** na **Count**.

   Klikněte na **použít**.

9. Výstup zobrazuje vizuální znázornění, jak je znázorněno na následujícím snímku obrazovky:

   Přizpůsobení pruhového(./media/quickstart-create-databricks-workspace-resource-manager-template/databricks-sql-query-output-bar-chart.png "grafu přizpůsobení") ![pruhového grafu]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto článku můžete cluster ukončit. Provedete to tak, že z pracovního prostoru Azure Databricks v levém podokně vyberete **clustery**. U clusteru, který chcete ukončit, přesuňte kurzor na tři tečky pod sloupcem **Actions (akce** ) a vyberte ikonu **ukončit** .

![Zastavení clusteru datacihly](./media/quickstart-create-databricks-workspace-resource-manager-template/terminate-databricks-cluster.png "zastavení clusteru datacihly")

Pokud cluster neukončíte ručně, zastaví se automaticky a za předpokladu, že jste při vytváření clusteru zaškrtli políčko **ukončit po \_ @ no__t – 2 minuty nečinnosti** . V takovém případě se cluster automaticky zastaví, pokud byl po určenou dobu neaktivní.

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili cluster Spark v Azure Databricks a spustili jste úlohu Sparku s využitím dat v Azure Storage. Můžete se také podívat na [zdroje dat Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) a Naučte se, jak importovat data z jiných zdrojů dat do Azure Databricks. Můžete se také podívat na šablonu Správce prostředků a [vytvořit pracovní prostor Azure Databricks s vlastní adresou VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-databricks-workspace-with-custom-vnet-address). Informace o syntaxi a vlastnostech JSON pro použití v šabloně najdete v referenčních informacích k šabloně [Microsoft. datacihly/pracovní prostory](/azure/templates/microsoft.databricks/workspaces) .

V dalším článku se dozvíte, jak provést operaci ETL (extrakce, transformace a načítání dat) pomocí Azure Databricks.

> [!div class="nextstepaction"]
> [Extrakce, transformace a načtení dat pomocí Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
