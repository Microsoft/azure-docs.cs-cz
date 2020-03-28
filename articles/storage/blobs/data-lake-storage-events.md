---
title: 'Kurz: Implementace vzoru sběru datového jezera k aktualizaci tabulky Delta Azure Databricks | Dokumenty společnosti Microsoft'
description: Tento kurz ukazuje, jak používat předplatné Event Grid, funkci Azure a úlohu Azure Databricks k vložení řádků dat do tabulky, která je uložená v Azure DataLake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: normesta
ms.reviewer: sumameh
ms.openlocfilehash: 85fad873b6c176d2278ea48709d2892ab515a025
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78303303"
---
# <a name="tutorial-implement-the-data-lake-capture-pattern-to-update-a-databricks-delta-table"></a>Kurz: Implementace vzoru sběru datového jezera k aktualizaci tabulky Databricks Delta

Tento kurz ukazuje, jak zpracovat události v účtu úložiště, který má hierarchický obor názvů.

Vytvoříte malé řešení, které uživateli umožní naplnit tabulku Delta Databricks nahráním souboru hodnot oddělených čárkami (csv), který popisuje prodejní objednávku. Toto řešení vytvoříte tak, že propojíte předplatné Grid událostí, funkci Azure a [úlohu](https://docs.azuredatabricks.net/user-guide/jobs.html) v Azure Databricks.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Vytvořte předplatné Grid událostí, které volá funkci Azure.
> * Vytvořte funkci Azure, která obdrží oznámení z události a pak spustí úlohu v Azure Databricks.
> * Vytvořte úlohu Databricks, která vloží objednávku zákazníka do tabulky Databricks Delta, která je umístěna v účtu úložiště.

Toto řešení vytvoříme v opačném pořadí, počínaje pracovním prostorem Azure Databricks.

## <a name="prerequisites"></a>Požadavky

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

* Vytvořte účet úložiště, který má hierarchický obor názvů (Azure Data Lake Storage Gen2). Tento kurz používá účet `contosoorders`úložiště s názvem . Ujistěte se, že váš uživatelský účet má [roli přispěvatele dat objektů blob úložiště](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) přiřazenou.

  Viz [Vytvoření účtu Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).

* Vytvořte instanční objekt. Viz [Postup: Pomocí portálu vytvořte instanční objekt azure a služby, který má přístup k prostředkům](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

  Existuje několik konkrétních věcí, které budete muset udělat, když provedete kroky v tomto článku.

  :heavy_check_mark: Při provádění kroků v části [Přiřazení aplikace k](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) části role článku nezapomeňte přiřadit roli **přispěvatele dat objektů blob úložiště** k instančnímu objektu.

  > [!IMPORTANT]
  > Nezapomeňte přiřadit roli v oboru účtu úložiště Storage Storage Data Lake Storage. Roli můžete přiřadit nadřazené skupině prostředků nebo předplatnému, ale budete dostávat chyby související s oprávněními, dokud se tato přiřazení rolí nerozšíří do účtu úložiště.

  :heavy_check_mark: Při provádění kroků v části [Získat hodnoty pro podepisování v](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) článku vložte ID klienta, ID aplikace a hodnoty hesla do textového souboru. Tyto hodnoty budete potřebovat později.

## <a name="create-a-sales-order"></a>Vytvoření prodejní objednávky

Nejprve vytvořte soubor CSV, který popisuje prodejní objednávku, a potom jej nahrajte do účtu úložiště. Později použijete data z tohoto souboru k naplnění prvního řádku v tabulce Delta Databricks.

1. Otevřete Průzkumníka úložišť Azure. Potom přejděte na svůj účet úložiště a v části **Kontejnery objektů blob** vytvořte nový kontejner s názvem **data**.

   ![složka dat](./media/data-lake-storage-events/data-container.png "složka dat")

   Další informace o tom, jak používat Průzkumníka úložiště, najdete [v tématu Správa dat v účtu Azure Data Lake Storage Gen2 pomocí Průzkumníka úložiště Azure.](data-lake-storage-explorer.md)

2. V **kontejneru dat** vytvořte složku s názvem **input**.

3. Vložte následující text do textového editoru.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536365,85123A,WHITE HANGING HEART T-LIGHT HOLDER,6,12/1/2010 8:26,2.55,17850,United Kingdom
   ```

4. Uložte tento soubor do místního počítače a pojmenujte jej **data.csv**.

5. V Průzkumníku úložiště nahrajte tento soubor do **vstupní** složky.  

## <a name="create-a-job-in-azure-databricks"></a>Vytvoření úlohy v Azure Databricks

V této části budete provádět tyto úkoly:

* Vytvořte pracovní prostor Azure Databricks.
* Vytvořte poznámkový blok.
* Vytvořte a naplňte tabulku Delta Databricks.
* Přidejte kód, který vloží řádky do tabulky Databricks Delta.
* Vytvořte úlohu.

### <a name="create-an-azure-databricks-workspace"></a>Vytvoření pracovního prostoru Azure Databricks

V této části vytvoříte pomocí portálu Azure pracovní prostor služby Azure Databricks.

1. Na webu Azure Portal vyberte **Vytvořit zdroj** > **Analytics** > **Azure Databricks**.

    ![Datové cihly na webu Azure Portal](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Datové cihly na webu Azure Portal")

2. V části **Služba Azure Databricks** zadejte hodnoty pro vytvoření pracovního prostoru Databricks.

    ![Vytvoření pracovního prostoru Azure Databricks](./media/data-lake-storage-events/new-databricks-service.png "Vytvoření pracovního prostoru Azure Databricks")

    Vytvoření pracovního prostoru trvá několik minut. Chcete-li sledovat stav operace, zobrazte indikátor průběhu nahoře.

### <a name="create-a-spark-cluster-in-databricks"></a>Vytvoření clusteru Spark ve službě Databricks

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do pracovního prostoru Azure Databricks, který jste vytvořili, a pak vyberte **Spustit pracovní prostor**.

2. Budete přesměrováni na portál Azure Databricks. Na portálu vyberte **Nový** > **cluster**.

    ![Datové cihly v Azure](./media/data-lake-storage-events/databricks-on-azure.png "Datové cihly v Azure")

3. Na stránce **New cluster** (Nový cluster) zadejte hodnoty pro vytvoření clusteru.

    ![Vytvoření clusteru Databricks Spark v Azure](./media/data-lake-storage-events/create-databricks-spark-cluster.png "Vytvoření clusteru Databricks Spark v Azure")

    Přijměte všechny výchozí hodnoty kromě následujících:

    * Zadejte název clusteru.
    * Nezapomeňte zaškrtnout políčko **Terminate after 120 minutes of inactivity** (Ukončit po 120 minutách nečinnosti). Zadejte dobu (v minutách), po které se má ukončit činnost clusteru, pokud se cluster nepoužívá.

4. Vyberte **Vytvořit cluster**. Po spuštění clusteru můžete ke clusteru připojit poznámkové bloky a spouštět úlohy Spark.

Další informace o vytváření clusterů najdete v tématu [Vytvoření clusteru Spark v Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

### <a name="create-a-notebook"></a>Vytvoření poznámkového bloku

1. V levém podokně vyberte **Pracovní prostor**. V rozevíracím seznamu **Pracovní prostor** vyberte **Vytvořit** > **Poznámkový blok**.

    ![Vytvoření poznámkového bloku v datových cihlách](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Vytvoření poznámkového bloku v datových cihlách")

2. V dialogovém okně **Vytvořit poznámkový blok** zadejte název poznámkového bloku. Vyberte **Python** jako jazyk a pak vyberte cluster Spark, který jste vytvořili dříve.

    ![Vytvoření poznámkového bloku v datových cihlách](./media/data-lake-storage-events/new-databricks-notebook.png "Vytvoření poznámkového bloku v datových cihlách")

    Vyberte **Vytvořit**.

### <a name="create-and-populate-a-databricks-delta-table"></a>Vytvoření a naplnění tabulky Delta Datových cihel

1. V poznámkovém bloku, který jste vytvořili, zkopírujte a vložte následující blok kódu do první buňky, ale tento kód ještě nespouštějte.  

   Nahraďte `password` `tenant` zástupné hodnoty `appId`, v tomto bloku kódu hodnotami, které jste shromáždili při vyplňování předpokladů tohoto kurzu.

    ```Python
    dbutils.widgets.text('source_file', "", "Source File")

    spark.conf.set("fs.azure.account.auth.type", "OAuth")
    spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
    spark.conf.set("fs.azure.account.oauth2.client.id", "<appId>")
    spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")
    spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant>/oauth2/token")

    adlsPath = 'abfss://data@contosoorders.dfs.core.windows.net/'
    inputPath = adlsPath + dbutils.widgets.get('source_file')
    customerTablePath = adlsPath + 'delta-tables/customers'
    ```

    Tento kód vytvoří widget s názvem **source_file**. Později vytvoříte funkci Azure, která volá tento kód a předá cestu k tomuto widgetu.  Tento kód také ověřuje váš instanční objekt pomocí účtu úložiště a vytvoří některé proměnné, které budete používat v jiných buňkách.

    > [!NOTE]
    > V produkčním prostředí zvažte uložení ověřovacího klíče v Azure Databricks. Potom přidejte vyhledávací klíč do bloku kódu namísto ověřovacího klíče. <br><br>Například místo použití tohoto řádku `spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")`kódu: byste použili následující `spark.conf.set("fs.azure.account.oauth2.client.secret", dbutils.secrets.get(scope = "<scope-name>", key = "<key-name-for-service-credential>"))`řádek kódu: . <br><br>Po dokončení tohoto kurzu najdete v článku [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) na webu Azure Databricks zobrazíte příklady tohoto přístupu.

2. Stisknutím kláves **SHIFT + ENTER** spusťte kód v tomto bloku.

3. Zkopírujte a vložte následující blok kódu do jiné buňky a stisknutím kláves **SHIFT + ENTER** spusťte kód v tomto bloku.

   ```Python
   from pyspark.sql.types import StructType, StructField, DoubleType, IntegerType, StringType


   inputSchema = StructType([
   StructField("InvoiceNo", IntegerType(), True),
   StructField("StockCode", StringType(), True),
   StructField("Description", StringType(), True),
   StructField("Quantity", IntegerType(), True),
   StructField("InvoiceDate", StringType(), True),
   StructField("UnitPrice", DoubleType(), True),
   StructField("CustomerID", IntegerType(), True),
   StructField("Country", StringType(), True)
   ])

   rawDataDF = (spark.read
    .option("header", "true")
    .schema(inputSchema)
    .csv(adlsPath + 'input')
   )

   (rawDataDF.write
     .mode("overwrite")
     .format("delta")
     .saveAsTable("customer_data", path=customerTablePath))
   ```

   Tento kód vytvoří tabulku Databricks Delta ve vašem účtu úložiště a pak načte některá počáteční data ze souboru CSV, který jste nahráli dříve.

4. Po úspěšném spuštění tohoto bloku kódu odeberte tento blok kódu z poznámkového bloku.

### <a name="add-code-that-inserts-rows-into-the-databricks-delta-table"></a>Přidání kódu, který vloží řádky do tabulky Databricks Delta

1. Zkopírujte a vložte následující blok kódu do jiné buňky, ale nespouštějte tuto buňku.

   ```Python
   upsertDataDF = (spark
     .read
     .option("header", "true")
     .csv(inputPath)
   )
   upsertDataDF.createOrReplaceTempView("customer_data_to_upsert")
   ```

   Tento kód vloží data do dočasného zobrazení tabulky pomocí dat ze souboru CSV. Cesta k tomuto souboru CSV pochází ze vstupního widgetu, který jste vytvořili v předchozím kroku.

2. Přidejte následující kód pro sloučení obsahu zobrazení dočasné tabulky s tabulka Databricks Delta.

   ```
   %sql
   MERGE INTO customer_data cd
   USING customer_data_to_upsert cu
   ON cd.CustomerID = cu.CustomerID
   WHEN MATCHED THEN
     UPDATE SET
       cd.StockCode = cu.StockCode,
       cd.Description = cu.Description,
       cd.InvoiceNo = cu.InvoiceNo,
       cd.Quantity = cu.Quantity,
       cd.InvoiceDate = cu.InvoiceDate,
       cd.UnitPrice = cu.UnitPrice,
       cd.Country = cu.Country
   WHEN NOT MATCHED
     THEN INSERT (InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, Country)
     VALUES (
       cu.InvoiceNo,
       cu.StockCode,
       cu.Description,
       cu.Quantity,
       cu.InvoiceDate,
       cu.UnitPrice,
       cu.CustomerID,
       cu.Country)
   ```

### <a name="create-a-job"></a>Vytvoření úlohy

Vytvořte úlohu, která spustí poznámkový blok, který jste vytvořili dříve. Později vytvoříte funkci Azure, která spustí tuto úlohu při vyvolání události.

1. Klepněte na **položku Úlohy**.

2. Na stránce **Úlohy** klikněte na **Vytvořit úlohu**.

3. Pojmenujte úlohu a pak `upsert-order-data` zvolte sešit.

   ![Vytvoření úlohy](./media/data-lake-storage-events/create-spark-job.png "Vytvoření úlohy")

## <a name="create-an-azure-function"></a>Vytvoření funkce Azure

Vytvořte funkci Azure, která spouští úlohu.

1. V horním rohu pracovního prostoru Databricks zvolte ikonu lidé a pak zvolte **Uživatelská nastavení**.

   ![Správa účtu](./media/data-lake-storage-events/generate-token.png "Uživatelská nastavení")

2. Klikněte na tlačítko **Generovat nový token** a potom klikněte na tlačítko **Generovat.**

   Ujistěte se, že zkopírovat token na bezpečné místo. Vaše funkce Azure potřebuje tento token k ověření pomocí Databricks tak, aby jej můžete spustit úlohu.
  
3. Vyberte tlačítko **Vytvořit prostředek,** které se nachází v levém horním rohu portálu Azure, a pak vyberte **Compute > Function App**.

   ![Vytvořit funkci Azure](./media/data-lake-storage-events/function-app-create-flow.png "Vytvoření funkce Azure")

4. Na stránce **Vytvořit** aplikace funkce nezapomeňte vybrat **.NET Core** pro zásobník za běhu a nezapomeňte nakonfigurovat instanci Application Insights.

   ![Konfigurace aplikace Function App](./media/data-lake-storage-events/new-function-app.png "Konfigurace aplikace Function App")

5. Na stránce **Přehled** aplikace Funkce klepněte na **tlačítko Konfigurace**.

   ![Konfigurace aplikace Function App](./media/data-lake-storage-events/configure-function-app.png "Konfigurace aplikace Function App")

6. Na stránce **Nastavení aplikace** zvolte tlačítko Nastavení **nové aplikace,** které chcete každé nastavení přidat.

   ![Přidat nastavení konfigurace](./media/data-lake-storage-events/add-application-setting.png "Přidat nastavení konfigurace")

   Přidejte následující nastavení:

   |Název nastavení | Hodnota |
   |----|----|
   |**DBX_INSTANCE**| Oblast pracovního prostoru databricks. Příklad: `westus2.azuredatabricks.net`|
   |**DBX_PAT**| Osobní přístupový token, který jste vygenerovali dříve. |
   |**DBX_JOB_ID**|Identifikátor spuštěné úlohy. V našem případě je `1`tato hodnota .|
7. Na stránce s přehledem aplikace funkce klikněte na tlačítko **Nová funkce.**

   ![Nová funkce](./media/data-lake-storage-events/new-function.png "Nová funkce")

8. Zvolte **Aktivační událost sítě událostí Azure**.

   Pokud k tomu budete vyzváni, nainstalujte rozšíření **Microsoft.Azure.WebJobs.Extensions.EventGrid.** Pokud ji musíte nainstalovat, budete muset znovu zvolit **Azure Event Grid Trigger** a vytvořit funkci.

   Zobrazí se podokno **Nová funkce.**

9. V podokně **Nová funkce** pojmenujte funkci **UpsertOrder**a klepněte na tlačítko **Vytvořit.**

10. Nahraďte obsah souboru kódu tímto kódem a klepněte na tlačítko **Uložit:**

    ```cs
    using "Microsoft.Azure.EventGrid"
    using "Newtonsoft.Json"
    using Microsoft.Azure.EventGrid.Models;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;

    private static HttpClient httpClient = new HttpClient();

    public static async Task Run(EventGridEvent eventGridEvent, ILogger log)
    {
        log.LogInformation("Event Subject: " + eventGridEvent.Subject);
        log.LogInformation("Event Topic: " + eventGridEvent.Topic);
        log.LogInformation("Event Type: " + eventGridEvent.EventType);
        log.LogInformation(eventGridEvent.Data.ToString());

        if (eventGridEvent.EventType == "Microsoft.Storage.BlobCreated" | | eventGridEvent.EventType == "Microsoft.Storage.FileRenamed") {
            var fileData = ((JObject)(eventGridEvent.Data)).ToObject<StorageBlobCreatedEventData>();
            if (fileData.Api == "FlushWithClose") {
                log.LogInformation("Triggering Databricks Job for file: " + fileData.Url);
                var fileUrl = new Uri(fileData.Url);
                var httpRequestMessage = new HttpRequestMessage {
                    Method = HttpMethod.Post,
                    RequestUri = new Uri(String.Format("https://{0}/api/2.0/jobs/run-now", System.Environment.GetEnvironmentVariable("DBX_INSTANCE", EnvironmentVariableTarget.Process))),
                    Headers = {
                        { System.Net.HttpRequestHeader.Authorization.ToString(), "Bearer " +  System.Environment.GetEnvironmentVariable ("DBX_PAT", EnvironmentVariableTarget.Process)},
                        { System.Net.HttpRequestHeader.ContentType.ToString (), "application/json" }
                    },
                    Content = new StringContent(JsonConvert.SerializeObject(new {
                        job_id = System.Environment.GetEnvironmentVariable ("DBX_JOB_ID", EnvironmentVariableTarget.Process) ,
                        notebook_params = new {
                            source_file = String.Join("", fileUrl.Segments.Skip(2))
                        }
                    }))
                 };
                var response = await httpClient.SendAsync(httpRequestMessage);
                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

   Tento kód analyzuje informace o události úložiště, která byla vyvolána a potom vytvoří zprávu požadavku s adresou URL souboru, který spustil událost. Jako součást zprávy předá funkce hodnotu **source_file** widgetu, který jste vytvořili dříve. kód funkce odešle zprávu databricks úlohy a používá token, který jste získali dříve jako ověřování.

## <a name="create-an-event-grid-subscription"></a>Vytvoření odběru Event Gridu

V této části vytvoříte odběr Grid událostí, který volá funkci Azure při odeslání souborů do účtu úložiště.

1. Na stránce s kódem funkce klikněte na tlačítko **Přidat odběr mřížky událostí.**

   ![Předplatné nové události](./media/data-lake-storage-events/new-event-subscription.png "Předplatné nové události")

2. Na stránce **Vytvořit odběr události** pojmenujte odběr a potom pomocí polí na stránce vyberte účet úložiště.

   ![Předplatné nové události](./media/data-lake-storage-events/new-event-subscription-2.png "Předplatné nové události")

3. V rozevíracím seznamu **Filtr ovat na typy událostí** vyberte události Objekt u **objektu blob a** **Odstraněné objekty objektů blob** a klepněte na tlačítko **Vytvořit.**

## <a name="test-the-event-grid-subscription"></a>Testování předplatného Event Grid

1. Vytvořte soubor `customer-order.csv`s názvem , vložte do něj následující informace a uložte je do místního počítače.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,228,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

2. V Průzkumníku úložiště nahrajte tento soubor do **vstupní** složky vašeho účtu úložiště.

   Nahrání souboru vyvolá událost **Microsoft.Storage.BlobCreated.** Event Grid upozorní všechny předplatitele na tuto událost. V našem případě je funkce Azure jediným předplatitelem. Funkce Azure analyzuje parametry události k určení, ke které došlo. Poté předá adresu URL souboru úloze Databricks. Databricks Job přečte soubor a přidá řádek databricks delta tabulka, která se nachází účet úložiště.

3. Chcete-li zkontrolovat, zda byla úloha úspěšná, otevřete pracovní prostor databricks, klikněte na tlačítko **Úlohy** a otevřete úlohu.

4. Vyberte úlohu, kterou chcete otevřít stránku úlohy.

   ![Spark práce](./media/data-lake-storage-events/spark-job.png "Spark práce")

   Po dokončení úlohy se zobrazí stav dokončení.

   ![Úspěšně dokončená úloha](./media/data-lake-storage-events/spark-job-completed.png "Úspěšně dokončená úloha")

5. V nové buňce sešitu spusťte tento dotaz v buňce a zobce, abyste viděli aktualizovanou tabulku delta.

   ```
   %sql select * from customer_data
   ```

   Vrácená tabulka zobrazuje nejnovější záznam.

   ![Poslední záznam se zobrazí v tabulce](./media/data-lake-storage-events/final_query.png "Poslední záznam se zobrazí v tabulce")

6. Chcete-li tento záznam aktualizovat, vytvořte soubor s názvem `customer-order-update.csv`, vložte do něj následující informace a uložte je do místního počítače.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,22,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

   Tento soubor csv je téměř totožný s předchozím, `228` `22`s výjimkou množství objednávky se změní z na .

7. V Průzkumníku úložiště nahrajte tento soubor do **vstupní** složky vašeho účtu úložiště.

8. Chcete-li `select` zobrazit aktualizovanou tabulku delta, spusťte dotaz znovu.

   ```
   %sql select * from customer_data
   ```

   Vrácená tabulka zobrazuje aktualizovaný záznam.

   ![Aktualizovaný záznam se zobrazí v tabulce](./media/data-lake-storage-events/final_query-2.png "Aktualizovaný záznam se zobrazí v tabulce")

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nejsou potřeba, odstraňte skupinu prostředků a všechny související prostředky. Chcete-li tak učinit, vyberte skupinu prostředků pro účet úložiště a vyberte **odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reakce na události služby Blob Storage](storage-blob-event-overview.md)
