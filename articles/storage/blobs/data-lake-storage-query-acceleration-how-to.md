---
title: Filtrování dat pomocí Azure Data Lake Storage akcelerace dotazů | Microsoft Docs
description: Pomocí akcelerace dotazů načtěte podmnožinu dat z vašeho účtu úložiště.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2021
ms.author: normesta
ms.reviewer: jamsbak
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 0340ab0a13d7c639d5e5bba49b3cdd445bad5a83
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879877"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration"></a>Filtrování dat pomocí Azure Data Lake Storage akcelerace dotazů

V tomto článku se dozvíte, jak pomocí akcelerace dotazů načíst podmnožinu dat z vašeho účtu úložiště. 

Zrychlení dotazů umožňuje aplikacím a analytickým architekturám významně optimalizovat zpracování dat tím, že načte jenom data, která potřebují k provedení dané operace. Další informace najdete v tématu [Azure Data Lake Storage zrychlení dotazů](data-lake-storage-query-acceleration.md).

## <a name="prerequisites"></a>Požadavky

- Pokud chcete získat přístup k Azure Storage, budete potřebovat předplatné Azure. Pokud ještě předplatné nemáte, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

- Účet úložiště pro **obecné účely v2** . viz [Vytvoření účtu úložiště](../common/storage-account-create.md).

- Vyberte kartu pro zobrazení všech požadavků specifických pro sadu SDK.

  ### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

  Neuvedeno

  ### <a name="net"></a>[.NET](#tab/dotnet)

  [Sada .NET SDK](https://dotnet.microsoft.com/download) 

  ### <a name="java"></a>[Java](#tab/java)

  - [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) verze 8 nebo vyšší

  - [Apache Maven](https://maven.apache.org/download.cgi) 

    > [!NOTE] 
    > V tomto článku se předpokládá, že jste vytvořili projekt Java pomocí Apache Maven. Příklad toho, jak vytvořit projekt pomocí Apache Maven, najdete v tématu [Nastavení](storage-quickstart-blobs-java.md#setting-up).
  
  ### <a name="python"></a>[Python](#tab/python)

  [Python](https://www.python.org/downloads/) 3,8 nebo vyšší.

  ### <a name="nodejs"></a>[Node.js](#tab/nodejs)

  Pro použití sady Node.js SDK nejsou vyžadovány žádné další požadavky.

---

## <a name="enable-query-acceleration"></a>Povolit zrychlení dotazů

Pokud chcete použít zrychlení dotazů, musíte u svého předplatného zaregistrovat funkci zrychlení dotazů. Po ověření, zda je funkce zaregistrována, je nutné zaregistrovat poskytovatele prostředků Azure Storage. 

### <a name="step-1-register-the-query-acceleration-feature"></a>Krok 1: registrace funkce zrychlení dotazů

Pokud chcete použít zrychlení dotazů, musíte nejdřív zaregistrovat funkci zrychlení dotazů u svého předplatného. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Otevřete okno příkazového řádku Windows PowerShellu.

1. Přihlaste se ke svému předplatnému Azure pomocí příkazu `Connect-AzAccount` a postupujte podle pokynů na obrazovce.

   ```powershell
   Connect-AzAccount
   ```

2. Pokud je vaše identita přidružená k více než jednomu předplatnému, nastavte své aktivní předplatné.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Nahraďte `<subscription-id>` hodnotu zástupného symbolu číslem ID vašeho předplatného.

3. Zaregistrujte funkci zrychlení dotazů pomocí příkazu [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

   ```powershell
   Register-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
   ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Otevřete [Azure Cloud Shell](../../cloud-shell/overview.md)nebo pokud máte rozhraní příkazového řádku Azure místně [nainstalované](/cli/azure/install-azure-cli) , otevřete konzolovou aplikaci, například Windows PowerShell.

2. Pokud je vaše identita přidružená k více než jednomu předplatnému, nastavte své aktivní předplatné na předplatné účtu úložiště.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Nahraďte `<subscription-id>` hodnotu zástupného symbolu číslem ID vašeho předplatného.

3. Zaregistrujte funkci zrychlení dotazů pomocí příkazu [AZ Feature Register](/cli/azure/feature#az-feature-register) .

   ```azurecli
   az feature register --namespace Microsoft.Storage --name BlobQuery
   ```

---

### <a name="step-2-verify-that-the-feature-is-registered"></a>Krok 2: Ověření registrace funkce

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete ověřit, že se registrace dokončila, použijte příkaz [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li ověřit, zda byla registrace dokončena, použijte příkaz [AZ Feature](/cli/azure/feature#az-feature-show) .

```azurecli
az feature show --namespace Microsoft.Storage --name BlobQuery
```

---

### <a name="step-3-register-the-azure-storage-resource-provider"></a>Krok 3: registrace poskytovatele prostředků Azure Storage

Po schválení registrace je potřeba znovu zaregistrovat poskytovatele prostředků Azure Storage. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Chcete-li zaregistrovat poskytovatele prostředků, použijte příkaz [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) .

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li zaregistrovat poskytovatele prostředků, použijte příkaz [AZ Provider Register](/cli/azure/provider#az-provider-register) .

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

---

## <a name="set-up-your-environment"></a>Nastavení prostředí

### <a name="step-1-install-packages"></a>Krok 1: Instalace balíčků 

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nainstalujte modul AZ Module verze 4.6.0 nebo vyšší.

```powershell
Install-Module -Name Az -Repository PSGallery -Force
```

Chcete-li provést aktualizaci ze starší verze AZ, spusťte následující příkaz:

```powershell
Update-Module -Name Az
```

#### <a name="net"></a>[.NET](#tab/dotnet)

1. Otevřete příkazový řádek a změňte adresář ( `cd` ) na složku projektu, například:

   ```console
   cd myProject
   ```

2. `12.5.0-preview.6`Pomocí příkazu nainstalujte verzi balíčku klienta Azure Blob Storage pro balíček .NET `dotnet add package` . 

   ```console
   dotnet add package Azure.Storage.Blobs -v 12.6.0
   ```

3. Příklady, které se zobrazí v tomto článku, analyzují soubor CSV pomocí knihovny [CsvHelper](https://www.nuget.org/packages/CsvHelper/) . K použití této knihovny použijte následující příkaz.

   ```console
   dotnet add package CsvHelper
   ```

#### <a name="java"></a>[Java](#tab/java)

1. Otevřete soubor *pom.xml* projektu v textovém editoru. Přidejte následující prvky závislosti do skupiny závislostí. 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.6.0</version>
   </dependency>
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-csv</artifactId>
        <version>1.8</version>
    </dependency>    
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-storage-blob</artifactId>
      <version>12.8.0-beta.1</version>
    </dependency>
   ```

#### <a name="python"></a>[Python](#tab/python)

Nainstalujte klientskou knihovnu Azure Data Lake Storage pro Python pomocí [PIP](https://pypi.org/project/pip/).

```
pip install azure-storage-blob==12.4.0
```

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Nainstalujte Data Lake klientské knihovny pro JavaScript tak, že otevřete okno terminálu a pak zadáte následující příkaz.

```javascript
    npm install @azure/storage-blob
    npm install @fast-csv/parse
```

---

### <a name="step-2-add-statements"></a>Krok 2: Přidání příkazů

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Neuvedeno

#### <a name="net"></a>[.NET](#tab/dotnet)

Přidejte tyto `using` příkazy do horní části souboru kódu.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
```

Zrychlení dotazů načte data ve formátu CSV a JSON. Proto nezapomeňte přidat příkazy using pro jakékoli knihovny pro analýzu CSV nebo JSON, které se rozhodnete použít. Příklady, které se zobrazí v tomto článku, analyzují soubor CSV pomocí knihovny [CsvHelper](https://www.nuget.org/packages/CsvHelper/) , která je dostupná na NuGet. Proto přidáme tyto `using` příkazy do horní části souboru kódu.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

Chcete-li zkompilovat příklady uvedené v tomto článku, budete také muset přidat `using` i tyto příkazy.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
```

#### <a name="java"></a>[Java](#tab/java)

Přidejte tyto `import` příkazy do horní části souboru kódu.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.options.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import java.io.*;
import java.util.function.Consumer;
import org.apache.commons.csv.*;
```

#### <a name="python"></a>[Python](#tab/python)

Přidejte tyto příkazy pro import do horní části souboru kódu.

```python
import sys, csv
from azure.storage.blob import BlobServiceClient, ContainerClient, BlobClient, DelimitedTextDialect, BlobQueryError
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Zahrňte `storage-blob` modul vložením tohoto příkazu do horní části souboru kódu. 

```javascript
const { BlobServiceClient } = require("@azure/storage-blob");
```

Zrychlení dotazů načte data ve formátu CSV a JSON. Proto je nutné přidat příkazy pro jakékoli moduly pro analýzu CSV nebo JSON, které se rozhodnete použít. Příklady, které se zobrazí v tomto článku, analyzují soubor CSV pomocí modulu [Fast-CSV](https://www.npmjs.com/package/fast-csv) . Proto přidáme tento příkaz na začátek souboru kódu.

```javascript
const csv = require('@fast-csv/parse');
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Načtení dat pomocí filtru

Pomocí jazyka SQL můžete zadat predikáty filtru řádků a projekce sloupců v požadavku na zrychlení dotazu. Následující kód odešle dotaz do souboru CSV v úložišti a vrátí všechny řádky dat, kde třetí sloupec odpovídá hodnotě `Hemingway, Ernest` . 

- V dotazu SQL se klíčové slovo `BlobStorage` používá k označení souboru, na který se dotazuje.

- Odkazy na sloupce jsou zadány jako `_N` v případě prvního sloupce `_1` . Pokud zdrojový soubor obsahuje řádek záhlaví, můžete odkazovat na sloupce podle názvu, který je uveden v řádku záhlaví. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library.csv"
Get-QueryCsv $ctx $container $blob "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'" $false

```

### <a name="net"></a>[.NET](#tab/dotnet)

Asynchronní metoda `BlobQuickQueryClient.QueryAsync` pošle dotaz do rozhraní API zrychlení dotazů a potom streamuje výsledky zpátky do aplikace jako objekt [streamu](/dotnet/api/system.io.stream) .

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        var options = new BlobQueryOptions() {
            InputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = headers },
            OutputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = true },
            ProgressHandler = new Progress<long>((finishedBytes) => Console.Error.WriteLine($"Data read: {finishedBytes}"))
        };
        options.ErrorHandler += (BlobQueryError err) => {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.Error.WriteLine($"Error: {err.Position}:{err.Name}:{err.Description}");
            Console.ResetColor();
        };
        // BlobDownloadInfo exposes a Stream that will make results available when received rather than blocking for the entire response.
        using (var reader = new StreamReader((await blob.QueryAsync(
                query,
                options)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture) { HasHeaderRecord = true }))
            {
                while (await parser.ReadAsync())
                {
                    Console.Out.WriteLine(String.Join(" ", parser.Context.Record));
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

Metoda `BlobQuickQueryClient.openInputStream()` pošle dotaz do rozhraní API pro zrychlení dotazování a poté streamuje výsledky zpátky do aplikace jako objekt, `InputStream` který lze číst stejně jako jakýkoli jiný objekt InputStream.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    DumpQueryCsv(blobClient, expression, true);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
        BlobQuerySerialization input = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');
        BlobQuerySerialization output = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(true)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
        Consumer<BlobQueryError> errorConsumer = System.out::println;
        Consumer<BlobQueryProgress> progressConsumer = progress -> System.out.println("total bytes read: " + progress.getBytesScanned());
        BlobQueryOptions queryOptions = new BlobQueryOptions(query)
            .setInputSerialization(input)
            .setOutputSerialization(output)
            .setErrorConsumer(errorConsumer)
            .setProgressConsumer(progressConsumer);            

        /* Open the query input stream. */
        InputStream stream = blobClient.openQueryInputStream(queryOptions).getValue();
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL.withHeader())) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
        e.printStackTrace(System.err);
    }
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_hemingway(blob: BlobClient):
    query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'"
    dump_query_csv(blob, query, False)

def dump_query_csv(blob: BlobClient, query: str, headers: bool):
    qa_reader = blob.query_blob(query, blob_format=DelimitedTextDialect(has_header=headers), on_error=report_error, encoding='utf-8')
    # records() returns a generator that will stream results as received. It will not block pending all results.
    csv_reader = csv.reader(qa_reader.records())
    for row in csv_reader:
        print("*".join(row))
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Tento příklad odešle dotaz do rozhraní API akcelerace dotazů a potom streamuje výsledky zpět. `blob`Objekt předaný do `queryHemingway` pomocné funkce je typu [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient). Další informace o tom, jak získat objekt [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) , najdete [v tématu rychlý Start: Správa objektů BLOB pomocí JavaScriptu v12 SDK v Node.js](storage-quickstart-blobs-nodejs.md).

```javascript
async function queryHemingway(blob)
{
    const query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await dumpQueryCsv(blob, query, false);
}

async function dumpQueryCsv(blob, query, headers)
{
    var response = await blob.query(query, {
        inputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: headers
        },
        outputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: true
        },
        onProgress: (progress) => console.log(`Data read: ${progress.loadedBytes}`),
        onError: (err) => console.error(`Error: ${err.position}:${err.name}:${err.description}`)});
    return new Promise(
        function (resolve, reject) {
            csv.parseStream(response.readableStreamBody)
                .on('data', row => console.log(row))
                .on('error', error => {
                    console.error(error);
                    reject(error);
                })
                .on('end', rowCount => resolve());
    });
}
```

---

## <a name="retrieve-specific-columns"></a>Načíst konkrétní sloupce

Můžete nastavit rozsah výsledků na podmnožinu sloupců. Tímto způsobem načtete pouze sloupce potřebné k provedení daného výpočtu. To zlepšuje výkon aplikace a snižuje náklady, protože v síti je přenášeno méně dat. 

Tento kód načte pouze `BibNum` sloupec pro všechny knihy v sadě dat. Používá také informace z řádku záhlaví ve zdrojovém souboru pro odkaz na sloupce v dotazu.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library-with-headers.csv"
Get-QueryCsv $ctx $container $blob "SELECT BibNum FROM BlobStorage" $true

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryBibNum(BlockBlobClient blob)
{
    string query = @"SELECT BibNum FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryBibNum(BlobClient blobClient)
{
    String expression = "SELECT BibNum FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_bibnum(blob: BlobClient):
    query = "SELECT BibNum FROM BlobStorage"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryBibNum(blob)
{
    const query = "SELECT BibNum FROM BlobStorage";
    await dumpQueryCsv(blob, query, true);
}
```

---

Následující kód kombinuje filtrování řádků a sloupcové projekce do stejného dotazu. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Get-QueryCsv $ctx $container $blob $query $true

Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
            FROM BlobStorage 
            WHERE ItemType IN 
                ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryDvds(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
        FROM BlobStorage 
        WHERE ItemType IN 
            ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryDvds(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                        "FROM BlobStorage " +
                        "WHERE ItemType IN " +
                        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_dvds(blob: BlobClient):
    query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType "\
        "FROM BlobStorage "\
        "WHERE ItemType IN "\
        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryDvds(blob)
{
    const query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                  "FROM BlobStorage " +
                  "WHERE ItemType IN " + 
                  " ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await dumpQueryCsv(blob, query, true);
}
```

---

## <a name="next-steps"></a>Další kroky

- [Akcelerace dotazů Azure Data Lake Storage](data-lake-storage-query-acceleration.md)
- [Referenční dokumentace jazyka SQL pro zrychlení dotazů](query-acceleration-sql-reference.md)