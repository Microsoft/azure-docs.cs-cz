---
title: Rychlé zprovoznění pro poradce pro metriky JavaScriptu
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 10/14/2020
ms.author: mbullwin
ms.openlocfilehash: a4426e9ca40b21c79e5e34f782be4ff2d07c7061
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186846"
---
[Referenční dokumentace](https://docs.microsoft.com/javascript/api/overview/azure/ai-metrics-advisor-readme-pre?view=azure-node-preview&preserve-view=true)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/metricsadvisor/ai-metrics-advisor/README.md)  |  [Balíček (npm)](https://www.npmjs.com/package/@azure/ai-metrics-advisor)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Aktuální verze [Node.js](https://nodejs.org/)
* Jakmile budete mít předplatné Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" vytvořte prostředek Advisoru metriky "  target="_blank"> vytvořením prostředku Advisoru metriky <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal k nasazení instance služby Advisor pro metriky.  
* Vaše vlastní databáze SQL s daty časových řad.
  
> [!TIP]
> * Ukázky na GitHubu pro JavaScript metriky najdete na [GitHubu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples).
> * Pro nasazení instance služby, kterou můžete použít, může trvat 10 až 30 minut, než se dokončí váš prostředek poradce metriky. Po úspěšném nasazení klikněte na **Přejít k prostředku** . Po nasazení můžete začít používat vaši instanci poradce metriky s webovým portálem i REST API. 
> * Adresu URL pro REST API můžete najít v Azure Portal v části **Přehled** prostředku. Bude vypadat takto:
>    * `https://<instance-name>.cognitiveservices.azure.com/`
    
## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```

Spuštěním `npm init` příkazu vytvořte aplikaci uzlu se `package.json` souborem. 

```console
npm init
```

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Nainstalujte `@azure/ai-metrics-advisor` balíček npm:

```console
npm install @azure/ai-metrics-advisor@1.0.0-beta.1
```

Soubor vaší aplikace `package.json` bude aktualizován pomocí závislostí.

Vytvořte soubor s názvem `index.js` a importujte následující knihovny:

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít na [GitHubu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples/javascript), který obsahuje příklady kódu v tomto rychlém startu.

Vytvořte proměnné pro koncový bod a klíč Azure prostředku. 

> [!IMPORTANT]
> Přejděte na Azure Portal. Pokud se prostředek poradce metriky, který jste vytvořili v části **předpoklady** , úspěšně nasadil, klikněte v části **Další kroky** na tlačítko **Přejít k prostředku** . Klíče předplatného a koncový bod můžete najít na stránce **klíč a koncový bod** prostředku v části **Správa prostředků** . <br><br>Pokud chcete načíst klíč rozhraní API, musíte přejít na [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) . Vyberte příslušný **adresář** , **odběry** a **pracovní prostor** pro váš prostředek a zvolte možnost **začít** . Pak budete moci načíst klíče rozhraní API z [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) .   
>
> Nezapomeňte odebrat klíč z kódu, až budete hotovi, a nikdy ho zveřejnit. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Další informace najdete v článku o [zabezpečení](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) Cognitive Services.

```javascript
subscriptionKey = "<paste-your-metrics-advisor-key-here>";
apiKey ="<paste-your-metrics-advisor-api-key-here>";
endpoint = "<paste-your-metrics-advisor-endpoint-here>";
```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé z hlavních funkcí sady SDK pro rutiny metriky s metrikami jazyka JavaScript.

|Název|Popis|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/javascript/azure-ai-metrics-advisor/1.0.0-beta.1/classes/metricsadvisorclient.html) | **Používá se pro** : <br> -Výpis incidentů <br> -Výpis hlavní příčiny incidentů <br> – Načítání původních dat časových řad a dat časových řad obohacených službou. <br> -Výpis výstrah <br> – Přidání zpětné vazby k ladění modelu |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/javascript/azure-ai-metrics-advisor/1.0.0-beta.1/classes/metricsadvisoradministrationclient.html)| **Umožňuje:** <br> – Správa datových kanálů <br> – Vytváření, konfigurace, načítání, zobrazování a odstraňování konfigurací upozorňujících na anomálii <br> -Spravovat zavěšení  |
| [Datové kanály](https://azuresdkdocs.blob.core.windows.net/$web/javascript/azure-ai-metrics-advisor/1.0.0-beta.1/interfaces/datafeed.html)| **Jaké metriky Advisoru ingestují ze zdroje dat. `DataFeed` Obsahuje řádky:** <br> – Časová razítka <br> – Nula nebo více dimenzí <br> – Jedna nebo více měr  |
| [Metrika](https://azuresdkdocs.blob.core.windows.net/$web/javascript/azure-ai-metrics-advisor/1.0.0-beta.1/interfaces/metric.html) | A `Metric` je kvantifikovaná míra, která se používá k monitorování a vyhodnocení stavu konkrétního obchodního procesu. Může se jednat o kombinaci více hodnot časových řad rozdělených do dimenzí. Například metrika stavu webu může obsahovat rozměry počtu uživatelů a trhu en-US. |

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí klientské knihovny Poradce pro metriky pro JavaScript:

* [Ověření klienta](#authenticate-the-client)
* [Přidání datového kanálu](#add-a-data-feed)
* [Ověřit stav ingestování](#check-ingestion-status)
* [Konfigurovat detekci anomálií](#configure-anomaly-detection)
* [Vytvořit zavěšení](#create-a-hook)
* [Vytvoření konfigurace výstrahy](#create-an-alert-configuration)
* [Dotaz na upozornění](#query-the-alert)

## <a name="authenticate-the-client"></a>Ověření klienta

Jakmile budete mít dvě klíče a adresu koncového bodu, můžete použít třídu MetricsAdvisorKeyCredential k ověřování klientů následujícím způsobem:

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorClient,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
const client = new MetricsAdvisorClient(endpoint, credential);
const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
```

## <a name="add-a-data-feed"></a>Přidání datového kanálu

Poradce metrik podporuje připojení různých typů zdrojů dat. Zde je ukázka pro ingestování dat z SQL Server.

Nahraďte `sql_server_connection_string` vlastním připojovacím řetězcem SQL serveru a nahraďte `query` dotazem, který vrací vaše data v jednom časovém razítku. Také budete muset upravit `metric` hodnoty a na `dimension` základě vlastních dat.

> [!IMPORTANT]
> Dotaz by měl vracet maximálně jeden záznam pro každou kombinaci dimenzí v každém časovém razítku. A všechny záznamy vrácené dotazem musí mít stejná časová razítka. Poradce metriky spustí tento dotaz pro každé časové razítko k ingestování vašich dat. Další informace a příklady najdete v [části Nejčastější dotazy k dotazům](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) . 

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  subscriptionKey = "<paste-your-metrics-advisor-key-here>";
  apiKey ="<paste-your-metrics-advisor-api-key-here>";
  endpoint = "<paste-your-metrics-advisor-endpoint-here>";
  const sqlServerConnectionString ="<sql_server_connection_string>";
  const sqlServerQuery ="<query>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);

  const created = await createDataFeed(adminClient, sqlServerConnectionString, sqlServerQuery);
  console.log(`Data feed created: ${created.id}`);
}

async function createDataFeed(adminClient, sqlServerConnectionString, sqlServerQuery) {
  const metric = [
    {
      name: "revenue",
      displayName: "revenue",
      description: "Metric1 description"
    },
    {
      name: "cost",
      displayName: "cost",
      description: "Metric2 description"
    }
  ];
  const dimension = [
    { name: "city", displayName: "city display" },
    { name: "category", displayName: "category display" }
  ];
  const dataFeedSchema = {
    metrics: metric,
    dimensions: dimension,
    timestampColumn: null
  };
  const dataFeedIngestion = {
    ingestionStartTime: new Date(Date.UTC(2020, 5, 1)),
    ingestionStartOffsetInSeconds: 0,
    dataSourceRequestConcurrency: -1,
    ingestionRetryDelayInSeconds: -1,
    stopRetryAfterInSeconds: -1
  };
  const granualarity = {
    granularityType: "Daily"
  };
  const source = {
    dataSourceType: "SqlServer",
    dataSourceParameter: {
      connectionString: sqlServerConnectionString,
      query: sqlServerQuery
    }
  };
  const options = {
    rollupSettings: {
      rollupType: "AutoRollup",
      rollupMethod: "Sum",
      rollupIdentificationValue: "__CUSTOM_SUM__"
    },
    missingDataPointFillSettings: {
      fillType: "SmartFilling"
    },
    accessMode: "Private",
    admins: ["xyz@example.com"]
  };

  console.log("Creating Datafeed...");
  const result = await adminClient.createDataFeed({
    name: "test_datafeed_" + new Date().getTime().toFixed(),
    source,
    granularity,
    schema: dataFeedSchema,
    ingestionSettings: dataFeedIngestion,
    options
  });

  return result;
}
```

## <a name="check-ingestion-status"></a>Ověřit stav ingestování

Po zahájení příjmu dat můžeme kontrolovat stav příjmu.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const dataFeedId = "<data feed id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  await checkIngestionStatus(
    adminClient,
    dataFeedId,
    new Date(Date.UTC(2020, 8, 1)),
    new Date(Date.UTC(2020, 8, 12))
  );
}

async function checkIngestionStatus(adminClient, datafeedId, startTime, endTime) {
  // This shows how to use for-await-of syntax to list status
  console.log("Checking ingestion status...");
  for await (const status of adminClient.listDataFeedIngestionStatus(
    datafeedId,
    startTime,
    endTime
  )) {
    console.log(`  [${status.timestamp}] ${status.status} - ${status.message}`);
  }
}
```

## <a name="configure-anomaly-detection"></a>Konfigurovat detekci anomálií

Abychom zjistili, jestli je bod v časové řadě anomálií, potřebujeme konfiguraci detekce anomálií. I když je výchozí konfigurace zjišťování automaticky použita u každé metriky, můžete vyladit režimy detekce používané na vašich datech vytvořením vlastní konfigurace detekce anomálií.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const metricId =  "<metric id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);

  const detectionConfig = await configureAnomalyDetectionConfiguration(adminClient, metricId);
  console.log(`Detection configuration created: ${detectionConfig.id}`);
}

async function configureAnomalyDetectionConfiguration(adminClient, metricId) {
  console.log(`Creating an anomaly detection configuration on metric '${metricId}'...`);
  return await adminClient.createMetricAnomalyDetectionConfiguration({
    name: "test_detection_configuration" + new Date().getTime().toString(),
    metricId,
    wholeSeriesDetectionCondition: {
      smartDetectionCondition: {
        sensitivity: 100,
        anomalyDetectorDirection: "Both",
        suppressCondition: {
          minNumber: 1,
          minRatio: 1
        }
      }
    },
    description: "Detection configuration description"
  });
}
```

### <a name="create-a-hook"></a>Vytvořit zavěšení

K přihlášení k odběru výstrah v reálném čase používáme háky. V tomto příkladu vytvoříme Webhook pro službu Advisoru metrik, do které bude tato výstraha publikovat.

```javascript

const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  const hook = await createWebhookHook(adminClient);
  console.log(`Webhook hook created: ${hook.id}`);
}

async function createWebhookHook(adminClient) {
  console.log("Creating a webhook hook");
  const hook = {
    hookType: "Webhook",
    name: "web hook " + new Date().getTime().toFixed(),
    description: "description",
    hookParameter: {
      endpoint: "https://example.com/handleAlerts",
      username: "username",
      password: "password"
      // certificateKey: "certificate key",
      // certificatePassword: "certificate password"
    }
  };

  return await adminClient.createHook(hook);
}
```

##  <a name="create-an-alert-configuration"></a>Vytvoření konfigurace výstrahy

V této ukázce se dozvíte, jak nakonfigurovat podmínky, které musí být spuštěny a které se zařadí k použití jako cíle výstrahy.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const detectionConfigId = "<detection id>";
  const hookId = "<hook id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  const alertConfig = await configureAlertConfiguration(adminClient, detectionConfigId, [hookId]);
  console.log(`Alert configuration created: ${alertConfig.id}`);
}

async function configureAlertConfiguration(adminClient, detectionConfigId, hookIds) {
  console.log("Creating a new alerting configuration...");
  const metricAlertingConfig = {
    detectionConfigurationId: detectionConfigId,
    alertScope: {
      scopeType: "All"
    },
    alertConditions: {
      severityCondition: { minAlertSeverity: "Medium", maxAlertSeverity: "High" }
    },
    snoozeCondition: {
      autoSnooze: 0,
      snoozeScope: "Metric",
      onlyForSuccessive: true
    }
  };
  return await adminClient.createAnomalyAlertConfiguration({
    name: "test_alert_config_" + new Date().getTime().toString(),
    crossMetricsOperator: "AND",
    metricAlertConfigurations: [metricAlertingConfig],
    hookIds,
    description: "Alerting config description"
  });
}
```

## <a name="query-the-alert"></a>Dotaz na upozornění

```javascript
const { MetricsAdvisorKeyCredential, MetricsAdvisorClient } = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const alertConfigId = "<alert config id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const client = new MetricsAdvisorClient(endpoint, credential);

  const alertIds = await queryAlerts(
    client,
    alertConfigId,
    new Date(Date.UTC(2020, 8, 1)),
    new Date(Date.UTC(2020, 8, 12))
  );

  if (alertIds.length > 1) {
    // query anomalies using an alert id.
    await queryAnomaliesByAlert(client, alertConfigId, alertIds[0]);
  } else {
    console.log("No alerts during the time period");
  }
}

async function queryAlerts(client, alertConfigId, startTime, endTime) {
  let alertIds = [];
  for await (const alert of client.listAlertsForAlertConfiguration(
    alertConfigId,
    startTime,
    endTime,
    "AnomalyTime"
  )) {
    alertIds.push(alert.id);
  }

  return alertIds;
}

async function queryAnomaliesByAlert(client, alertConfigId, alertId) {
  console.log(
    `Listing anomalies for alert configuration '${alertConfigId}' and alert '${alertId}'`
  );
  for await (const anomaly of client.listAnomaliesForAlert(alertConfigId, alertId)) {
    console.log(
      `  Anomaly ${anomaly.severity} ${anomaly.status} ${anomaly.dimension} ${anomaly.timestamp}`
    );
  }
}
```

### <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí `node` příkazu v souboru rychlého startu.

```console
node index.js
```