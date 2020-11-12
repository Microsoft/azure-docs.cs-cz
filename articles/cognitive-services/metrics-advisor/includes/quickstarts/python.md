---
title: Rychlé zprovoznění Pythonu pro metriky
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 2c79773d6697ae9fb62e2b7515da60178243fe40
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94523684"
---
[Referenční dokumentace](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/README.md)  |  [Balíček (PiPy)](https://pypi.org/project/azure-ai-metricsadvisor/)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples/README.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* [Python 3.x](https://www.python.org/)
* Jakmile budete mít předplatné Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" vytvořte prostředek Advisoru metriky "  target="_blank"> vytvořením prostředku Advisoru metriky <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal k nasazení instance služby Advisor pro metriky.  
* Vaše vlastní databáze SQL s daty časových řad.
  
> [!TIP]
> * Ukázky pro poradce pro metriky Pythonu můžete najít na [GitHubu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples).
> * Pro nasazení instance služby, kterou můžete použít, může trvat 10 až 30 minut, než se dokončí váš prostředek poradce metriky. Po úspěšném nasazení klikněte na **Přejít k prostředku** . Po nasazení můžete začít používat vaši instanci poradce metriky s webovým portálem i REST API.
> * Adresu URL pro REST API můžete najít v Azure Portal v části **Přehled** prostředku. Bude vypadat takto:
>    * `https://<instance-name>.cognitiveservices.azure.com/` 
 
## <a name="setting-up"></a>Nastavení

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Po instalaci Pythonu můžete nainstalovat klientskou knihovnu pomocí nástroje:

```console
pip install azure-ai-metricsadvisor --pre
```

### <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Vytvořte nový soubor Pythonu a importujte následující knihovny.

```python
import os
import datetime
```

Vytvořte proměnné pro koncový bod a klíč Azure prostředku.

> [!IMPORTANT]
> Přejděte na Azure Portal. Pokud se prostředek poradce metriky, který jste vytvořili v části **předpoklady** , úspěšně nasadil, klikněte v části **Další kroky** na tlačítko **Přejít k prostředku** . Klíče předplatného a koncový bod můžete najít na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**. <br><br>Pokud chcete načíst klíč rozhraní API, musíte přejít na [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) . Vyberte příslušný **adresář** , **odběry** a **pracovní prostor** pro váš prostředek a zvolte možnost **začít**. Pak budete moci načíst klíče rozhraní API z [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) .   
>
> Nezapomeňte odebrat klíč z kódu, až budete hotovi, a nikdy ho zveřejnit. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Další informace najdete v článku o [zabezpečení](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) Cognitive Services.

```python
subscription_key = "<paste-your-metrics-advisor-subscription-key-here>"
api_key = "<paste-your-metrics-advisor-api-key-here>"
service_endpoint = "<paste-your-metrics-advisor-endpoint-here>"
```

## <a name="object-model"></a>Objektový model

Následující třídy zpracovávají některé z hlavních funkcí sady metrik Python SDK pro metriky.

|Název|Popis|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient) | **Používá se pro** : <br> -Výpis incidentů <br> -Výpis hlavní příčiny incidentů <br> – Načítání původních dat časových řad a dat časových řad obohacených službou. <br> -Výpis výstrah <br> – Přidání zpětné vazby k ladění modelu |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorAdministrationClient) | **Umožňuje:** <br> – Správa datových kanálů <br> – Vytváření, konfigurace, načítání, vypsání a odstraňování konfigurací detekce anomálií <br> – Vytváření, konfigurace, načítání, zobrazování a odstraňování konfigurací upozorňujících na anomálii <br> -Spravovat zavěšení  | |
| [Datové kanály](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeed#azure.ai.metricsadvisor.models.DataFeed)| **Jaké metriky Advisoru ingestují ze zdroje dat. `DataFeed` Obsahuje řádky:** <br> – Časová razítka <br> – Nula nebo více dimenzí <br> – Jedna nebo více měr  |
| [DataFeedMetric](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeedmetric#azure.ai.metricsadvisor.models.DataFeedMetric) | A `DataFeedMetric` je kvantifikovaná míra, která se používá k monitorování a vyhodnocení stavu konkrétního obchodního procesu. Může se jednat o kombinaci více hodnot časových řad rozdělených do dimenzí. Například metrika stavu webu může obsahovat rozměry počtu uživatelů a trhu en-US. |

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí klientské knihovny Poradce pro metriky pro Python:

* [Ověření klienta](#authenticate-the-client)
* [Přidání datového kanálu](#add-a-data-feed)
* [Ověřit stav ingestování](#check-the-ingestion-status)
* [Konfigurace zjišťování nastavení a konfigurace výstrah](#configure-anomaly-detection)
* [Vytvoření konfigurace výstrahy](#create-an-alert-configuration)
* [Výsledky detekce anomálií dotazů](#query-the-alert)

## <a name="authenticate-the-client"></a>Ověření klienta

Klient v tomto příkladu je `MetricsAdvisorAdministrationClient` objekt, který používá váš koncový bod a `MetricsAdvisorKeyCredential` objekt, který obsahuje vaše klíče. Tuto ukázku kódu nemusíte kopírovat. Metody, které vytvoříte později, vytvoří instanci klienta. Alternativní klient se nazývá `MetricsAdvisorClient` Další informace o tomto klientovi najdete v [referenční dokumentaci](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient).

```python
client = MetricsAdvisorAdministrationClient(service_endpoint,
                                MetricsAdvisorKeyCredential(subscription_key, api_key))
```

## <a name="add-a-data-feed"></a>Přidání datového kanálu

V nové metodě vytvořte příkazy importu jako příklad níže. Nahraďte `sql_server_connection_string` vlastním připojovacím řetězcem SQL serveru a nahraďte `query` dotazem, který vrací vaše data v jednom časovém razítku. Také budete muset upravit `DataFeedmetric` hodnoty a na `DataFeedDimension` základě vlastních dat.

> [!IMPORTANT]
> Dotaz by měl vracet maximálně jeden záznam pro každou kombinaci dimenzí v každém časovém razítku. A všechny záznamy vrácené dotazem musí mít stejná časová razítka. Poradce metriky spustí tento dotaz pro každé časové razítko k ingestování vašich dat. Další informace a příklady najdete v [části Nejčastější dotazy k dotazům](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) . 

Vytvořte klienta s vašimi klíči a koncovým bodem a použijte `client.create_data_feed()` ke konfiguraci názvu, zdroje, členitosti a schématu. Můžete také nastavit dobu příjmu, souhrn nastavení a další.


```python
def sample_create_data_feed():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        SQLServerDataFeed,
        DataFeedSchema,
        DataFeedMetric,
        DataFeedDimension,
        DataFeedOptions,
        DataFeedRollupSettings
    )
    sql_server_connection_string = "<replace-with-your-sql-server-connection-string>"
    query = "<replace-with-metrics-advisor-sql-server-query>"

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    data_feed = client.create_data_feed(
    name="My data feed",
    source=SQLServerDataFeed(
        connection_string=sql_server_connection_string,
        query=query,
    ),
    granularity="Daily",
    schema=DataFeedSchema(
        metrics=[
            DataFeedMetric(name="cost", display_name="Cost"),
            DataFeedMetric(name="revenue", display_name="Revenue")
        ],
        dimensions=[
            DataFeedDimension(name="category", display_name="Category"),
            DataFeedDimension(name="city", display_name="City")
        ],
        timestamp_column="Timestamp"
    ),
    ingestion_settings=datetime.datetime(2019, 10, 1),
    options=DataFeedOptions(
        data_feed_description="cost/revenue data feed",
        rollup_settings=DataFeedRollupSettings(
            rollup_type="AutoRollup",
            rollup_method="Sum",
            rollup_identification_value="__CUSTOM_SUM__"
        ),
        missing_data_point_fill_settings=DataFeedMissingDataPointFillSettings(
            fill_type="SmartFilling"
        ),
        access_mode="Private"
    )
)

return data_feed
sample_create_data_feed()
```

## <a name="check-the-ingestion-status"></a>Zkontroluje stav příjmu.

V nové metodě vytvořte příkaz importu podobný následujícímu příkladu. Nahraďte `data_feed_id` ID datového kanálu, který jste vytvořili. Vytvořte klienta s vašimi klíči a koncovým bodem a použijte `client.list_data_feed_ingestion_status()` k získání průběhu přijímání. Vytiskněte podrobnosti, například poslední aktivní a úspěšná časová razítka.


```python
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient

    data_feed_id = "<replace-with-your-metrics-advisor-data-feed-id>"

   client = MetricsAdvisorAdministrationClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

ingestion_status = client.list_data_feed_ingestion_status(
    data_feed_id,
    datetime.datetime(2020, 9, 20),
    datetime.datetime(2020, 9, 25)
)
for status in ingestion_status:
    print("Timestamp: {}".format(status.timestamp))
    print("Status: {}".format(status.status))
    print("Message: {}\n".format(status.message))

```

## <a name="configure-anomaly-detection"></a>Konfigurovat detekci anomálií

V nové metodě vytvořte příkazy importu jako příklad níže. Nahraďte `metric_id` ID metriky, kterou chcete nakonfigurovat. Vytvořte klienta s vašimi klíči a koncovým bodem a použijte `client.create_detection_configuration` k vytvoření nové konfigurace detekce. Mezní podmínky určují parametry pro detekci anomálií.

```python
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        ChangeThresholdCondition,
        HardThresholdCondition,
        SmartDetectionCondition,
        SuppressCondition,
        MetricDetectionCondition,
    )
    metric_id = "replace-with-your-metric-id"

    
client = MetricsAdvisorAdministrationClient(
    service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

change_threshold_condition = ChangeThresholdCondition(
    anomaly_detector_direction="Both",
    change_percentage=20,
    shift_point=10,
    within_range=True,
    suppress_condition=SuppressCondition(
        min_number=5,
        min_ratio=2
    )
)
hard_threshold_condition = HardThresholdCondition(
    anomaly_detector_direction="Up",
    upper_bound=100,
    suppress_condition=SuppressCondition(
        min_number=2,
        min_ratio=2
    )
)
smart_detection_condition = SmartDetectionCondition(
    anomaly_detector_direction="Up",
    sensitivity=10,
    suppress_condition=SuppressCondition(
        min_number=2,
        min_ratio=2
    )
)

detection_config = client.create_detection_configuration(
    name="my_detection_config",
    metric_id=metric_id,
    description="anomaly detection config for metric",
    whole_series_detection_condition=MetricDetectionCondition(
        cross_conditions_operator="OR",
        change_threshold_condition=change_threshold_condition,
        hard_threshold_condition=hard_threshold_condition,
        smart_detection_condition=smart_detection_condition
    )
)
return detection_config
```

## <a name="create-a-hook"></a>Vytvořit zavěšení

V nové metodě vytvořte příkazy importu jako příklad níže. Vytvořte klienta s vašimi klíči a koncovým bodem a použijte `client.create_hook()` k vytvoření zavěšení. Zadejte popis, seznam e-mailů, na které se má výstraha odeslat, a externí odkaz pro příjem této výstrahy.  

```python
def sample_create_hook():

    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import EmailNotificationHook

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

client = MetricsAdvisorAdministrationClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key))

hook = client.create_hook(
    hook=EmailNotificationHook(
        name="email hook",
        description="my email hook",
        emails_to_alert=["alertme@alertme.com"],
        external_link="https://example.com/handleAlerts", # you must enter a valid webhook url to post the alert payload
    )
)
```

##  <a name="create-an-alert-configuration"></a>Vytvoření konfigurace výstrahy

V nové metodě vytvořte příkazy importu jako příklad níže. Nahraďte `detection_configuration_id` ID pro vaši konfiguraci detekce anomálií a nahraďte jím `hook_id` vytvořeným zavěšením. Vytvořte klienta s vašimi klíči a koncovým bodem a použijte `client.create_alert_configuration()` k vytvoření konfigurace výstrahy. 

```python
def sample_create_alert_config():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        MetricAlertConfiguration,
        MetricAnomalyAlertScope,
        TopNGroupScope,
        MetricAnomalyAlertConditions,
        SeverityCondition,
        MetricBoundaryCondition,
        MetricAnomalyAlertSnoozeCondition
    )
    anomaly_detection_configuration_id = "<replace-with-your-detection-configuration-id"
    hook_id = "<replace-with-your-hook-id>"

    client = MetricsAdvisorAdministrationClient(
    service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

alert_config = client.create_alert_configuration(
        name="my alert config",
        description="alert config description",
        cross_metrics_operator="AND",
        metric_alert_configurations=[
            MetricAlertConfiguration(
                detection_configuration_id=detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="WholeSeries"
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    severity_condition=SeverityCondition(
                        min_alert_severity="Low",
                        max_alert_severity="High"
                    )
                )
            ),
            MetricAlertConfiguration(
                detection_configuration_id=detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="TopN",
                    top_n_group_in_scope=TopNGroupScope(
                        top=10,
                        period=5,
                        min_top_count=5
                    )
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    metric_boundary_condition=MetricBoundaryCondition(
                        direction="Up",
                        upper=50
                    )
                ),
                alert_snooze_condition=MetricAnomalyAlertSnoozeCondition(
                    auto_snooze=2,
                    snooze_scope="Metric",
                    only_for_successive=True
                )
            ),
        ],
        hook_ids=[hook_id]
    )

    return alert_config
```

### <a name="query-the-alert"></a>Dotaz na upozornění

V nové metodě vytvořte příkaz importu podobný následujícímu příkladu. Nahraďte `alert_id` ID upozornění a nahraďte `alert_config_id` ID konfigurace výstrahy. Vytvořte klienta s vašimi klíči a koncovým bodem a použijte `client.list_anomalies` k vypsání anomálií výstrahy. 

```python
from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorClient
    
alert_id = "<replace-with-your-alert-id>"
alert_config_id = "<replace-with-your-alert-configuration-id"

client = MetricsAdvisorClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

results = client.list_alerts(
    alert_configuration_id=alert_config_id,
    start_time=datetime.datetime(2020, 1, 1),
    end_time=datetime.datetime(2020, 9, 9),
    time_mode="AnomalyTime",
)
for result in results:
    print("Alert id: {}".format(result.id))
    print("Create on: {}".format(result.created_on))

results = client.list_anomalies(
    alert_configuration_id=alert_config_id,
    alert_id=alert_id,
)
for result in results:
    print("Create on: {}".format(result.created_on))
    print("Severity: {}".format(result.severity))
    print("Status: {}".format(result.status))
```

### <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí `python` příkazu v souboru rychlého startu.

```console
python quickstart-file.py
```