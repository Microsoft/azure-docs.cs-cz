---
title: Rychlé zprovoznění poradce metriky jazyka C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: c0f2c9a6a9b17ce1979143840b0647e9af2183e7
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356388"
---
[Referenční dokumentace](/dotnet/api/overview/azure/ai.metricsadvisor-readme-pre)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src)  |  [Balíček (NuGet)](https://www.nuget.org/packages/Azure.AI.MetricsAdvisor)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples/README.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Jakmile budete mít předplatné Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" vytvořte prostředek Advisoru metriky "  target="_blank"> vytvořením prostředku Advisoru metriky <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal k nasazení instance služby Advisor pro metriky.  
* Vaše vlastní databáze SQL s daty časových řad.
   
> [!TIP]
> * Ukázky pro službu .NET metriky Advisor můžete najít na [GitHubu](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples/README.md).
> * Pro nasazení instance služby, kterou můžete použít, může trvat 10 až 30 minut, než se dokončí váš prostředek poradce metriky. Po úspěšném nasazení klikněte na **Přejít k prostředku** . Po nasazení můžete začít používat vaši instanci poradce metriky s webovým portálem i REST API. 
> * Adresu URL pro REST API můžete najít v Azure Portal v části **Přehled** prostředku. Bude vypadat takto:
>    * `https://<instance-name>.cognitiveservices.azure.com/`
   
## <a name="setting-up"></a>Nastavení

### <a name="install-the-client-library"></a>Instalace klientské knihovny 

Po vytvoření nového projektu nainstalujte knihovnu klienta tak, že kliknete pravým tlačítkem na řešení projektu v **Průzkumník řešení** a vyberete **Spravovat balíčky NuGet**. Ve Správci balíčků, který se otevře, vyberte **Procházet**, zaškrtněte políčko **Zahrnout předprodejní** a vyhledejte `Azure.AI.MetricsAdvisor` . Vyberte verzi `1.0.0-beta.2` a pak **nainstalujte**. 

V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem `metrics-advisor-quickstart` . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: *program.cs*. 

```console
dotnet new console -n metrics-advisor-quickstart
```

Změňte adresář na nově vytvořenou složku aplikace. Aplikaci můžete vytvořit pomocí:

```console
dotnet build
```

Výstup sestavení by neměl obsahovat žádná upozornění ani chyby. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Instalace klientské knihovny 

Pokud používáte jiné integrované vývojové prostředí než Visual Studio, můžete nainstalovat knihovnu klienta Poradce pro metriky pro rozhraní .NET pomocí následujícího příkazu:

```console
dotnet add package Azure.AI.MetricsAdvisor --version 1.0.0-beta.2
```

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít na [GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.MetricsAdvisor_1.0.0-beta.1/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples), který obsahuje příklady kódu v tomto rychlém startu.

Z adresáře projektu otevřete soubor *program.cs* a přidejte následující `using` direktivy:

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Azure.AI.MetricsAdvisor.Administration;
using Azure.AI.MetricsAdvisor;
using Azure.AI.MetricsAdvisor.Models;
```

V `Main()` metodě aplikace přidejte volání metod používaných v rámci tohoto rychlého startu. Budete je vytvářet později.

```csharp
static void Main(string[] args){
    // You will create the below methods later in the quickstart
    exampleTask1();
}
```

## <a name="object-model"></a>Objektový model

Následující třídy zpracovávají některé z hlavních funkcí sady SDK pro službu metriky metrik v jazyce C#.

|Název|Popis|
|---|---|
| [MetricsAdvisorClient](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorClient.cs) | **Používá se pro**: <br> -Výpis incidentů <br> -Výpis hlavní příčiny incidentů <br> – Načítání původních dat časových řad a dat časových řad obohacených službou. <br> -Výpis výstrah <br> – Přidání zpětné vazby k ladění modelu |
| [MetricsAdvisorAdministrationClient](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorAdministrationClient.cs)| **Umožňuje:** <br> – Správa datových kanálů <br> -Konfigurovat konfigurace detekce anomálií <br> -Konfigurovat konfigurace upozorňování anomálií <br> -Spravovat zavěšení  |
| [Datové kanály](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/Models/DataFeed/DataFeed.cs)| **Jaké metriky Advisoru ingestují ze zdroje dat. `DataFeed` Obsahuje řádky:** <br> – Časová razítka <br> – Nula nebo více dimenzí <br> – Jedna nebo více měr  |
| [DataFeedMetric](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/Models/DataFeed/DataFeedMetric.cs)| A `DataFeedMetric` je kvantifikovaná míra, která se používá k monitorování a vyhodnocení stavu konkrétního obchodního procesu. Může se jednat o kombinaci více hodnot časových řad rozdělených do dimenzí. Například metrika stavu webu může obsahovat rozměry počtu uživatelů a trhu en-US. |

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny Poradce pro metriky pro .NET:

* [Ověření klienta](#authenticate-the-client)
* [Přidání datového kanálu](#add-a-data-feed)
* [Zkontroluje stav příjmu.](#check-the-ingestion-status)
* [Konfigurovat detekci anomálií](#configure-anomaly-detection)
* [Vytvořit zavěšení](#create-a-hook)
* [Vytvoření konfigurace výstrahy](#create-an-alert-configuration)
* [Dotaz na upozornění](#query-the-alert)

## <a name="authenticate-the-client"></a>Ověření klienta

Ve `Program` třídě aplikace vytvořte proměnné pro klíče prostředku a koncový bod.

> [!IMPORTANT]
> Přejděte na Azure Portal. Pokud se prostředek poradce metriky, který jste vytvořili v části **předpoklady** , úspěšně nasadil, klikněte v části **Další kroky** na tlačítko **Přejít k prostředku** . Klíče předplatného a koncový bod můžete najít na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**. <br><br>Pokud chcete načíst klíč rozhraní API, musíte přejít na [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) . Vyberte příslušný **adresář**, **odběry** a **pracovní prostor** pro váš prostředek a zvolte možnost **začít**. Pak budete moci načíst klíče rozhraní API z [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) .   
>
> Nezapomeňte odebrat klíč z kódu, až budete hotovi, a nikdy ho zveřejnit. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Další informace najdete v článku o [zabezpečení](../../../cognitive-services-security.md) Cognitive Services.

Jakmile budete mít předplatné a klíče rozhraní API, vytvořte MetricsAdvisorKeyCredential. Pomocí koncového bodu a klíčového přihlašovacího údaje můžete vytvořit [`MetricsAdvisorClient`](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorClient.cs) :

```csharp
string endpoint = "<endpoint>";
string subscriptionKey = "<subscriptionKey>";
string apiKey = "<apiKey>";
var credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
var client = new MetricsAdvisorClient(new Uri(endpoint), credential);
```

Můžete také vytvořit [`MetricsAdvisorAdministrationClient`](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorAdministrationClient.cs) pro provádění operací správy:

```csharp
string endpoint = "<endpoint>";
string subscriptionKey = "<subscriptionKey>";
string apiKey = "<apiKey>";
var credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
var adminClient = new MetricsAdvisorAdministrationClient(new Uri(endpoint), credential);
```

## <a name="add-a-data-feed"></a>Přidání datového kanálu

Poradce metrik podporuje více typů zdrojů dat. V této ukázce ukážeme, jak vytvořit objekt `DataFeed` , který extrahuje data z SQL serveru. Nahraďte `connection_String` vlastním připojovacím řetězcem SQL serveru a nahraďte `query` dotazem, který vrací vaše data v jednom časovém razítku. Také budete muset upravit `DataFeedMetric` hodnoty a na `DataFeedDimension` základě vlastních dat.


```csharp
string sqlServerConnectionString = "<connection_String>";
string sqlServerQuery = "<query>";

var dataFeedName = "Sample data feed";
var dataFeedSource = new SqlServerDataFeedSource(sqlServerConnectionString, sqlServerQuery);
var dataFeedGranularity = new DataFeedGranularity(DataFeedGranularityType.Daily);

var dataFeedMetrics = new List<DataFeedMetric>()
{
    new DataFeedMetric("cost"),
    new DataFeedMetric("revenue")
};
var dataFeedDimensions = new List<DataFeedDimension>()
{
    new DataFeedDimension("category"),
    new DataFeedDimension("city")
};
var dataFeedSchema = new DataFeedSchema(dataFeedMetrics)
{
    DimensionColumns = dataFeedDimensions
};

var ingestionStartTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var dataFeedIngestionSettings = new DataFeedIngestionSettings(ingestionStartTime);

var dataFeed = new DataFeed(dataFeedName, dataFeedSource, dataFeedGranularity, dataFeedSchema, dataFeedIngestionSettings);

Response<string> response = await adminClient.CreateDataFeedAsync(dataFeed);

string dataFeedId = response.Value;

Console.WriteLine($"Data feed ID: {dataFeedId}");

// Note that only the ID of the data feed is known at this point. You can perform another
// service call to GetDataFeedAsync or GetDataFeed to get more information, such as status,
// created time, the list of administrators, or the metric IDs.

Response<DataFeed> response = await adminClient.GetDataFeedAsync(dataFeedId);

DataFeed dataFeed = response.Value;

Console.WriteLine($"Data feed status: {dataFeed.Status.Value}");
Console.WriteLine($"Data feed created time: {dataFeed.CreatedTime.Value}");

Console.WriteLine($"Data feed administrators:");
foreach (string admin in dataFeed.Administrators)
{
    Console.WriteLine($" - {admin}");
}

Console.WriteLine($"Metric IDs:");
foreach (DataFeedMetric metric in dataFeed.Schema.MetricColumns)
{
    Console.WriteLine($" - {metric.MetricName}: {metric.MetricId}");
}
```

## <a name="check-the-ingestion-status"></a>Zkontroluje stav příjmu.

Zkontroluje stav příjmu dříve vytvořeného `DataFeed`

```csharp
string dataFeedId = "<dataFeedId>";

var startTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var endTime = DateTimeOffset.Parse("2020-09-09T00:00:00Z");
var options = new GetDataFeedIngestionStatusesOptions(startTime, endTime)
{
    TopCount = 5
};

Console.WriteLine("Ingestion statuses:");
Console.WriteLine();

int statusCount = 0;

await foreach (DataFeedIngestionStatus ingestionStatus in adminClient.GetDataFeedIngestionStatusesAsync(dataFeedId, options))
{
    Console.WriteLine($"Timestamp: {ingestionStatus.Timestamp}");
    Console.WriteLine($"Status: {ingestionStatus.Status}");
    Console.WriteLine($"Service message: {ingestionStatus.Message}");
    Console.WriteLine();

    // Print at most 5 statuses.
    if (++statusCount >= 5)
    {
        break;
    }
}
```

## <a name="configure-anomaly-detection"></a>Konfigurovat detekci anomálií 

Vytvořte konfiguraci detekce anomálií a sdělte službě, které datové body by měly být považovány za anomálie.

```csharp
string metricId = "<metricId>";
string configurationName = "Sample anomaly detection configuration";

var hardThresholdSuppressCondition = new SuppressCondition(1, 100);
var hardThresholdCondition = new HardThresholdCondition(AnomalyDetectorDirection.Down, hardThresholdSuppressCondition)
{
    LowerBound = 5.0
};

var smartDetectionSuppressCondition = new SuppressCondition(4, 50);
var smartDetectionCondition = new SmartDetectionCondition(10.0, AnomalyDetectorDirection.Up, smartDetectionSuppressCondition);

var detectionCondition = new MetricWholeSeriesDetectionCondition()
{
    HardThresholdCondition = hardThresholdCondition,
    SmartDetectionCondition = smartDetectionCondition,
    CrossConditionsOperator = DetectionConditionsOperator.Or
};

var detectionConfiguration = new AnomalyDetectionConfiguration(metricId, configurationName, detectionCondition);

Response<string> response = await adminClient.CreateDetectionConfigurationAsync(detectionConfiguration);

string detectionConfigurationId = response.Value;

Console.WriteLine($"Anomaly detection configuration ID: {detectionConfigurationId}");
```

### <a name="create-a-hook"></a>Vytvořit zavěšení

Poradce metrik podporuje `EmailNotificationHook` `WebNotificationHook` třídy a jako způsob přihlášení k odběru oznámení výstrah. V tomto příkladu ukážeme, jak vytvořit `EmailNotificationHook` . Aby bylo možné začít dostávat oznámení, je nutné předávat zavěšení do konfigurace výstrahy anomálií. Další informace najdete v ukázce [Vytvoření konfigurace výstrahy anomálií](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor#create-an-anomaly-alert-configuration) .

```csharp
string hookName = "Sample hook";
var emailsToAlert = new List<string>()
{
    "email1@sample.com",
    "email2@sample.com"
};

var emailHook = new EmailNotificationHook(hookName, emailsToAlert);

Response<string> response = await adminClient.CreateHookAsync(emailHook);

string hookId = response.Value;

Console.WriteLine($"Hook ID: {hookId}");
```

##  <a name="create-an-alert-configuration"></a>Vytvoření konfigurace výstrahy

Vytvořte `AnomalyAlertConfiguration` a sdělte službě, které výstrahy mají aktivovat.

```csharp
string hookId = "<hookId>";
string anomalyDetectionConfigurationId = "<anomalyDetectionConfigurationId>";

string configurationName = "Sample anomaly alert configuration";
var idsOfHooksToAlert = new List<string>() { hookId };

var scope = MetricAnomalyAlertScope.GetScopeForWholeSeries();
var metricAlertConfigurations = new List<MetricAnomalyAlertConfiguration>()
{
    new MetricAnomalyAlertConfiguration(anomalyDetectionConfigurationId, scope)
};

AnomalyAlertConfiguration alertConfiguration = new AnomalyAlertConfiguration(configurationName, idsOfHooksToAlert, metricAlertConfigurations);

Response<string> response = await adminClient.CreateAlertConfigurationAsync(alertConfiguration);

string alertConfigurationId = response.Value;

Console.WriteLine($"Alert configuration ID: {alertConfigurationId}");
```

### <a name="query-the-alert"></a>Dotaz na upozornění

Prohlédněte si výstrahy vytvořené pomocí dané konfigurace výstrahy anomálií.

```csharp
string anomalyAlertConfigurationId = "<anomalyAlertConfigurationId>";

var startTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var endTime = DateTimeOffset.UtcNow;
var options = new GetAlertsOptions(startTime, endTime, AlertQueryTimeMode.AnomalyTime)
{
    TopCount = 5
};

int alertCount = 0;

await foreach (AnomalyAlert alert in client.GetAlertsAsync(anomalyAlertConfigurationId, options))
{
    Console.WriteLine($"Alert created at: {alert.CreatedTime}");
    Console.WriteLine($"Alert at timestamp: {alert.Timestamp}");
    Console.WriteLine($"Id: {alert.Id}");
    Console.WriteLine();

    // Print at most 5 alerts.
    if (++alertCount >= 5)
    {
        break;
    }
}
```

Jakmile znáte ID výstrahy, uveďte [anomálie](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/README.md#data-point-anomaly) , které tuto výstrahu aktivovaly.

```csharp
string alertConfigurationId = "<alertConfigurationId>";
string alertId = "<alertId>";

var options = new GetAnomaliesForAlertOptions() { TopCount = 3 };

int anomalyCount = 0;

await foreach (DataPointAnomaly anomaly in client.GetAnomaliesAsync(alertConfigurationId, alertId, options))
{
    Console.WriteLine($"Anomaly detection configuration ID: {anomaly.AnomalyDetectionConfigurationId}");
    Console.WriteLine($"Metric ID: {anomaly.MetricId}");
    Console.WriteLine($"Anomaly at timestamp: {anomaly.Timestamp}");
    Console.WriteLine($"Anomaly detected at: {anomaly.CreatedTime}");
    Console.WriteLine($"Status: {anomaly.Status}");
    Console.WriteLine($"Severity: {anomaly.Severity}");
    Console.WriteLine("Series key:");

    foreach (KeyValuePair<string, string> keyValuePair in anomaly.SeriesKey.AsDictionary())
    {
        Console.WriteLine($"  Dimension '{keyValuePair.Key}': {keyValuePair.Value}");
    }

    Console.WriteLine();

    // Print at most 3 anomalies.
    if (++anomalyCount >= 3)
    {
        break;
    }
}
```


### <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci z adresáře aplikace pomocí `dotnet run` příkazu.

```dotnet
dotnet run
```