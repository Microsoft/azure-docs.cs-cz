---
title: Povolení metrik úložiště na portálu Azure | Microsoft Docs
description: Postup povolení úložiště metriky pro služby objektů Blob, fronty, tabulky a souborů
services: storage
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: 0407adfc-2a41-4126-922d-b76e90b74563
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/14/2017
ms.author: rogarana
ms.openlocfilehash: 0caa4eff80877ad4bf8d501a276e82922b1a84c7
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="enabling-azure-storage-metrics-and-viewing-metrics-data"></a>Zapnutí metrik Azure Storage a prohlížení dat metrik
[!INCLUDE [storage-selector-portal-enable-and-view-metrics](../../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Přehled
Úložiště Metrics je povolena ve výchozím nastavení, když vytvoříte nový účet úložiště. Můžete nakonfigurovat monitorování prostřednictvím [portál Azure](https://portal.azure.com) nebo prostředí Windows PowerShell, nebo prostřednictvím kódu programu prostřednictvím knihovny klienta úložiště.

Můžete nakonfigurovat dobu uchování dat metrik: Toto období určuje, jak dlouho úložiště služby udržuje metriky a poplatky, že v prostoru třeba je uložit. Obvykle měli byste použít kratší doby uchování pro minutu metriky než hodinová metriky z důvodu významné přebytečné místo požadované pro minutu metriky. Vyberte dobu uchování tak, že máte dostatek času k analýze dat a stáhnout všechny metriky, které chcete zachovat pro offline analýzu nebo pro účely generování sestav. Mějte na paměti, že se vám také účtuje stahování metriky dat z vašeho účtu úložiště.

## <a name="how-to-enable-metrics-using-the-azure-portal"></a>Postup povolení metriky pomocí portálu Azure
Použijte následující postup metriky v povolit [portál Azure](https://portal.azure.com):

1. Přejděte na svůj účet úložiště.
1. Vyberte **diagnostiky** z **nabídky** podokně.
1. Ujistěte se, že **stav** je nastaven na **na**.
1. Vyberte metriky pro služby, které chcete monitorovat.
1. Zadejte zásady uchovávání informací a jak dlouho informuje zachovat metriky a protokolovat data.
1. Vyberte **Uložit**.

[Portál Azure](https://portal.azure.com) neumožňuje aktuálně nakonfigurovat minutu metriky v účtu úložiště; je nutné povolit minutu metriky pomocí prostředí PowerShell nebo prostřednictvím kódu programu.

## <a name="how-to-enable-metrics-using-powershell"></a>Postup povolení metriky pomocí prostředí PowerShell
Konfigurace úložiště metriky ve vašem účtu úložiště pomocí rutiny prostředí Azure PowerShell Get-AzureStorageServiceMetricsProperty načíst aktuální nastavení a rutinu Set-AzureStorageServiceMetricsProperty změnit aktuální nastavení můžete použít PowerShell na místním počítači.

Rutiny, které řídí metriky úložiště použijte následující parametry:

* MetricsType: možné hodnoty jsou hodin a minut.
* ServiceType: možné hodnoty jsou objekt Blob, fronty a tabulky.
* MetricsLevel: možné hodnoty jsou None, služby a ServiceAndApi.

Například následující příkaz přepne na minutu metriky pro služby objektů Blob v účtu úložiště výchozí s dobou uchování období nastavit na pět dní:

```powershell
Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`
```

Tento příkaz načte aktuální hodinové metriky úrovně a jejich uchovávání dny služby objektů blob na výchozí účet úložiště:

```powershell
Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob
```

Informace o tom, jak nakonfigurovat rutin prostředí Azure PowerShell k práci s předplatným Azure a jak vybrat výchozí účet úložiště, který chcete použít, najdete v tématu: [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

## <a name="how-to-enable-storage-metrics-programmatically"></a>Postup povolení metrik úložiště prostřednictvím kódu programu
Následující fragment C# ukazuje, jak povolit protokolování pro službu Blob pomocí klientské knihovny pro úložiště pro .NET a metriky:

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

// Create service client for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Enable Storage Analytics logging and set retention policy to 10 days.
ServiceProperties properties = new ServiceProperties();
properties.Logging.LoggingOperations = LoggingOperations.All;
properties.Logging.RetentionDays = 10;
properties.Logging.Version = "1.0";

// Configure service properties for metrics. Both metrics and logging must be set at the same time.
properties.HourMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.HourMetrics.RetentionDays = 10;
properties.HourMetrics.Version = "1.0";

properties.MinuteMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.MinuteMetrics.RetentionDays = 10;
properties.MinuteMetrics.Version = "1.0";

// Set the default service version to be used for anonymous requests.
properties.DefaultServiceVersion = "2015-04-05";

// Set the service properties.
blobClient.SetServiceProperties(properties);
```

## <a name="viewing-storage-metrics"></a>Zobrazení metriky úložiště
Po dokončení konfigurace metriky Storage Analytics k monitorování účtu úložiště, Storage Analytics zaznamenává metriky v sadě dobře známé tabulek ve vašem účtu úložiště. Můžete nakonfigurovat grafy, aby se po hodinách metriky v zobrazit [portál Azure](https://portal.azure.com):

1. Přejděte na svůj účet úložiště [portál Azure](https://portal.azure.com).
1. Vyberte **metriky** v **nabídky** podokně pro službu jejichž metriky, které chcete zobrazit.
1. Vyberte **upravit** v grafu, kterou chcete konfigurovat.
1. V **upravit graf** podokně, vyberte **časový rozsah**, **typ grafu**a metriky, které chcete zobrazit v grafu.
1. Vyberte **OK**.

Pokud chcete stáhnout metriky pro dlouhodobé uložení nebo pro analýzu je místně, je třeba:

* Použijte nástroj, který má informace o těchto tabulek a vám umožní zobrazit a je stáhnout.
* Napište vlastní aplikaci nebo skript ke čtení a ukládání tabulek.

Celou řadu nástrojů procházení úložiště jiných výrobců jsou informace o těchto tabulek a vám umožní zobrazit přímo.
V tématu [Azure Storage Client Tools](storage-explorers.md) seznam dostupných nástrojů.

> [!NOTE]
> Počínaje verzí 0.8.0 [Microsoft Azure Storage Explorer](http://storageexplorer.com/), můžete zobrazit a stáhnout tabulky metriky analytics.
> 
> 

K přístupu k tabulce analýzy prostřednictvím kódu programu, Všimněte si, analýzy tabulky se nezobrazí Pokud seznamu všechny tabulky v účtu úložiště. Můžete k nim přistupovat přímo podle názvu, nebo použít [CloudAnalyticsClient API](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.analytics.cloudanalyticsclient.aspx) v klientské knihovny .NET k dotazování názvy tabulek.

### <a name="hourly-metrics"></a>Hodinové metriky
* $MetricsHourPrimaryTransactionsBlob
* $MetricsHourPrimaryTransactionsTable
* $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>Minutu metriky
* $MetricsMinutePrimaryTransactionsBlob
* $MetricsMinutePrimaryTransactionsTable
* $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>Kapacita
* $MetricsCapacityBlob

Můžete najít podrobnosti schémat pro tyto tabulky v [schématu tabulky metriky Analytics úložiště](https://msdn.microsoft.com/library/azure/hh343264.aspx). Ukázka řádků níže zobrazit pouze podmnožinu sloupců, které jsou k dispozici, ale ilustrovat některé důležité funkce způsob, jakým Storage Metrics uloží tyto metriky:

| PartitionKey | RowKey | Časové razítko | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Dostupnost | AverageE2ELatency | AverageServerLatency | PercentSuccess |
| --- |:---:| ---:| --- | --- | --- | --- | --- | --- | --- | --- |
| 20140522T1100 |uživatel; Všechny |2014-05-22T11:01:16.7650250Z |7 |7 |4003 |46801 |100 |104.4286 |6.857143 |100 |
| 20140522T1100 |uživatel; QueryEntities |2014-05-22T11:01:16.7640250Z |5 |5 |2694 |45951 |100 |143.8 |7.8 |100 |
| 20140522T1100 |uživatel; QueryEntity |2014-05-22T11:01:16.7650250Z |1 |1 |538 |633 |100 |3 |3 |100 |
| 20140522T1100 |user;UpdateEntity |2014-05-22T11:01:16.7650250Z |1 |1 |771 |217 |100 |9 |6 |100 |

V těchto datech minutu metriky příklad používá klíč oddílu čas na minutu řešení. Klíč řádku určuje typ informací, která je uložená v řádku. Klíč řádku se skládá ze dvou částí informace, typ přístupu a typ požadavku:

* Typ přístupu je uživatel nebo systém, uživatel odkazuje na všechny požadavky na uživatele ve službě úložiště, kde systém odkazuje na požadavky na analytika úložiště.
* Typ požadavku je buď všechny v takovém případě ji souhrn řádku, nebo ji identifikuje konkrétní API jako třeba QueryEntity nebo UpdateEntity.

Výše uvedené ukázková data zobrazí všechny záznamy pro jednu minutu (počínaje 11:00 AM), tak počet požadavků QueryEntities plus počet QueryEntity požadavky plus počet UpdateEntity požadavků přidat až 7, což je celkový počet zobrazený na uživatele: všechny řádek. Podobně můžete odvodit Průměrná latence začátku do konce 104.4286 pro uživatele: všechny řádek pomocí výpočtu ((143.8 * 5) + 3 + 9) / 7.

## <a name="metrics-alerts"></a>Metriky výstrahy
Měli byste zvážit vytvoření výstrahy v [portál Azure](https://portal.azure.com) tak metriky úložiště můžete automaticky upozorňují na důležité změny v chování nástroje vaší služby úložiště. Pokud používáte nástroj Průzkumník úložišť pro stahování této metriky dat ve formátu odděleného, můžete k analýze dat aplikace Microsoft Excel. V tématu [Azure Storage Client Tools](storage-explorers.md) seznam nástrojů Průzkumníka úložiště k dispozici. Výstrahy v můžete nakonfigurovat **výstrah pravidla** podokně přístupná **monitorování** v podokně nabídky účet úložiště.

> [!IMPORTANT]
> Může nastat zpoždění mezi událostí úložiště a když se zaznamenává odpovídající data hodinových nebo minutu metriky. Pokud protokolování minutu metriky, několik minut dat může být napsán najednou. Transakce z dřívějších minut může pak dají agregovat do transakce pro do aktuální minuty. V takovém případě výstrah služby nemusí mít všechna data dostupné metriky pro nakonfigurované výstrahy interval, což může vést k upozornění, která iniciovala neočekávaně.
>

## <a name="accessing-metrics-data-programmatically"></a>Přístup k datům metriky prostřednictvím kódu programu
Následující seznam zobrazuje ukázka C# kód, který přistupuje k minutu metriky pro řadu minut a zobrazí výsledky v okně konzoly. Používá knihovna pro úložiště Azure verze 4, který zahrnuje CloudAnalyticsClient třídu, která zjednodušuje přístup k tabulky metriky v úložišti.

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)
{
    // Convert the dates to the format used in the PartitionKey
    var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");

    var services = Enum.GetValues(typeof(StorageService));
    foreach (StorageService service in services)
    {
        Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);
        var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);
        var t = analyticsClient.GetMinuteMetricsTable(service);
        var opContext = new OperationContext();
        var query =
          from entity in metricsQuery
          // Note, you can't filter using the entity properties Time, AccessType, or TransactionType
          // because they are calculated fields in the MetricsEntity class.
          // The PartitionKey identifies the DataTime of the metrics.
          where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0
        select entity;

        // Filter on "user" transactions after fetching the metrics from Table Storage.
        // (StartsWith is not supported using LINQ with Azure table storage)
        var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));
        var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();
        Console.WriteLine(resultString);
    }
}

private static string MetricsString(MetricsEntity entity, OperationContext opContext)
{
    var entityProperties = entity.WriteEntity(opContext);
    var entityString =
      string.Format("Time: {0}, ", entity.Time) +
      string.Format("AccessType: {0}, ", entity.AccessType) +
      string.Format("TransactionType: {0}, ", entity.TransactionType) +
      string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));
    return entityString;
}
```

## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>Jaké poplatky platit při povolení metrik úložiště?
Zapsat požadavky na vytvoření entity tabulky metrik, které budou účtovat podle standardních sazeb platí pro všechny operace úložiště Azure.

Požadavků na čtení a odstranění klientem metriky dat jsou také fakturovatelný na standardních sazeb. Pokud jste nakonfigurovali zásadu uchovávání dat, vám není účtován když odstraní stará data metrik Azure Storage. Pokud odstraníte analytická data, váš účet účtovat pro operace odstranění.

Kapacita používané metriky tabulky je také fakturovatelný: k zjištění přibližné hodnoty množství kapacity, na které se používá k ukládání dat metriky můžete použít následující:

* Pokud každou hodinu služba využívá každé rozhraní API v každé službě, pak přibližně 148KB dat je uložený každou hodinu do tabulky transakcí metriky Pokud jste povolili službu a úroveň API souhrnu.
* Pokud každou hodinu služba využívá každé rozhraní API v každé službě, pak přibližně 12KB dat je uložený každou hodinu do tabulky transakcí metriky Pokud jste povolili právě služby úrovni souhrnu.
* Tabulka kapacity pro objekty BLOB obsahuje dva řádky přidat každý den (za předpokladu, že uživatel požádal v protokolů): to znamená, že každý den velikost této tabulky zvyšuje úroveň až přibližně 300 bajtů.

## <a name="next-steps"></a>Další postup
[Povolení protokolování a přístup k datům protokolu úložiště](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data)
