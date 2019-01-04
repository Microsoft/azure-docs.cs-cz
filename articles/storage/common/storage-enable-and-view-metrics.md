---
title: Zapnutí metrik storage na webu Azure Portal | Dokumentace Microsoftu
description: Postup povolení metrik úložiště pro služby objektů Blob, fronty, tabulky a souboru
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 02/14/2017
ms.author: rogarana
ms.component: common
ms.openlocfilehash: 3e6a11a8c225afb220f290cee2db39c36750b401
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632140"
---
# <a name="enabling-azure-storage-metrics-and-viewing-metrics-data"></a>Zapnutí metrik Azure Storage a zobrazení dat metrik
[!INCLUDE [storage-selector-portal-enable-and-view-metrics](../../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Přehled
Storage Metrics je povolena ve výchozím nastavení při vytváření nového účtu úložiště. Můžete nakonfigurovat monitorování prostřednictvím [webu Azure portal](https://portal.azure.com) nebo prostředí Windows PowerShell, nebo prostřednictvím kódu programu prostřednictvím klientských knihoven pro úložiště.

Můžete nakonfigurovat takovou dobu uchování dat metrik: Toto období určuje, jak dlouho úložiště zajišťuje metriky a poplatky, které jste pro prostor musí k jejich uložení. Obvykle byste měli použít kratší doba uchování pro minutové metriky než hodinové metriky z důvodu významné přebytečné místo požadované pro minutové metriky. Vyberte dobu uchování tak, aby měl dostatek času k analýze dat a stáhnout všechny metriky, které chcete zachovat pro offline analýzu nebo pro účely generování sestav. Mějte na paměti, že vám také účtují za stahování dat metrik z vašeho účtu úložiště.

## <a name="how-to-enable-metrics-using-the-azure-portal"></a>Postup povolení metriky pomocí webu Azure portal
Postupujte podle těchto kroků k zapnutí metrik v [webu Azure portal](https://portal.azure.com):

1. Přejděte na svůj účet úložiště.
1. Vyberte **diagnostiky** z **nabídky** podokně.
1. Ujistěte se, že **stav** je nastavena na **na**.
1. Vyberte metriku pro služby, které chcete monitorovat.
1. Zadejte zásady uchovávání informací udávajících dobu uchovávat metrik a protokolování dat o.
1. Vyberte **Uložit**.

[Webu Azure portal](https://portal.azure.com) nepovolíte aktuálně je možné nakonfigurovat minutové metriky ve vašem účtu úložiště, je nutné povolit minutové metriky pomocí Powershellu nebo prostřednictvím kódu programu.

## <a name="how-to-enable-metrics-using-powershell"></a>Postup povolení metriky pomocí Powershellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Konfigurace metrik Storage ve vašem účtu úložiště pomocí prostředí Azure PowerShell rutinu Get-AzStorageServiceMetricsProperty načíst aktuální nastavení a rutiny Set-AzStorageServiceMetricsProperty můžete použít prostředí PowerShell na místním počítači Chcete-li změnit aktuální nastavení.

Rutiny, které řídí metrik úložiště, použijte následující parametry:

* MetricsType: možné hodnoty jsou hodiny a minuty.
* ServiceType: možné hodnoty jsou objekt Blob, Queue a Table.
* MetricsLevel: možné hodnoty jsou None, služby a ServiceAndApi.

Například následující příkaz zapne minutové metriky pro službu Blob service ve výchozí účet úložiště s uchováním nastavte období na pět dní:

```powershell
Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`
```

Následující příkaz načte aktuální hodinové metriky úroveň a uchovávání dní pro službu blob service ve výchozí účet úložiště:

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob
```

Informace o tom, jak nakonfigurovat rutiny Azure Powershellu pro práci s vaším předplatným Azure a jak vybrat výchozí účet úložiště pro použití najdete tady: [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).

## <a name="how-to-enable-storage-metrics-programmatically"></a>Postup povolení metrik Storage prostřednictvím kódu programu
Následující jazyka C# fragment kódu ukazuje, jak povolit metrik a protokolování pro službu Blob service pomocí klientskou knihovnu pro úložiště pro .NET:

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

## <a name="viewing-storage-metrics"></a>Zobrazení metrik Storage
Po dokončení konfigurace metrik Storage Analytics k monitorování vašeho účtu úložiště, analytika úložiště zaznamenává metriky sadu známých tabulek ve vašem účtu úložiště. Můžete konfigurovat grafy pro zobrazení hodinové metriky [webu Azure portal](https://portal.azure.com):

1. Přejděte do svého účtu úložiště v [webu Azure portal](https://portal.azure.com).
1. Vyberte **metriky** v **nabídky** podokno pro službu, jejíž metriky, které chcete zobrazit.
1. Vyberte **upravit** v grafu, kterou chcete konfigurovat.
1. V **upravit graf** podokně, vyberte **časový rozsah**, **typ grafu**a metriky, které chcete zobrazit v grafu.
1. Vyberte **OK**.

Pokud chcete stáhnout metriky pro dlouhodobé uložení nebo jejich analýze místně, musíte:

* Použijte nástroj, který zná z těchto tabulek a vám umožní zobrazovat a stahovat je.
* Zapsat vlastní aplikace nebo skript ke čtení a ukládání tabulek.

Mnoho výrobců procházení úložiště nástroje víme z těchto tabulek a vám umožní zobrazit přímo.
Zobrazit [klientské nástroje pro Azure Storage](storage-explorers.md) seznam dostupných nástrojů.

> [!NOTE]
> Počínaje verzí 0.8.0 [Microsoft Azure Storage Explorer](http://storageexplorer.com/), můžete zobrazit a stáhnout tabulky metrik analýzy.
>
>

Aby bylo možné prostřednictvím kódu programu k tabulkám analytics, mějte na paměti, že tabulky analýzy nezobrazí seznam všech tabulek v účtu úložiště. Můžete k nim přistupovat přímo podle názvu, nebo použít [CloudAnalyticsClient API](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.analytics.cloudanalyticsclient.aspx) v klientské knihovně .NET k dotazování názvy tabulek.

### <a name="hourly-metrics"></a>Hodinové metriky
* $MetricsHourPrimaryTransactionsBlob
* $MetricsHourPrimaryTransactionsTable
* $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>Minutové metriky
* $MetricsMinutePrimaryTransactionsBlob
* $MetricsMinutePrimaryTransactionsTable
* $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>Kapacita
* $MetricsCapacityBlob

Pro tyto tabulky na najdete úplné podrobnosti o schémata [tabulkovému schématu metrik Storage Analytics](https://msdn.microsoft.com/library/azure/hh343264.aspx). Zobrazit pouze podmnožinu sloupců, které jsou k dispozici následující řádky vzorku, ale ukazují některé důležité funkce způsob, jakým metrik Storage ukládá tyto metriky:

| PartitionKey | RowKey | Časové razítko | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Dostupnost | Hodnotu AverageE2ELatency | Hodnotu AverageServerLatency. | PercentSuccess |
| --- |:---:| ---:| --- | --- | --- | --- | --- | --- | --- | --- |
| 20140522T1100 |uživatele. Všechny |2014-05-22T11:01:16.7650250Z |7 |7 |4003 |46801 |100 |104.4286 |6.857143 |100 |
| 20140522T1100 |uživatele. QueryEntities |2014-05-22T11:01:16.7640250Z |5 |5 |2694 |45951 |100 |143.8 |7.8 |100 |
| 20140522T1100 |uživatele. QueryEntity |2014-05-22T11:01:16.7650250Z |1 |1 |538 |633 |100 |3 |3 |100 |
| 20140522T1100 |uživatele. UpdateEntity |2014-05-22T11:01:16.7650250Z |1 |1 |771 |217 |100 |9 |6 |100 |

V těchto datech minutové metriky příklad používá klíč oddílu čas minuta rozlišením. Klíč řádku určuje typ informací uložených v řádku. Klíč řádku se skládá ze dvou částí informací, typ přístupu a typ požadavku:

* Typ přístupu je uživatele nebo systému, kde uživatel odkazuje na všechny žádosti uživatelů do služby storage a systém odkazuje na požadavky od analytika úložiště.
* Typ požadavku je všechny v takovém případě ji je souhrnný řádek, nebo označuje konkrétní rozhraní API, jako je například QueryEntity nebo UpdateEntity.

Ukázková data výše zobrazuje všechny záznamy pro za minutu (od v 11:00), tak počet požadavků QueryEntities plus počet QueryEntity požadavků plus počet požadavků UpdateEntity přidat až sedm, což je celkový počet zobrazí na řádku uživatele: All. Obdobně lze odvodit průměrnou dobu vyřízení začátku do konce 104.4286 na řádku, uživatel: všechny výpočtem ((143.8 * 5) + 3 + 9) / 7.

Mějte prosím na paměti, která **nastavení hodinové metriky objektů Blob** platí pro obě **metriku kapacity Blob** ($MetricsCapacityBlob) a **hodinovou metriku transakcí Blob** ($ MetricsHourPrimaryTransactionsBlob). Obě jsou povolené nebo zakázané společně a použít stejné zásady uchovávání informací.

## <a name="metrics-alerts"></a>Upozornění metriky
Měli byste zvážit vytvoření výstrah ve službě [webu Azure portal](https://portal.azure.com) tak metrik úložiště můžete automaticky upozorňovat na důležité změny v chování služby úložiště. Pokud používáte storage explorer nástroj ke stažení tato data metrik ve formátu s oddělovači, můžete analyzovat data aplikace Microsoft Excel. Zobrazit [klientské nástroje pro Azure Storage](storage-explorers.md) seznam nástrojů Průzkumníka úložiště k dispozici. Výstrahy v můžete nakonfigurovat **pravidla upozornění** podokno, přístupná pro **monitorování** na panelu nabídky účtu úložiště.

> [!IMPORTANT]
> Může existovat zpoždění mezi úložiště událostí a kdy se zaznamenává odpovídajících dat hodinových nebo minutové metriky. Při přihlašování minutové metriky, může několik minut dat zapsat najednou. Transakce z předchozích minut může pak agregují do transakce pro do aktuální minuty. Pokud k tomu dojde, služba upozornění nemusí mít všechna data dostupné metriky pro nakonfigurovaný interval výstrah, což může vést k neočekávaně aktivaci výstrahy.
>

## <a name="accessing-metrics-data-programmatically"></a>Přístup k datům metriky prostřednictvím kódu programu
Následující výpis ukazuje ukázkový kód jazyka C#, který přistupuje k nim minutové metriky pro celou řadu minut a zobrazí výsledky v okně konzoly. Využívá knihovnu verze 4, která zahrnuje CloudAnalyticsClient třídu, která zjednodušuje přístup k tabulkám metriky v úložišti Azure Storage.

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

## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>S jakými poplatky vám účtovat potřeba povolit metriky úložiště?
Zapsat požadavky na vytvoření entity tabulky pro metriky se účtují standardní sazby platné pro všechny operace služby Azure Storage.

Požadavky na čtení a odstranění pro klienta k datům metriky se taky účtují za standardní sazby. Pokud jste nakonfigurovali zásady uchovávání dat, se vám neúčtují, odstranění starých dat metrik Azure Storage. Ale při odstranění analytická data, váš účet se účtuje za operace odstranění.

Kapacita použitá tabulky metrik se také dají fakturovat: následující můžete použít k odhadu kapacity se používá k ukládání dat metrik:

* Pokud každou hodinu služba využívá každé rozhraní API v každé službě, pak přibližně 148KB dat se ukládají v tabulce metrik transakcí za hodinu Pokud jste povolili službu i souhrn úroveň rozhraní API.
* Pokud každou hodinu služba využívá každé rozhraní API v každé službě, pak přibližně 12KB dat se ukládají každou hodinu v tabulce metrik transakcí Pokud je povolené jenom úroveň služby souhrnu.
* Tabulka kapacity pro objekty BLOB obsahuje dva řádky přidané každý den (za předpokladu, že uživatel má pro protokoly výslovného souhlasu): z toho vyplývá, že každý den velikost této tabulky zvýší o přibližně 300 bajtů.

## <a name="next-steps"></a>Další postup
[Povolení úložiště, protokolování a přístup k datům protokolů](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data)
