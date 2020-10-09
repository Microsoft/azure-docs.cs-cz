---
title: 'Kurz: implementace vzoru Data Lake Capture pro aktualizaci rozdílové tabulky Azure Databricks | Microsoft Docs'
description: V tomto kurzu se dozvíte, jak pomocí předplatného Event Grid, funkce Azure a úlohy Azure Databricks vložit řádky dat do tabulky, která je uložená v Azure datalake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: normesta
ms.reviewer: sumameh
ms.custom: devx-track-csharp
ms.openlocfilehash: 8df4de01750de92222bfa9021b66828927804e85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89005475"
---
# <a name="tutorial-implement-the-data-lake-capture-pattern-to-update-a-databricks-delta-table"></a>Kurz: implementace vzoru Data Lake Capture pro aktualizaci rozdílové tabulky datacihly

V tomto kurzu se dozvíte, jak zpracovávat události v účtu úložiště, který má hierarchický obor názvů.

Vytvoříte malé řešení, které umožní uživateli naplnit rozdílovou tabulku datacihly tím, že nahraje textový soubor s oddělovači (CSV), který popisuje prodejní objednávku. Toto řešení sestavíte tak, že spojíte Event Grid předplatné, funkci Azure a [úlohu](https://docs.azuredatabricks.net/user-guide/jobs.html) v Azure Databricks.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Vytvoření předplatného Event Grid, které volá funkci Azure Functions.
> * Vytvořte funkci Azure, která obdrží oznámení od události a pak úlohu spustí v Azure Databricks.
> * Vytvořte úlohu datacihly, která vloží objednávku zákazníka do tabulky Delta datacihly, která se nachází v účtu úložiště.

Toto řešení sestavíme v obráceném pořadí, počínaje Azure Databricks pracovním prostorem.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Vytvořte účet úložiště, který má hierarchický obor názvů (Azure Data Lake Storage Gen2). V tomto kurzu se používá účet úložiště s názvem `contosoorders` . Ujistěte se, že váš uživatelský účet má přiřazenou [roli Přispěvatel dat objektů BLOB úložiště](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) .

  Viz [Vytvoření účtu Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).

* Vytvoření instančního objektu. Viz [Postup: použití portálu k vytvoření aplikace a instančního objektu služby Azure AD, který má přístup k prostředkům](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

  K dispozici je několik konkrétních věcí, které budete muset udělat při provádění kroků v tomto článku.

  : heavy_check_mark: při provádění kroků v části [přiřazení aplikace k roli](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) v článku se ujistěte, že k instančnímu objektu přiřadíte roli **Přispěvatel dat objektu BLOB služby Storage** .

  > [!IMPORTANT]
  > Ujistěte se, že roli přiřadíte v oboru účtu úložiště Data Lake Storage Gen2. K nadřazené skupině prostředků nebo předplatnému můžete přiřadit roli, ale chyby související s oprávněními obdržíte, dokud tato přiřazení role nerozšíříte do účtu úložiště.

  : heavy_check_mark: při provádění kroků v části [získat hodnoty pro přihlášení v](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) článku Vložte ID TENANTA, ID aplikace a heslo do textového souboru. Tyto hodnoty budete potřebovat později.

## <a name="create-a-sales-order"></a>Vytvoření prodejní objednávky

Nejdřív vytvořte soubor CSV, který popisuje prodejní objednávku, a pak tento soubor nahrajte do účtu úložiště. Později použijete data z tohoto souboru k naplnění prvního řádku v naší tabulce Delta datacihly.

1. Otevřete Průzkumník služby Azure Storage. Pak přejděte do svého účtu úložiště a v části **kontejnery objektů BLOB** vytvořte nový kontejner s názvem **data**.

   ![Složka dat](./media/data-lake-storage-events/data-container.png "Složka dat")

   Další informace o tom, jak používat Průzkumník služby Storage, najdete v tématu [použití Průzkumník služby Azure Storage ke správě dat v účtu Azure Data Lake Storage Gen2](data-lake-storage-explorer.md).

2. V kontejneru **dat** vytvořte složku s názvem **input**.

3. Vložte následující text do textového editoru.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536365,85123A,WHITE HANGING HEART T-LIGHT HOLDER,6,12/1/2010 8:26,2.55,17850,United Kingdom
   ```

4. Uložte tento soubor do místního počítače a pojmenujte ho **data.csv**.

5. V Průzkumník služby Storage nahrajte tento soubor do **vstupní** složky.  

## <a name="create-a-job-in-azure-databricks"></a>Vytvořit úlohu v Azure Databricks

V této části provedete následující úlohy:

* Vytvořte pracovní prostor Azure Databricks.
* Vytvořte poznámkový blok.
* Vytvoření a naplnění tabulky Delta datacihly.
* Přidejte kód, který vloží řádky do tabulky Delta datacihly.
* Vytvořte úlohu.

### <a name="create-an-azure-databricks-workspace"></a>Vytvoření pracovního prostoru Azure Databricks

V této části vytvoříte pomocí portálu Azure pracovní prostor služby Azure Databricks.

1. V Azure Portal vyberte vytvořit Azure Databricks **prostředků**  >  **Analytics**  >  **Azure Databricks**.

    ![Datacihly na Azure Portal](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Datacihly na Azure Portal")

2. V části **Služba Azure Databricks** zadejte hodnoty pro vytvoření pracovního prostoru Databricks.

    ![Vytvoření pracovního prostoru Azure Databricks](./media/data-lake-storage-events/new-databricks-service.png "Vytvoření pracovního prostoru Azure Databricks")

    Vytvoření pracovního prostoru trvá několik minut. Chcete-li monitorovat stav operace, zobrazte indikátor průběhu v horní části.

### <a name="create-a-spark-cluster-in-databricks"></a>Vytvoření clusteru Spark ve službě Databricks

1. V [Azure Portal](https://portal.azure.com)přejdete do pracovního prostoru Azure Databricks, který jste vytvořili, a pak vyberte **Spustit pracovní prostor**.

2. Budete přesměrováni na portál Azure Databricks. Na portálu vyberte **Nový**  >  **cluster**.

    ![Datacihly v Azure](./media/data-lake-storage-events/databricks-on-azure.png "Datacihly v Azure")

3. Na stránce **New cluster** (Nový cluster) zadejte hodnoty pro vytvoření clusteru.

    ![Vytvoření clusteru datacihly Spark v Azure](./media/data-lake-storage-events/create-databricks-spark-cluster.png "Vytvoření clusteru datacihly Spark v Azure")

    Přijměte všechny výchozí hodnoty kromě následujících:

    * Zadejte název clusteru.
    * Nezapomeňte zaškrtnout políčko **Terminate after 120 minutes of inactivity** (Ukončit po 120 minutách nečinnosti). Zadejte dobu (v minutách), po které se má ukončit činnost clusteru, pokud se cluster nepoužívá.

4. Vyberte **vytvořit cluster**. Po spuštění clusteru můžete ke clusteru připojit poznámkové bloky a spouštět úlohy Spark.

Další informace o vytváření clusterů najdete v tématu [Vytvoření clusteru Spark v Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

### <a name="create-a-notebook"></a>Vytvoření poznámkového bloku

1. V levém podokně vyberte **Pracovní prostor**. V rozevíracím seznamu **Pracovní prostor** vyberte **Vytvořit** > **Poznámkový blok**.

    ![Vytvoření poznámkového bloku v datacihlech](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Vytvoření poznámkového bloku v datacihlech")

2. V dialogovém okně **Vytvořit poznámkový blok** zadejte název poznámkového bloku. Jako jazyk vyberte **Python** a pak vyberte cluster Spark, který jste vytvořili dříve.

    ![Vytvoření poznámkového bloku v datacihlech](./media/data-lake-storage-events/new-databricks-notebook.png "Vytvoření poznámkového bloku v datacihlech")

    Vyberte **Vytvořit**.

### <a name="create-and-populate-a-databricks-delta-table"></a>Vytvoření a naplnění tabulky Delta datacihly

1. V poznámkovém bloku, který jste vytvořili, zkopírujte a vložte následující blok kódu do první buňky, ale tento kód ještě nespustíte.  

   Nahraďte `appId` `password` `tenant` zástupné hodnoty v tomto bloku kódu hodnotami, které jste shromáždili při dokončování požadavků tohoto kurzu.

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

    Tento kód vytvoří pomůcku s názvem **source_file**. Později vytvoříte funkci Azure, která tento kód volá a předá do této pomůcky cestu k souboru.  Tento kód také ověřuje váš instanční objekt s účtem úložiště a vytváří některé proměnné, které budete používat v jiných buňkách.

    > [!NOTE]
    > V nastavení produkčního prostředí zvažte uložení ověřovacího klíče v Azure Databricks. Pak místo ověřovacího klíče přidejte do bloku kódu vyhledávací klíč. <br><br>Například namísto použití tohoto řádku kódu: byste `spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")` použili následující řádek kódu: `spark.conf.set("fs.azure.account.oauth2.client.secret", dbutils.secrets.get(scope = "<scope-name>", key = "<key-name-for-service-credential>"))` . <br><br>Po dokončení tohoto kurzu si přečtěte článek [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) na webu Azure Databricks a podívejte se na příklady tohoto přístupu.

2. Stiskněte klávesy **SHIFT + ENTER** a spusťte kód v tomto bloku.

3. Zkopírujte následující blok kódu a vložte ho do jiné buňky a stiskněte klávesy **SHIFT + ENTER** pro spuštění kódu v tomto bloku.

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

   Tento kód vytvoří v účtu úložiště rozdílovou tabulku datacihly a potom načte některá počáteční data ze souboru CSV, který jste nahráli dříve.

4. Po úspěšném spuštění tohoto bloku kódu odeberte z poznámkového bloku tento blok kódu.

### <a name="add-code-that-inserts-rows-into-the-databricks-delta-table"></a>Přidejte kód, který vloží řádky do tabulky Delta datacihly.

1. Zkopírujte následující blok kódu a vložte ho do jiné buňky, ale tuto buňku nespouštějte.

   ```Python
   upsertDataDF = (spark
     .read
     .option("header", "true")
     .csv(inputPath)
   )
   upsertDataDF.createOrReplaceTempView("customer_data_to_upsert")
   ```

   Tento kód vloží data do tabulkového zobrazení v dočasné tabulce pomocí dat ze souboru CSV. Cesta k tomuto souboru CSV pochází ze vstupní pomůcky, kterou jste vytvořili v předchozím kroku.

2. Přidejte následující kód pro sloučení obsahu dočasného zobrazení tabulky s tabulkou Delta datacihly.

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

Vytvořte úlohu, která spustí Poznámkový blok, který jste vytvořili dříve. Později vytvoříte funkci Azure, která tuto úlohu spustí při vyvolání události.

1. Klikněte na **úlohy**.

2. Na stránce **úlohy** klikněte na **vytvořit úlohu**.

3. Pojmenujte úlohu a pak zvolte `upsert-order-data` sešit.

   ![Vytvoření úlohy](./media/data-lake-storage-events/create-spark-job.png "Vytvoření úlohy")

## <a name="create-an-azure-function"></a>Vytvoření funkce Azure Function

Vytvořte funkci Azure, která úlohu spustí.

1. V horním rohu pracovního prostoru datacihly zvolte ikonu lidé a pak zvolte **nastavení uživatele**.

   ![Správa účtu](./media/data-lake-storage-events/generate-token.png "Uživatelská nastavení")

2. Klikněte na tlačítko **generovat nový token** a pak klikněte na tlačítko **Generovat** .

   Nezapomeňte zkopírovat token na bezpečné místo. Funkce Azure potřebuje tento token k ověřování pomocí datacihly, aby mohl úlohu spustit.
  
3. Vyberte tlačítko **vytvořit prostředek** v levém horním rohu Azure Portal a pak vyberte **COMPUTE > Function App**.

   ![Vytvoření funkce Azure Functions](./media/data-lake-storage-events/function-app-create-flow.png "Vytvoření funkce Azure Functions")

4. Na stránce **vytvořit** Function App Ujistěte se, že jste vybrali **.NET Core** pro zásobník modulu runtime, a ujistěte se, že jste nakonfigurovali instanci Application Insights.

   ![Konfigurace aplikace Function App](./media/data-lake-storage-events/new-function-app.png "Konfigurace aplikace Function App")

5. Na stránce **přehled** Function App klikněte na **Konfigurace**.

   ![Konfigurace aplikace Function App](./media/data-lake-storage-events/configure-function-app.png "Konfigurace aplikace Function App")

6. Na stránce **nastavení aplikace** klikněte na tlačítko **Nová nastavení aplikace** a přidejte jednotlivá nastavení.

   ![Přidat nastavení konfigurace](./media/data-lake-storage-events/add-application-setting.png "Přidat nastavení konfigurace")

   Přidejte následující nastavení:

   |Název nastavení | Hodnota |
   |----|----|
   |**DBX_INSTANCE**| Oblast pracovního prostoru datacihly. Příklad: `westus2.azuredatabricks.net`|
   |**DBX_PAT**| Osobní přístupový token, který jste předtím vygenerovali. |
   |**DBX_JOB_ID**|Identifikátor spuštěné úlohy. V našem případě je tato hodnota `1` .|
7. Na stránce Přehled aplikace Function App klikněte na tlačítko **Nová funkce** .

   ![Nová funkce](./media/data-lake-storage-events/new-function.png "Nová funkce")

8. Vyberte **aktivační událost Azure Event Grid**.

   Pokud budete vyzváni k tomu, nainstalujte rozšíření **Microsoft. Azure. WebJobs. Extensions. EventGrid** . Pokud ho potřebujete nainstalovat, budete muset znovu zvolit **Azure Event Grid Trigger** a vytvořit funkci.

   Zobrazí se podokno **Nová funkce** .

9. V podokně **Nová funkce** pojmenujte funkci **UpsertOrder**a potom klikněte na tlačítko **vytvořit** .

10. Nahraďte obsah souboru s kódem tímto kódem a pak klikněte na tlačítko **Uložit** :

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

   Tento kód analyzuje informace o aktivované události úložiště a následně vytvoří zprávu požadavku s adresou URL souboru, který událost aktivoval. V rámci zprávy funkce předává hodnotu widgetu **source_file** , kterou jste vytvořili dříve. kód funkce odešle zprávu do úlohy datacihly a použije token, který jste dříve získali jako ověřování.

## <a name="create-an-event-grid-subscription"></a>Vytvoření odběru Event Gridu

V této části vytvoříte předplatné Event Grid, které při nahrávání souborů do účtu úložiště zavolá funkci Azure Functions.

1. Na stránce s kódem funkce klikněte na tlačítko **přidat Event Grid předplatné** .

   ![Nové předplatné událostí](./media/data-lake-storage-events/new-event-subscription.png "Nové předplatné událostí")

2. Na stránce **vytvořit odběr události** zadejte název předplatného a pak použijte pole na stránce a vyberte svůj účet úložiště.

   ![Nové předplatné událostí](./media/data-lake-storage-events/new-event-subscription-2.png "Nové předplatné událostí")

3. V rozevíracím seznamu **filtrovat do typů událostí** vyberte **vytvořený objekt BLOB**a události **odstraněné z objektů BLOB** a potom klikněte na tlačítko **vytvořit** .

## <a name="test-the-event-grid-subscription"></a>Test předplatného Event Grid

1. Vytvořte soubor s názvem `customer-order.csv` , do tohoto souboru vložte následující informace a uložte ho do místního počítače.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,228,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

2. V Průzkumník služby Storage nahrajte tento soubor do **vstupní** složky vašeho účtu úložiště.

   Nahráním souboru se vyvolá událost **Microsoft. Storage. BlobCreated** . Event Grid upozorní všechny předplatitele na tuto událost. V našem případě je jediným předplatitelem funkce Azure Functions. Funkce Azure analyzuje parametry události a určí, která událost se stala. Pak předá adresu URL souboru do úlohy datacihly. Úloha datacihly načte soubor a přidá řádek do tabulky Delta datacihly, která je umístěná v účtu úložiště.

3. Pokud chcete zjistit, jestli se úloha úspěšně dokončila, otevřete pracovní prostor datacihly, klikněte na tlačítko **úlohy** a pak otevřete svoji úlohu.

4. Výběrem úlohy otevřete stránku úlohy.

   ![Spark – úloha](./media/data-lake-storage-events/spark-job.png "Spark – úloha")

   Po dokončení úlohy se zobrazí stav dokončení.

   ![Úloha se úspěšně dokončila.](./media/data-lake-storage-events/spark-job-completed.png "Úloha se úspěšně dokončila.")

5. V nové buňce sešitu spusťte v buňce tento dotaz, aby se zobrazila aktualizovaná tabulka Delta.

   ```
   %sql select * from customer_data
   ```

   Vrácená tabulka ukazuje poslední záznam.

   ![Poslední záznam se zobrazí v tabulce.](./media/data-lake-storage-events/final_query.png "Poslední záznam se zobrazí v tabulce.")

6. Chcete-li aktualizovat tento záznam, vytvořte soubor s názvem `customer-order-update.csv` , do tohoto souboru vložte následující informace a uložte jej do místního počítače.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,22,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

   Tento soubor CSV je skoro totožný s předchozím souborem, s výjimkou toho, že je množství objednávky změněno z `228` na `22` .

7. V Průzkumník služby Storage nahrajte tento soubor do **vstupní** složky vašeho účtu úložiště.

8. Spusťte `select` dotaz znovu, abyste viděli aktualizovanou tabulku Delta.

   ```
   %sql select * from customer_data
   ```

   Vrácená tabulka zobrazuje aktualizovaný záznam.

   ![Aktualizovaný záznam se zobrazuje v tabulce.](./media/data-lake-storage-events/final_query-2.png "Aktualizovaný záznam se zobrazuje v tabulce.")

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků pro účet úložiště a vyberete **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reakce na události služby Blob Storage](storage-blob-event-overview.md)
