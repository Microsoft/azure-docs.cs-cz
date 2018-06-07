---
title: Správa Azure Data Lake Analytics pomocí sady Azure .NET SDK
description: Tento článek popisuje, jak používat Azure .net SDK k zápisu aplikace, které spravovat úlohy Data Lake Analytics, zdroje dat a uživatelů.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: 811d172d-9873-4ce9-a6d5-c1a26b374c79
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/18/2017
ms.openlocfilehash: dc49bb9b5461a4e8eb3573877276f5876e12f340
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625116"
---
# <a name="manage-azure-data-lake-analytics-a-net-app"></a>Správa Azure Data Lake Analytics aplikace .NET
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Tento článek popisuje, jak pro správu účtů Azure Data Lake Analytics, zdroje dat, uživatelů a úloh pomocí aplikace napsané pomocí .NET SDK služby Azure. 

## <a name="prerequisites"></a>Požadavky

* **Visual Studio 2015, Visual Studio 2013 Update 4 nebo Visual Studio 2012 s nainstalovaným Visual C++**.
* **Sada Microsoft Azure SDK pro .NET verze 2.5 nebo vyšší**.  Nainstalujte ji pomocí [Instalačního programu webové platformy](http://www.microsoft.com/web/downloads/platform.aspx).
* **Balíčky požadované NuGet**

### <a name="install-nuget-packages"></a>Instalace balíčků NuGet

|Balíček|Verze|
|-------|-------|
|[Microsoft.Rest.ClientRuntime.Azure.Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)| 2.3.1|
|[Microsoft.Azure.Management.DataLake.Analytics](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Analytics)|3.0.0|
|[Microsoft.Azure.Management.DataLake.Store](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Store)|2.2.0|
|[Microsoft.Azure.Management.ResourceManager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)|1.6.0-Preview|
|[Microsoft.Azure.Graph.RBAC](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)|3.4.0-Preview|

Tyto balíčky NuGet příkazového řádku můžete nainstalovat pomocí následujících příkazů:

```
Install-Package -Id Microsoft.Rest.ClientRuntime.Azure.Authentication  -Version 2.3.1
Install-Package -Id Microsoft.Azure.Management.DataLake.Analytics  -Version 3.0.0
Install-Package -Id Microsoft.Azure.Management.DataLake.Store  -Version 2.2.0
Install-Package -Id Microsoft.Azure.Management.ResourceManager  -Version 1.6.0-preview
Install-Package -Id Microsoft.Azure.Graph.RBAC -Version 3.4.0-preview
```

## <a name="common-variables"></a>Běžné proměnné

``` csharp
string subid = "<Subscription ID>"; // Subscription ID (a GUID)
string tenantid = "<Tenant ID>"; // AAD tenant ID or domain. For example, "contoso.onmicrosoft.com"
string rg == "<value>"; // Resource  group name
string clientid = "1950a258-227b-4e31-a9cf-717495945fc2"; // Sample client ID (this will work, but you should pick your own)
```

## <a name="authentication"></a>Authentication

Máte několik možností pro přihlášení k Azure Data Lake Analytics. Následující fragment kódu ukazuje příklad ověření s interaktivním ověřování uživatelů s automaticky.

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

Zdrojový kód pro **GetCreds_User_Popup** a kód pro další možnosti pro ověřování jsou popsané v [možnosti ověřování Data Lake Analytics .NET](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options)


## <a name="create-the-client-management-objects"></a>Vytvoření klienta objekty pro správu

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

Pokud jste již žádný nevytvořili, musí mít skupiny prostředků Azure k vytvoření komponentů vaše Data Lake Analytics. Budete potřebovat pověření pro ověření, ID předplatného a umístění. Následující kód ukazuje, jak vytvořit skupinu prostředků:

``` csharp
var resourceGroup = new ResourceGroup { Location = location };
resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, rg);
```
Další informace najdete v tématu [skupiny prostředků Azure a Data Lake Analytics](#Azure-Resource-Groups-and-Data-Lake-Analytics).

### <a name="create-a-data-lake-store-account"></a>Vytvoření účtu Data Lake Store

Někdy ADLA účet vyžaduje účtu ADLS. Pokud chcete používat nemáte, můžete vytvořit jeden následujícím kódem:

``` csharp
var new_adls_params = new DataLakeStoreAccount(location: _location);
adlsAccountClient.Account.Create(rg, adls, new_adls_params);
```

### <a name="create-a-data-lake-analytics-account"></a>Vytvoření účtu Data Lake Analytics

Následující kód vytvoří účtu ADLS

``` csharp
var new_adla_params = new DataLakeAnalyticsAccount()
{
   DefaultDataLakeStoreAccount = adls,
   Location = location
};

adlaClient.Account.Create(rg, adla, new_adla_params);
```

### <a name="list-data-lake-store-accounts"></a>Účty seznamu Data Lake Store

``` csharp
var adlsAccounts = adlsAccountClient.Account.List().ToList();
foreach (var adls in adlsAccounts)
{
   Console.WriteLine($"ADLS: {0}", adls.Name);
}
```

### <a name="list-data-lake-analytics-accounts"></a>Účty seznamu Data Lake Analytics

``` csharp
var adlaAccounts = adlaClient.Account.List().ToList();

for (var adla in AdlaAccounts)
{
   Console.WriteLine($"ADLA: {0}, adla.Name");
}
```

### <a name="checking-if-an-account-exists"></a>Kontrola, jestli účet existuje

``` csharp
bool exists = adlaClient.Account.Exists(rg, adla));
```

### <a name="get-information-about-an-account"></a>Získání informací o účtu

``` csharp
bool exists = adlaClient.Account.Exists(rg, adla));
if (exists)
{
   var adla_accnt = adlaClient.Account.Get(rg, adla);
}
```

### <a name="delete-an-account"></a>Odstranit účet

``` csharp
if (adlaClient.Account.Exists(rg, adla))
{
   adlaClient.Account.Delete(rg, adla);
}
```

### <a name="get-the-default-data-lake-store-account"></a>Získání výchozího účtu Data Lake Store

Každý účet Data Lake Analytics vyžaduje výchozího účtu Data Lake Store. Pomocí tohoto kódu k určení výchozí účet úložiště pro účet Analytics.

``` csharp
if (adlaClient.Account.Exists(rg, adla))
{
  var adla_accnt = adlaClient.Account.Get(rg, adla);
  string def_adls_account = adla_accnt.DefaultDataLakeStoreAccount;
}
```

## <a name="manage-data-sources"></a>Správa zdrojů dat

Data Lake Analytics teď podporuje následující zdroje dat:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Účet úložiště Azure](../storage/common/storage-introduction.md)

### <a name="link-to-an-azure-storage-account"></a>Odkaz na účet úložiště Azure

Můžete vytvořit odkazy na účty Azure Storage.

``` csharp
string storage_key = "xxxxxxxxxxxxxxxxxxxx";
string storage_account = "mystorageaccount";
var addParams = new AddStorageAccountParameters(storage_key);            
adlaClient.StorageAccounts.Add(rg, adla, storage_account, addParams);
```

### <a name="list-azure-storage-data-sources"></a>Seznam zdrojů dat úložiště Azure

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

### <a name="list-data-lake-store-data-sources"></a>Zdroje dat seznamu Data Lake Store

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
Objekt správy systému klienta služby Data Lake Store souborů můžete použít k odesílání a stahování jednotlivé soubory nebo složky z Azure do místního počítače, pomocí následujících metod:

- UploadFolder
- UploadFile
- DownloadFolder
- DownloadFile

První parametr pro tyto metody je název účtu Data Lake Store, ve následuje parametry pro zdrojovou cestu a cílovou cestu.

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

### <a name="verify-azure-storage-account-paths"></a>Ověření cesty účet úložiště Azure
Následující kód ověří, jestli účet služby Azure Storage (storageAccntName) existuje v účtu Data Lake Analytics (analyticsAccountName), a Pokud kontejner (containerName) existuje v účtu úložiště Azure.

``` csharp
string storage_account = "mystorageaccount";
string storage_container = "mycontainer";
bool accountExists = adlaClient.Account.StorageAccountExists(rg, adla, storage_account));
bool containerExists = adlaClient.Account.StorageContainerExists(rg, adla, storage_account, storage_container));
```

## <a name="manage-catalog-and-jobs"></a>Správa katalogu a úlohy
Objekt DataLakeAnalyticsCatalogManagementClient poskytuje metody pro správu databáze SQL, zadaná pro každý účet Azure Data Lake Analytics. DataLakeAnalyticsJobManagementClient poskytuje metody k odeslání a spravovat úlohy spustit na databázi s skriptů U-SQL.

### <a name="list-databases-and-schemas"></a>Seznam databází a schémat.
Mezi několik věcí, které můžete vytvořit seznam nejobvyklejší jsou databáze a jejich schématu. Následující kód získá kolekci databází a poté zobrazí schéma pro každou databázi.

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
Následující kód ukazuje, jak získat přístup k databázi pomocí katalogu Data Lake Analytics správy klienta seznam sloupců zadané tabulky.

``` csharp
var tbl = adlaCatalogClient.Catalog.GetTable(adla, "master", "dbo", "MyTableName");
IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

foreach (USqlTableColumn utc in columns)
{
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

}
```

### <a name="list-failed-jobs"></a>Seznam neúspěšných úloh
Následující kód uvádí informace o úlohách, které se nezdařilo.

``` csharp
var odq = new ODataQuery<JobInformation> { Filter = "result eq 'Failed'" };
var jobs = adlaJobClient.Job.List(adla, odq);
foreach (var j in jobs)
{
   Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
}
```

### <a name="list-pipelines"></a>Seznam kanálů
Následující kód uvádí informace o jednotlivých kanálu úlohy, odeslané do účtu.

``` csharp
var pipelines = adlaJobClient.Pipeline.List(adla);
foreach (var p in pipelines)
{
   Console.WriteLine($"Pipeline: {p.Name}\t{p.PipelineId}\t{p.LastSubmitTime}");
}
```

### <a name="list-recurrences"></a>Opakování seznamu
Následující kód uvádí informace o každém opakování úlohy, odeslané do účtu.

``` csharp
var recurrences = adlaJobClient.Recurrence.List(adla);
foreach (var r in recurrences)
{
   Console.WriteLine($"Recurrence: {r.Name}\t{r.RecurrenceId}\t{r.LastSubmitTime}");
}
```

## <a name="common-graph-scenarios"></a>Běžné scénáře grafu

### <a name="look-up-user-in-the-aad-directory"></a>Vyhledání uživatele v adresáři AAD

``` csharp
var userinfo = graphClient.Users.Get( "bill@contoso.com" );
```

### <a name="get-the-objectid-of-a-user-in-the-aad-directory"></a>Získejte ObjectId uživatele v adresáři AAD

``` csharp
var userinfo = graphClient.Users.Get( "bill@contoso.com" );
Console.WriteLine( userinfo.ObjectId )
```

## <a name="manage-compute-policies"></a>Správa zásad výpočetní
Objekt DataLakeAnalyticsAccountManagementClient poskytuje metody pro správu výpočetních zásady pro účet Data Lake Analytics.

### <a name="list-compute-policies"></a>Seznam výpočetní zásad
Následující kód načte seznam výpočetní zásad pro účet Data Lake Analytics.

``` csharp
var policies = adlaAccountClient.ComputePolicies.ListByAccount(rg, adla);
foreach (var p in policies)
{
   Console.WriteLine($"Name: {p.Name}\tType: {p.ObjectType}\tMax AUs / job: {p.MaxDegreeOfParallelismPerJob}\tMin priority / job: {p.MinPriorityPerJob}");
}
```

### <a name="create-a-new-compute-policy"></a>Vytvořit novou zásadu výpočetní
Následující kód vytvoří novou zásadu výpočetní pro účet Data Lake Analytics, nastavení maximální Austrálie dostupná pro zadaného uživatele na 50 a priority minimální úloh na 250.

``` csharp
var userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde";
var newPolicyParams = new ComputePolicyCreateOrUpdateParameters(userAadObjectId, "User", 50, 250);
adlaAccountClient.ComputePolicies.CreateOrUpdate(rg, adla, "GaryMcDaniel", newPolicyParams);
```

## <a name="next-steps"></a>Další postup
* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Správa Azure Data Lake Analytics pomocí webu Azure Portal](data-lake-analytics-manage-use-portal.md)
* [Monitorování úloh Azure Data Lake Analytics a odstraňování potíží pomocí webu Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
