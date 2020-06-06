---
title: Filtrování dat pomocí Azure Data Lake Storage akcelerace dotazů (Preview) | Microsoft Docs
description: K načtení podmnožiny dat z vašeho účtu úložiště použijte akceleraci dotazů (Preview).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.openlocfilehash: cc9235f07c0829abfb8be42e83d05d8428bc1806
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465860"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration-preview"></a>Filtrování dat pomocí Azure Data Lake Storage akcelerace dotazů (Preview)

V tomto článku se dozvíte, jak pomocí akcelerace dotazů (Preview) načíst podmnožinu dat z vašeho účtu úložiště. 

Akcelerace dotazů (Preview) je nová funkce pro Azure Data Lake Storage, která umožňuje aplikacím a analytickým architekturám významně optimalizovat zpracování dat tím, že načte jenom data, která potřebují k provedení dané operace. Další informace najdete v tématu [akcelerace dotazů Azure Data Lake Storage (Preview)](data-lake-storage-query-acceleration.md).

> [!NOTE]
> Funkce zrychlení dotazů je ve verzi Public Preview a je dostupná v oblastech Kanada – střed a Francie – střed. Chcete-li zkontrolovat omezení, přečtěte si článek [známé problémy](data-lake-storage-known-issues.md) . Pokud se chcete zaregistrovat ve verzi Preview, podívejte se na [Tento formulář](https://aka.ms/adls/qa-preview-signup).  

## <a name="prerequisites"></a>Požadavky

### <a name="net"></a>[.NET](#tab/dotnet)

- Pokud chcete získat přístup k Azure Storage, budete potřebovat předplatné Azure. Pokud ještě předplatné nemáte, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

- Účet úložiště pro **obecné účely v2** . viz [Vytvoření účtu úložiště](../common/storage-quickstart-create-account.md).

- [sada .NET SDK](https://dotnet.microsoft.com/download). 

### <a name="java"></a>[Java](#tab/java)

- Pokud chcete získat přístup k Azure Storage, budete potřebovat předplatné Azure. Pokud ještě předplatné nemáte, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

- Účet úložiště pro **obecné účely v2** . viz [Vytvoření účtu úložiště](../common/storage-quickstart-create-account.md).

- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) verze 8 nebo vyšší.

- [Apache Maven](https://maven.apache.org/download.cgi) 

  > [!NOTE] 
  > V tomto článku se předpokládá, že jste vytvořili projekt Java pomocí Apache Maven. Příklad toho, jak vytvořit projekt pomocí Apache Maven, najdete v tématu [Nastavení](storage-quickstart-blobs-java.md#setting-up).
  
---

## <a name="install-packages"></a>Nainstalovat balíčky 

### <a name="net"></a>[.NET](#tab/dotnet)

1. Stáhněte si balíčky pro zrychlení dotazů. Komprimovaný soubor zip, který obsahuje tyto balíčky, můžete získat pomocí tohoto odkazu: [https://aka.ms/adls/qqsdk/.net](https://aka.ms/adls/qqsdk/.net) . 

2. Extrahujte obsah tohoto souboru do adresáře projektu.

3. V textovém editoru otevřete soubor projektu (*. csproj*) a přidejte tyto odkazy na balíčky uvnitř \<Project\> elementu.

   ```xml
   <ItemGroup>
       <PackageReference Include="Azure.Storage.Blobs" Version="12.5.0-preview.1" />
       <PackageReference Include="Azure.Storage.Common" Version="12.4.0-preview.1" />
       <PackageReference Include="Azure.Storage.QuickQuery" Version="12.0.0-preview.1" />
   </ItemGroup>
   ```

4. Obnovte balíčky sady SDK pro Preview. Tento ukázkový příkaz obnoví balíčky sady SDK verze Preview pomocí `dotnet restore` příkazu. 

   ```console
   dotnet restore --source C:\Users\contoso\myProject
   ```

5. Obnovte všechny ostatní závislosti z veřejného úložiště NuGet.

   ```console
   dotnet restore
   ```

### <a name="java"></a>[Java](#tab/java)

1. Vytvořte adresář v kořenu projektu. Kořenový adresář je adresář, který obsahuje soubor **pom. XML** .

   > [!NOTE]
   > V příkladech v tomto článku se předpokládá, že název adresáře je **lib**.

2. Stáhněte si balíčky pro zrychlení dotazů. Komprimovaný soubor zip, který obsahuje tyto balíčky, můžete získat pomocí tohoto odkazu: [https://aka.ms/adls/qqsdk/java](https://aka.ms/adls/qqsdk/java) . 

3. Extrahujte soubory v tomto souboru zip do adresáře, který jste vytvořili. V našem příkladu má tento adresář název **lib**. 

4. V textovém editoru otevřete soubor *pom. XML* . Přidejte následující prvky závislosti do skupiny závislostí. 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-core-http-netty</artifactId>
      <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>org.apache.avro</groupId>
      <artifactId>avro</artifactId>
      <version>1.9.2</version>
   </dependency>
   <dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-csv</artifactId>
    <version>1.8</version>
   </dependency>
   <!-- Local dependencies -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-blob</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-blob-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-common</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-common-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-quickquery</artifactId>
       <version>12.0.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-quickquery-12.0.0-beta.1.jar</systemPath>
   </dependency>
   ```

---

## <a name="add-statements"></a>Přidat příkazy


### <a name="net"></a>[.NET](#tab/dotnet)

Přidejte tyto `using` příkazy do horní části souboru kódu.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using Azure.Storage.QuickQuery;
using Azure.Storage.QuickQuery.Models;
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
using System.Threading;
using System.Linq;
```

### <a name="java"></a>[Java](#tab/java)

Přidejte tyto `import` příkazy do horní části souboru kódu.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import com.azure.storage.quickquery.*;
import com.azure.storage.quickquery.models.*;
import java.io.*;
import org.apache.commons.csv.*;
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Načtení dat pomocí filtru

Pomocí jazyka SQL můžete zadat predikáty filtru řádků a projekce sloupců v požadavku na zrychlení dotazu. Následující kód odešle dotaz do souboru CSV v úložišti a vrátí všechny řádky dat, kde třetí sloupec odpovídá hodnotě `Hemingway, Ernest` . 

- V dotazu SQL se klíčové slovo `BlobStorage` používá k označení souboru, na který se dotazuje.

- Odkazy na sloupce jsou zadány jako `_N` v případě prvního sloupce `_1` . Pokud zdrojový soubor obsahuje řádek záhlaví, můžete odkazovat na sloupce podle názvu, který je uveden v řádku záhlaví. 

### <a name="net"></a>[.NET](#tab/dotnet)

Asynchronní metoda `BlobQuickQueryClient.QueryAsync` pošle dotaz do rozhraní API zrychlení dotazů a potom streamuje výsledky zpátky do aplikace jako objekt [streamu](https://docs.microsoft.com/dotnet/api/system.io.stream?view=netframework-4.8) .

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        using (var reader = new StreamReader((await blob.GetQuickQueryClient().QueryAsync(query,
                new CsvTextConfiguration() { HasHeaders = headers }, 
                new CsvTextConfiguration() { HasHeaders = false }, 
                new ErrorHandler(),
                new BlobRequestConditions(), 
                new ProgressHandler(),
                CancellationToken.None)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture) 
            { HasHeaderRecord = false }))
            {
                while (await parser.ReadAsync())
                {
                    parser.Context.Record.All(cell =>
                    {
                        Console.Out.Write(cell + "  ");
                        return true;
                    });
                    Console.Out.WriteLine();
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

class ErrorHandler : IBlobQueryErrorReceiver
{
    public void ReportError(BlobQueryError err)
    {
        Console.Error.WriteLine($"Error: {err.Name}:{ err.Description }");
    }
}

class ProgressHandler : IProgress<long>
{
    public void Report(long value)
    {
        Console.Error.WriteLine("Bytes scanned: " + value.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

Metoda `BlobQuickQueryClient.openInputStream()` pošle dotaz do rozhraní API pro zrychlení dotazování a poté streamuje výsledky zpátky do aplikace jako objekt, `InputStream` který lze číst stejně jako jakýkoli jiný objekt InputStream.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    DumpQueryCsv(blobClient, expression, false);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
    
        BlobQuickQueryDelimitedSerialization input = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');

        BlobQuickQueryDelimitedSerialization output = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(false)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
                
        BlobRequestConditions requestConditions = null;
        /* ErrorReceiver determines what to do on errors. */
        ErrorReceiver<BlobQuickQueryError> errorReceiver = System.out::println;

        /* ProgressReceiver details how to log progress*/
        com.azure.storage.common.ProgressReceiver progressReceiver = System.out::println;
    
        /* Create a query acceleration client to the blob. */
        BlobQuickQueryClient qqClient = new BlobQuickQueryClientBuilder(blobClient)
            .buildClient();
        /* Open the query input stream. */
        InputStream stream = qqClient.openInputStream(query, input, output, requestConditions, errorReceiver, progressReceiver);
            
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL)) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
    }
}
```

---

## <a name="retrieve-specific-columns"></a>Načíst konkrétní sloupce

Můžete nastavit rozsah výsledků na podmnožinu sloupců. Tímto způsobem načtete pouze sloupce potřebné k provedení daného výpočtu. To zlepšuje výkon aplikace a snižuje náklady, protože v síti je přenášeno méně dat. 

Tento kód načte pouze `PublicationYear` sloupec pro všechny knihy v sadě dat. Používá také informace z řádku záhlaví ve zdrojovém souboru pro odkaz na sloupce v dotazu.


### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryPublishDates(BlockBlobClient blob)
{
    string query = @"SELECT PublicationYear FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryPublishDates(BlobClient blobClient)
{
    String expression = "SELECT PublicationYear FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

Následující kód kombinuje filtrování řádků a sloupcové projekce do stejného dotazu. 

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryMysteryBooks(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryMysteryBooks(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

## <a name="next-steps"></a>Další kroky

- [Registrační formulář pro dotaz na zrychlení](https://aka.ms/adls/qa-preview-signup)    
- [Akcelerace dotazů Azure Data Lake Storage (Preview)](data-lake-storage-query-acceleration.md)
- [Referenční dokumentace jazyka SQL pro zrychlení dotazů (Preview)](query-acceleration-sql-reference.md)
