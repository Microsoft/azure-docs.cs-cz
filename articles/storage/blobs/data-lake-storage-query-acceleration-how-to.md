---
title: Filtrování dat pomocí akcelerace dotazu Azure Data Lake Storage (preview) | Dokumenty společnosti Microsoft
description: Pomocí akcelerace dotazu (preview) načtěte podmnožinu dat z účtu úložiště.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.openlocfilehash: ae3dfc7681ef0d8ce3fcf679bddbd0ff195f4e3b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771843"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration-preview"></a>Filtrování dat pomocí akcelerace dotazu Azure Data Lake Storage (preview)

Tento článek ukazuje, jak pomocí akcelerace dotazu (preview) načíst podmnožinu dat z vašeho účtu úložiště. 

Akcelerace dotazů (preview) je nová funkce pro Azure Data Lake Storage, která umožňuje aplikacím a analytickým rámcům výrazně optimalizovat zpracování dat načtením pouze dat, která potřebují k provedení dané operace. Další informace najdete v [tématu Azure Data Lake Storage Query Acceleration (preview).](data-lake-storage-query-acceleration.md)

> [!NOTE]
> Funkce akcelerace dotazu je ve verzi Public Preview a je k dispozici v oblastech Kanada – střed a Francie – střed. Omezení najdete v článku [Známé problémy.](data-lake-storage-known-issues.md) Chcete-li se zaregistrovat do náhledu, přečtěte [si tento formulář](https://aka.ms/adls/qa-preview-signup).  

## <a name="prerequisites"></a>Požadavky

### <a name="net"></a>[.NET](#tab/dotnet)

- Pro přístup k Azure Storage budete potřebovat předplatné Azure. Pokud ještě nemáte předplatné, vytvořte si před zahájením [bezplatný účet.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

- Účet úložiště **pro obecné účely v2.** viz [Vytvoření účtu úložiště](../common/storage-quickstart-create-account.md).

- [Sada SDK .NET .](https://dotnet.microsoft.com/download) 

### <a name="java"></a>[Java](#tab/java)

- Pro přístup k Azure Storage budete potřebovat předplatné Azure. Pokud ještě nemáte předplatné, vytvořte si před zahájením [bezplatný účet.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

- Účet úložiště **pro obecné účely v2.** viz [Vytvoření účtu úložiště](../common/storage-quickstart-create-account.md).

- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) verze 8 nebo vyšší.

- [Apache Maven](https://maven.apache.org/download.cgi). 

  > [!NOTE] 
  > Tento článek předpokládá, že jste vytvořili projekt Java pomocí Apache Maven. Příklad vytvoření projektu pomocí Apache Maven najdete v [tématu Nastavení](storage-quickstart-blobs-java.md#setting-up).
  
---

## <a name="install-packages"></a>Instalace balíčků 

### <a name="net"></a>[.NET](#tab/dotnet)

1. Stáhněte balíčky akcelerace dotazu. Komprimovaný soubor ZIP, který obsahuje tyto balíčky, [https://aka.ms/adls/qqsdk/.net](https://aka.ms/adls/qqsdk/.net)můžete získat pomocí tohoto odkazu: . 

2. Extrahujte obsah tohoto souboru do adresáře projektu.

3. Otevřete soubor projektu (*.csproj*) v textovém editoru \<a\> přidejte tyto odkazy na balíček uvnitř elementu Project.

   ```xml
   <ItemGroup>
       <PackageReference Include="Azure.Storage.Blobs" Version="12.5.0-preview.1" />
       <PackageReference Include="Azure.Storage.Common" Version="12.4.0-preview.1" />
       <PackageReference Include="Azure.Storage.QuickQuery" Version="12.0.0-preview.1" />
   </ItemGroup>
   ```

4. Obnovte balíčky sady Preview SDK. Tento příklad příkazu obnoví balíčky náhledu `dotnet restore` sady SDK pomocí příkazu. 

   ```console
   dotnet restore --source C:\Users\contoso\myProject
   ```

5. Obnovte všechny ostatní závislosti z veřejného úložiště NuGet.

   ```console
   dotnet restore
   ```

### <a name="java"></a>[Java](#tab/java)

1. Vytvořte adresář v kořenovém adresáři projektu. Kořenový adresář je adresář, který obsahuje soubor **pom.xml.**

   > [!NOTE]
   > Příklady v tomto článku předpokládají, že název adresáře je **lib**.

2. Stáhněte balíčky akcelerace dotazu. Komprimovaný soubor ZIP, který obsahuje tyto balíčky, [https://aka.ms/adls/qqsdk/java](https://aka.ms/adls/qqsdk/java)můžete získat pomocí tohoto odkazu: . 

3. Extrahujte soubory v tomto souboru ZIP do adresáře, který jste vytvořili. V našem příkladu se tento adresář nazývá **lib**. 

4. Otevřete soubor *pom.xml* v textovém editoru. Přidejte následující prvky závislostí do skupiny závislostí. 

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

Přidejte `using` tyto příkazy do horní části souboru kódu.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using Azure.Storage.QuickQuery;
using Azure.Storage.QuickQuery.Models;
```

Akcelerace dotazu načte data ve formátu CSV a Json. Proto nezapomeňte přidat pomocí příkazů pro všechny knihovny analýzy CSV nebo Json, které se rozhodnete použít. Příklady, které se zobrazí v tomto článku analyzovat soubor CSV pomocí knihovny [CsvHelper,](https://www.nuget.org/packages/CsvHelper/) která je k dispozici na NuGet. Proto bychom přidat `using` tyto příkazy do horní části souboru kódu.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

Chcete-li zkompilovat příklady uvedené v tomto `using` článku, budete také muset přidat tyto příkazy.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
using System.Threading;
using System.Linq;
```

### <a name="java"></a>[Java](#tab/java)

Přidejte `import` tyto příkazy do horní části souboru kódu.

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

Sql můžete použít k určení predikáty filtru řádků a projekce sloupců v požadavku akcelerace dotazu. Následující kód se dotazuje souboru CSV v úložišti a vrátí `Hemingway, Ernest`všechny řádky dat, kde třetí sloupec odpovídá hodnotě . 

- V dotazu SQL `BlobStorage` klíčové slovo slouží k označení souboru, který je dotazován.

- Odkazy na sloupce `_N` jsou určeny `_1`jako místo, kde je první sloupec . Pokud zdrojový soubor obsahuje řádek záhlaví, můžete odkazovat na sloupce podle názvu, který je zadán v řádku záhlaví. 

### <a name="net"></a>[.NET](#tab/dotnet)

Asynchronní `BlobQuickQueryClient.QueryAsync` metoda odešle dotaz do rozhraní API akcelerace dotazu a potom streamuje výsledky zpět do aplikace jako objekt [Stream.](https://docs.microsoft.com/dotnet/api/system.io.stream?view=netframework-4.8)

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
        Console.Error.WriteLine(String.Format("Error: {1}:{2}", err.Name, err.Description));
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

Metoda `BlobQuickQueryClient.openInputStream()` odešle dotaz do rozhraní API akcelerace dotazu a potom `InputStream` streamuje výsledky zpět do aplikace jako objekt, který lze číst jako jakýkoli jiný objekt InputStream.

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

Výsledky můžete oborovat na podmnožinu sloupců. Tímto způsobem načtete pouze sloupce potřebné k provedení daného výpočtu. To zlepšuje výkon aplikace a snižuje náklady, protože méně dat se přenáší v síti. 

Tento kód načte `PublicationYear` pouze sloupec pro všechny knihy v datové sadě. Používá také informace z řádku záhlaví ve zdrojovém souboru k odkazování na sloupce v dotazu.


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

Následující kód kombinuje filtrování řádků a projekce sloupců do stejného dotazu. 

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

- [Formulář pro zápis akcelerace dotazu](https://aka.ms/adls/queryaccelerationpreview)    
- [Akcelerace dotazu Azure Data Lake Storage (preview)](data-lake-storage-query-acceleration.md)
- [Referenční příručka jazyka SQL akcelerace dotazu (náhled)](query-acceleration-sql-reference.md)
- Odkaz na rozhraní REST API akcelerace dotazu
