---
title: Správa Azure Data Lake Analytics pomocí sady Azure .NET SDK
description: Tento článek popisuje, jak pomocí sady Azure .NET SDK psát aplikace, které spravují Data Lake Analytics úlohy, zdroje dat, & uživatelé.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/18/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: a9ece3fe54ca8596f610439f8a0dd228ae1ef907
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220903"
---
# <a name="manage-azure-data-lake-analytics-a-net-app"></a>Správa služby Azure Data Lake Analytics pomocí aplikace .NET

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Tento článek popisuje, jak spravovat účty Azure Data Lake Analytics, zdroje dat, uživatele a úlohy pomocí aplikace napsané pomocí sady Azure .NET SDK. 

## <a name="prerequisites"></a>Předpoklady

* **Visual Studio 2015, Visual Studio 2013 Update 4 nebo Visual Studio 2012 s nainstalovaným Visual C++**.
* **Sada Microsoft Azure SDK pro .NET verze 2.5 nebo vyšší**.  Nainstalujte ji pomocí [Instalačního programu webové platformy](https://www.microsoft.com/web/downloads/platform.aspx).
* **Požadované balíčky NuGet**

### <a name="install-nuget-packages"></a>Instalace balíčků NuGet

|Balíček|Verze|
|-------|-------|
|[Microsoft. REST. ClientRuntime. Azure. Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)| 2.3.1|
|[Microsoft.Azure.Management.DataLake.Analytics](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Analytics)|3.0.0|
|[Microsoft.Azure.Management.DataLake.Store](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Store)|2.2.0|
|[Microsoft.Azure.Management.ResourceManager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)|1.6.0 – Preview|
|[Microsoft.Azure.Graph.RBAC](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)|3.4.0 – Preview|

Tyto balíčky můžete nainstalovat pomocí příkazového řádku NuGet pomocí následujících příkazů:

```powershell
Install-Package -Id Microsoft.Rest.ClientRuntime.Azure.Authentication  -Version 2.3.1
Install-Package -Id Microsoft.Azure.Management.DataLake.Analytics  -Version 3.0.0
Install-Package -Id Microsoft.Azure.Management.DataLake.Store  -Version 2.2.0
Install-Package -Id Microsoft.Azure.Management.ResourceManager  -Version 1.6.0-preview
Install-Package -Id Microsoft.Azure.Graph.RBAC -Version 3.4.0-preview
```

## <a name="common-variables"></a>Společné proměnné

```csharp
string subid = "<Subscription ID>"; // Subscription ID (a GUID)
string tenantid = "<Tenant ID>"; // AAD tenant ID or domain. For example, "contoso.onmicrosoft.com"
string rg == "<value>"; // Resource  group name
string clientid = "1950a258-227b-4e31-a9cf-717495945fc2"; // Sample client ID (this will work, but you should pick your own)
```

## <a name="authentication"></a>Authentication

Máte k dispozici několik možností pro přihlášení k Azure Data Lake Analytics. Následující fragment kódu ukazuje příklad ověřování pomocí interaktivního ověřování uživatelů pomocí automaticky otevíraného okna.

``` csharp
using System;
using System.IO;
using System.Threading;
using System.Security.Cryptography.X509Certificates;

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Analytics;
using Microsoft.Azure.Management.DataLake.Analytics.Models;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Azure.Graph.RBAC;

public static Program
{
   public static string TENANT = "microsoft.onmicrosoft.com";
   public static string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
   public static System.Uri ARM_TOKEN_AUDIENCE = new System.Uri( @"https://management.core.windows.net/");
   public static System.Uri ADL_TOKEN_AUDIENCE = new System.Uri( @"https://datalake.azure.net/" );
   public static System.Uri GRAPH_TOKEN_AUDIENCE = new System.Uri( @"https://graph.windows.net/" );

   static void Main(string[] args)
   {
      string MY_DOCUMENTS= System.Environment.GetFolderPath( System.Environment.SpecialFolder.MyDocuments);
      string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");

      var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
      var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
      var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
      var graphCreds = GetCreds_User_Popup(TENANT, GRAPH_TOKEN_AUDIENCE, CLIENTID, tokenCache);
   }
}
```

Zdrojový kód pro **GetCreds_User_Popup** a kód pro další možnosti ověřování jsou pokryté v části [Možnosti ověřování Data Lake Analytics .NET](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options) .


## <a name="create-the-client-management-objects"></a>Vytvoření objektů správy klienta

``` csharp
var resourceManagementClient = new ResourceManagementClient(armCreds) { SubscriptionId = subid };

var adlaAccountClient = new DataLakeAnalyticsAccountManagementClient(armCreds);
adlaAccountClient.SubscriptionId = subid;

var adlsAccountClient = new DataLakeStoreAccountManagementClient(armCreds);
adlsAccountClient.SubscriptionId = subid;

var adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(adlCreds);
var adlaJobClient = new DataLakeAnalyticsJobManagementClient(adlCreds);

var adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(adlCreds);

var  graphClient = new GraphRbacManagementClient(graphCreds);
graphClient.TenantID = domain;

```

## <a name="manage-accounts"></a>Správa účtů

### <a name="create-an-azure-resource-group"></a>Vytvoření skupiny prostředků Azure

Pokud jste ho ještě nevytvořili, musíte mít skupinu prostředků Azure, která by vytvořila vaše Data Lake Analytics komponenty. Budete potřebovat přihlašovací údaje pro ověřování, ID předplatného a umístění. Následující kód ukazuje, jak vytvořit skupinu prostředků:

``` csharp
var resourceGroup = new ResourceGroup { Location = location };
resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, rg);
```

Další informace najdete v tématu skupiny prostředků Azure a Data Lake Analytics.

### <a name="create-a-data-lake-store-account"></a>Vytvoření účtu Data Lake Store

Účet ADLA se někdy vyžaduje pro účet ADLS. Pokud ho ještě nemáte k použití, můžete ho vytvořit pomocí následujícího kódu:

``` csharp
var new_adls_params = new DataLakeStoreAccount(location: _location);
adlsAccountClient.Account.Create(rg, adls, new_adls_params);
```

### <a name="create-a-data-lake-analytics-account"></a>Vytvoření účtu Data Lake Analytics

Následující kód vytvoří účet ADLS.

``` csharp
var new_adla_params = new DataLakeAnalyticsAccount()
{
   DefaultDataLakeStoreAccount = adls,
   Location = location
};

adlaClient.Account.Create(rg, adla, new_adla_params);
```

### <a name="list-data-lake-store-accounts"></a>Výpis Data Lake Store účtů

``` csharp
var adlsAccounts = adlsAccountClient.Account.List().ToList();
foreach (var adls in adlsAccounts)
{
   Console.WriteLine($"ADLS: {0}", adls.Name);
}
```

### <a name="list-data-lake-analytics-accounts"></a>Výpis Data Lake Analytics účtů

``` csharp
var adlaAccounts = adlaClient.Account.List().ToList();

for (var adla in AdlaAccounts)
{
   Console.WriteLine($"ADLA: {0}, adla.Name");
}
```

### <a name="checking-if-an-account-exists"></a>Kontroluje se, jestli existuje účet.

``` csharp
bool exists = adlaClient.Account.Exists(rg, adla));
```

### <a name="get-information-about-an-account"></a>Získat informace o účtu

``` csharp
bool exists = adlaClient.Account.Exists(rg, adla));
if (exists)
{
   var adla_accnt = adlaClient.Account.Get(rg, adla);
}
```

### <a name="delete-an-account"></a>Odstranění účtu

``` csharp
if (adlaClient.Account.Exists(rg, adla))
{
   adlaClient.Account.Delete(rg, adla);
}
```

### <a name="get-the-default-data-lake-store-account"></a>Získat výchozí účet Data Lake Store

Každý Data Lake Analytics účet vyžaduje výchozí účet Data Lake Store. Pomocí tohoto kódu můžete určit výchozí účet úložiště pro účet Analytics.

``` csharp
if (adlaClient.Account.Exists(rg, adla))
{
  var adla_accnt = adlaClient.Account.Get(rg, adla);
  string def_adls_account = adla_accnt.DefaultDataLakeStoreAccount;
}
```

## <a name="manage-data-sources"></a>Správa zdrojů dat

Data Lake Analytics aktuálně podporuje následující zdroje dat:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Účet služby Azure Storage](../storage/common/storage-introduction.md)

### <a name="link-to-an-azure-storage-account"></a>Odkaz na účet Azure Storage

Můžete vytvořit odkazy na účty Azure Storage.

``` csharp
string storage_key = "xxxxxxxxxxxxxxxxxxxx";
string storage_account = "mystorageaccount";
var addParams = new AddStorageAccountParameters(storage_key);            
adlaClient.StorageAccounts.Add(rg, adla, storage_account, addParams);
```

### <a name="list-azure-storage-data-sources"></a>Seznam Azure Storage zdrojů dat

``` csharp
var stg_accounts = adlaAccountClient.StorageAccounts.ListByAccount(rg, adla);

if (stg_accounts != null)
{
  foreach (var stg_account in stg_accounts)
  {
      Console.WriteLine($"Storage account: {0}", stg_account.Name);
  }
}
```

### <a name="list-data-lake-store-data-sources"></a>Seznam Data Lake Store zdrojů dat

``` csharp
var adls_accounts = adlsClient.Account.List();

if (adls_accounts != null)
{
  foreach (var adls_accnt in adls_accounts)
  {
      Console.WriteLine($"ADLS account: {0}", adls_accnt.Name);
  }
}
```

### <a name="upload-and-download-folders-and-files"></a>Nahrávání a stahování složek a souborů

K nahrání a stažení jednotlivých souborů nebo složek z Azure do místního počítače můžete použít Data Lake Store objekt správy klienta souborového systému, a to pomocí následujících metod:

- UploadFolder
- UploadFile
- DownloadFolder
- DownloadFile

První parametr pro tyto metody je název účtu Data Lake Store následovaný parametry pro zdrojovou cestu a cílovou cestu.

Následující příklad ukazuje, jak stáhnout složku v Data Lake Store.

``` csharp
adlsFileSystemClient.FileSystem.DownloadFolder(adls, sourcePath, destinationPath);
```

### <a name="create-a-file-in-a-data-lake-store-account"></a>Vytvoření souboru v účtu Data Lake Store

``` csharp
using (var memstream = new MemoryStream())
{
   using (var sw = new StreamWriter(memstream, UTF8Encoding.UTF8))
   {
      sw.WriteLine("Hello World");
      sw.Flush();
      
      memstream.Position = 0;

      adlsFileSystemClient.FileSystem.Create(adls, "/Samples/Output/randombytes.csv", memstream);
   }
}
```

### <a name="verify-azure-storage-account-paths"></a>Ověřit Azure Storage cesty k účtu

Následující kód zkontroluje, zda Azure Storage účet (storageAccntName) existuje v účtu Data Lake Analytics (analyticsAccountName) a zda kontejner (ContainerName) existuje v účtu Azure Storage.

``` csharp
string storage_account = "mystorageaccount";
string storage_container = "mycontainer";
bool accountExists = adlaClient.Account.StorageAccountExists(rg, adla, storage_account));
bool containerExists = adlaClient.Account.StorageContainerExists(rg, adla, storage_account, storage_container));
```

## <a name="manage-catalog-and-jobs"></a>Správa katalogu a úloh

Objekt DataLakeAnalyticsCatalogManagementClient poskytuje metody pro správu SQL Database, která je k dispozici pro každý účet Azure Data Lake Analytics. DataLakeAnalyticsJobManagementClient poskytuje metody pro odesílání a správu úloh spuštěných v databázi pomocí skriptů U-SQL.

### <a name="list-databases-and-schemas"></a>Výpis databází a schémat

Mezi několik věcí, které můžete uvést, jsou nejběžnější databáze a jejich schéma. Následující kód získá kolekci databází a potom vytvoří výčet schématu pro každou databázi.

``` csharp
var databases = adlaCatalogClient.Catalog.ListDatabases(adla);
foreach (var db in databases)
{
  Console.WriteLine($"Database: {db.Name}");
  Console.WriteLine(" - Schemas:");
  var schemas = adlaCatalogClient.Catalog.ListSchemas(adla, db.Name);
  foreach (var schm in schemas)
  {
      Console.WriteLine($"\t{schm.Name}");
  }
}
```

### <a name="list-table-columns"></a>Seznam sloupců tabulky

Následující kód ukazuje, jak získat přístup k databázi pomocí klienta správy katalogu Data Lake Analytics pro zobrazení seznamu sloupců v zadané tabulce.

```csharp
var tbl = adlaCatalogClient.Catalog.GetTable(adla, "master", "dbo", "MyTableName");
IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

foreach (USqlTableColumn utc in columns)
{
  Console.WriteLine($"\t{utc.Name}");
}
```

### <a name="submit-a-u-sql-job"></a>Odeslání úlohy U-SQL

Následující kód ukazuje, jak použít klienta správy úloh Data Lake Analytics k odeslání úlohy.

``` csharp
string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
Stream scriptStrm = adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
string scriptTxt = string.Empty;
using (StreamReader sr = new StreamReader(scriptStrm))
{
    scriptTxt = sr.ReadToEnd();
}

var jobName = "SR_Wikipedia";
var jobId = Guid.NewGuid();
var properties = new USqlJobProperties(scriptTxt);
var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
var jobInfo = adlaJobClient.Job.Create(adla, jobId, parameters);
Console.WriteLine($"Job {jobName} submitted.");
```

### <a name="list-failed-jobs"></a>Vypsat neúspěšné úlohy

Následující kód obsahuje informace o úlohách, které selhaly.

```csharp
var odq = new ODataQuery<JobInformation> { Filter = "result eq 'Failed'" };
var jobs = adlaJobClient.Job.List(adla, odq);
foreach (var j in jobs)
{
   Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
}
```

### <a name="list-pipelines"></a>Seznam kanálů

Následující kód uvádí informace o jednotlivých kanálech úloh odeslaných do účtu.

``` csharp
var pipelines = adlaJobClient.Pipeline.List(adla);
foreach (var p in pipelines)
{
   Console.WriteLine($"Pipeline: {p.Name}\t{p.PipelineId}\t{p.LastSubmitTime}");
}
```

### <a name="list-recurrences"></a>Seznam opakování

Následující kód uvádí informace o každém opakování úloh odeslaných na účet.

``` csharp
var recurrences = adlaJobClient.Recurrence.List(adla);
foreach (var r in recurrences)
{
   Console.WriteLine($"Recurrence: {r.Name}\t{r.RecurrenceId}\t{r.LastSubmitTime}");
}
```

## <a name="common-graph-scenarios"></a>Běžné scénáře grafů

### <a name="look-up-user-in-the-aad-directory"></a>Vyhledat uživatele v adresáři AAD

``` csharp
var userinfo = graphClient.Users.Get( "bill@contoso.com" );
```

### <a name="get-the-objectid-of-a-user-in-the-aad-directory"></a>Získá ObjectId uživatele v adresáři AAD.

``` csharp
var userinfo = graphClient.Users.Get( "bill@contoso.com" );
Console.WriteLine( userinfo.ObjectId )
```

## <a name="manage-compute-policies"></a>Správa výpočetních zásad

Objekt DataLakeAnalyticsAccountManagementClient poskytuje metody pro správu výpočetních zásad pro účet Data Lake Analytics.

### <a name="list-compute-policies"></a>Vypsat výpočetní zásady

Následující kód načte seznam výpočetních zásad pro účet Data Lake Analytics.

``` csharp
var policies = adlaAccountClient.ComputePolicies.ListByAccount(rg, adla);
foreach (var p in policies)
{
   Console.WriteLine($"Name: {p.Name}\tType: {p.ObjectType}\tMax AUs / job: {p.MaxDegreeOfParallelismPerJob}\tMin priority / job: {p.MinPriorityPerJob}");
}
```

### <a name="create-a-new-compute-policy"></a>Vytvořit nové výpočetní zásady

Následující kód vytvoří novou výpočetní zásadu pro účet Data Lake Analytics, nastaví maximální jednotky Austrálie dostupné pro zadaného uživatele na 50 a minimální prioritu úlohy na 250.

``` csharp
var userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde";
var newPolicyParams = new ComputePolicyCreateOrUpdateParameters(userAadObjectId, "User", 50, 250);
adlaAccountClient.ComputePolicies.CreateOrUpdate(rg, adla, "GaryMcDaniel", newPolicyParams);
```

## <a name="next-steps"></a>Další kroky

* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Správa Azure Data Lake Analytics pomocí Azure Portal](data-lake-analytics-manage-use-portal.md)
* [Monitorování úloh Azure Data Lake Analytics a odstraňování potíží pomocí webu Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)