---
title: Rychlé zprovoznění poradce metriky jazyka C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 10/14/2020
ms.author: mbullwin
ms.openlocfilehash: 8e9b2c69344a19c027f72983a02834aee2e14ccf
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186836"
---
[Referenční dokumentace](https://aka.ms/azsdk/net/docs/ref/metricsadvisor)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src)  |  [Balíček (NuGet)](https://www.nuget.org/packages/Azure.AI.MetricsAdvisor)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples/README.md)

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

Po vytvoření nového projektu nainstalujte knihovnu klienta tak, že kliknete pravým tlačítkem na řešení projektu v **Průzkumník řešení** a vyberete **Spravovat balíčky NuGet** . Ve Správci balíčků, který se otevře, vyberte **Procházet** , zaškrtněte políčko **Zahrnout předprodejní** a vyhledejte `Azure.AI.MetricsAdvisor` . Vyberte verzi `1.0.0-beta.1` a pak **nainstalujte** . 

V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem `metrics-advisor-quickstart` . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: *program.cs* . 

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
dotnet add package Azure.AI.MetricsAdvisor --version 1.0.0-beta.1
```

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít na [GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.MetricsAdvisor_1.0.0-beta.1/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples), který obsahuje příklady kódu v tomto rychlém startu.

Z adresáře projektu otevřete soubor *program.cs* a přidejte následující `using` direktivy:

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Azure.AI.MetricsAdvisor.Administration;
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
| [MetricsAdvisorClient](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorClient.cs) | **Používá se pro** : <br> -Výpis incidentů <br> -Výpis hlavní příčiny incidentů <br> – Načítání původních dat časových řad a dat časových řad obohacených službou. <br> -Výpis výstrah <br> – Přidání zpětné vazby k ladění modelu |
| [MetricsAdvisorAdministrationClient](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorAdministrationClient.cs)| **Umožňuje:** <br> – Správa datových kanálů <br> -Konfigurovat konfigurace detekce anomálií <br> -Konfigurovat konfigurace upozorňování anomálií <br> -Spravovat zavěšení  |
| [Datové kanály](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/Models/DataFeed/DataFeed.cs)| **Jaké metriky Advisoru ingestují ze zdroje dat. `DataFeed` Obsahuje řádky:** <br> – Časová razítka <br> – Nula nebo více dimenzí <br> – Jedna nebo více měr  |
| [Metrika](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/Models/DataFeedMetric.cs)| A `Metric` je kvantifikovaná míra, která se používá k monitorování a vyhodnocení stavu konkrétního obchodního procesu. Může se jednat o kombinaci více hodnot časových řad rozdělených do dimenzí. Například metrika stavu webu může obsahovat rozměry počtu uživatelů a trhu en-US. |

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
> Přejděte na Azure Portal. Pokud se prostředek poradce metriky, který jste vytvořili v části **předpoklady** , úspěšně nasadil, klikněte v části **Další kroky** na tlačítko **Přejít k prostředku** . Klíče předplatného a koncový bod můžete najít na stránce **klíč a koncový bod** prostředku v části **Správa prostředků** . <br><br>Pokud chcete načíst klíč rozhraní API, musíte přejít na [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) . Vyberte příslušný **adresář** , **odběry** a **pracovní prostor** pro váš prostředek a zvolte možnost **začít** . Pak budete moci načíst klíče rozhraní API z [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) .   
>
> Nezapomeňte odebrat klíč z kódu, až budete hotovi, a nikdy ho zveřejnit. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Další informace najdete v článku o [zabezpečení](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) Cognitive Services.

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

Poradce metrik podporuje více typů zdrojů dat. V této ukázce ukážeme, jak vytvořit objekt `DataFeed` , který extrahuje data z SQL serveru. Nahraďte `connection_String` vlastním připojovacím řetězcem SQL serveru a nahraďte `query` dotazem, který vrací vaše data v jednom časovém razítku. Také budete muset upravit `metric` hodnoty a na `dimension` základě vlastních dat.


```csharp
string sqlServerConnectionString = "<connection_String>";
string sqlServerQuery = "<query>";

var dataFeedName = "Sample data feed";
var dataFeedSource = new MySqlDataFeedSource(sqlServerConnectionString, sqlServerQuery);
var dataFeedGranularity = new DataFeedGranularity(DataFeedGranularityType.Daily);

var dataFeedMetrics = new List<DataFeedMetric>()
{
    new DataFeedMetric("cost"),
    new DataFeedMetric("revenue")
};
var dataFeedDimensions = new List<MetricDimension>()
{
    new MetricDimension("category"),
    new MetricDimension("city")
};
var dataFeedSchema = new DataFeedSchema(dataFeedMetrics)
{
    DimensionColumns = dataFeedDimensions
};

var ingestionStartTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var dataFeedIngestionSettings = new DataFeedIngestionSettings(ingestionStartTime);

Response<DataFeed> response = await adminClient.CreateDataFeedAsync(dataFeedName, dataFeedSource,
    dataFeedGranularity, dataFeedSchema, dataFeedIngestionSettings);

DataFeed dataFeed = response.Value;

Console.WriteLine($"Data feed ID: {dataFeed.Id}");

// Only the ID of the data feed is known at this point. You can perform another service
// call to get more information, such as status, created time, the list of administrators,
// or the metric IDs.

response = await adminClient.GetDataFeedAsync(dataFeed.Id);

dataFeed = response.Value;

Console.WriteLine($"Data feed status: {dataFeed.Status.Value}");
Console.WriteLine($"Data feed created time: {dataFeed.CreatedTime.Value}");

Console.WriteLine($"Data feed administrators:");
foreach (string admin in dataFeed.Options.Administrators)
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
var options = new GetDataFeedIngestionStatusesOptions(startTime, endTime);

Console.WriteLine("Ingestion statuses:");
Console.WriteLine();

int statusCount = 0;

await foreach (DataFeedIngestionStatus ingestionStatus in adminClient.GetDataFeedIngestionStatusesAsync(dataFeedId, options))
{
    Console.WriteLine($"Timestamp: {ingestionStatus.Timestamp}");
    Console.WriteLine($"Status: {ingestionStatus.Status.Value}");
    Console.WriteLine($"Service message: {ingestionStatus.Message}");
    Console.WriteLine();

    // Print at most 10 statuses.
    if (++statusCount >= 10)
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

Response<AnomalyDetectionConfiguration> response = await adminClient.CreateMetricAnomalyDetectionConfigurationAsync(detectionConfiguration);

detectionConfiguration = response.Value;

Console.WriteLine($"Anomaly detection configuration ID: {detectionConfiguration.Id}");
```

### <a name="create-a-hook"></a>Vytvořit zavěšení

Poradce metrik podporuje `EmailHook` `WebHook` třídy a jako způsob přihlášení k odběru oznámení výstrah. V tomto příkladu ukážeme, jak vytvořit `EmailHook` . Aby bylo možné začít dostávat oznámení, je nutné předávat zavěšení do konfigurace výstrahy anomálií. Další informace najdete v ukázce [Vytvoření konfigurace výstrahy anomálií](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor#create-an-anomaly-alert-configuration) .

```csharp
string hookName = "Sample hook";
var emailsToAlert = new List<string>()
{
    "email1@sample.com",
    "email2@sample.com"
};

var emailHook = new EmailNotificationHook(hookName, emailsToAlert);

Response<NotificationHook> response = await adminClient.CreateHookAsync(emailHook);

NotificationHook hook = response.Value;

Console.WriteLine($"Hook ID: {hook.Id}");
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

Response<AnomalyAlertConfiguration> response = await adminClient.CreateAnomalyAlertConfigurationAsync(alertConfiguration);

alertConfiguration = response.Value;

Console.WriteLine($"Alert configuration ID: {alertConfiguration.Id}");
```

### <a name="query-the-alert"></a>Dotaz na upozornění

Prohlédněte si výstrahy vytvořené pomocí dané konfigurace výstrahy anomálií a uveďte anomálie, které tyto výstrahy aktivovaly.

```csharp
string anomalyAlertConfigurationId = "<anomalyAlertConfigurationId>";

var startTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var endTime = DateTimeOffset.UtcNow;
var options = new GetAlertsOptions(startTime, endTime, TimeMode.AnomalyTime);

int alertCount = 0;

await foreach (AnomalyAlert alert in client.GetAlertsAsync(anomalyAlertConfigurationId, options))
{
    Console.WriteLine($"Alert at timestamp: {alert.Timestamp}");
    Console.WriteLine($"Id: {alert.Id}");
    Console.WriteLine($"Anomalies that triggered this alert:");

    await foreach (DataAnomaly anomaly in client.GetAnomaliesForAlertAsync(anomalyAlertConfigurationId, alert.Id))
    {
        Console.WriteLine("  Anomaly:");
        Console.WriteLine($"    Status: {anomaly.Status.Value}");
        Console.WriteLine($"    Severity: {anomaly.Severity}");
        Console.WriteLine($"    Series key:");

        foreach (KeyValuePair<string, string> keyValuePair in anomaly.SeriesKey.AsDictionary())
        {
            Console.WriteLine($"      Dimension '{keyValuePair.Key}': {keyValuePair.Value}");
        }

        Console.WriteLine();
    }

    // Print at most 3 alerts.
    if (++alertCount >= 3)
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