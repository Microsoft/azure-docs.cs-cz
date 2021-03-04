---
title: Kurz – připojení a monitorování metrik na úrovni aplikace v Azure synapse Spark
description: Kurz – Přečtěte si, jak integrovat svůj stávající místní Prometheus Server se službou Azure synapse Workspace pro metriky aplikací Azure Spark v reálném čase pomocí konektoru synapse Prometheus.
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: bd04c692655161a2ba8d4ff51a8ff07e9b9bd374
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101696108"
---
# <a name="tutorial-connect-and-monitor-azure-synapse-spark-application-level-metrics"></a>Kurz: připojení a monitorování metrik na úrovni aplikace v Azure synapse Spark

## <a name="overview"></a>Přehled

V tomto kurzu se dozvíte, jak integrovat svůj stávající místní Prometheus Server se službou Azure synapse Workspace pro Azure Spark v reálném čase pomocí konektoru synapse Prometheus. 

Tento kurz obsahuje také rozhraní API REST služby Azure synapse. Data metrik aplikace Spark můžete načíst prostřednictvím rozhraní REST API, abyste mohli vytvářet vlastní nástroje pro monitorování a diagnostiku nebo je integrovat s monitorovacími systémy.

## <a name="use-azure-synapse-prometheus-connector-for-your-on-premises-prometheus-servers"></a>Použití konektoru Azure synapse Prometheus pro místní servery Prometheus

[Konektor Azure synapse Prometheus](https://github.com/microsoft/azure-synapse-spark-metrics) je open source projekt. Konektor synapse Prometheus používá metodu zjišťování služby na základě souborů, která umožňuje:
 - Ověřování v pracovním prostoru synapse prostřednictvím instančního objektu služby AAD.
 - Načte seznam Apache Sparkch aplikací v pracovním prostoru. 
 - Vyžádejte metriky aplikace Spark prostřednictvím Prometheus konfigurace na základě souborů. 

### <a name="1-prerequisite"></a>1. předpoklad

Musíte mít nasazený Server Prometheus na virtuálním počítači se systémem Linux.

### <a name="2-create-a-service-principal"></a>2. Vytvoření instančního objektu

Pokud chcete použít konektor Azure synapse Prometheus na místním Prometheus serveru, postupujte podle následujících kroků a vytvořte instanční objekt.

#### <a name="21-create-a-service-principal"></a>2,1 Vytvoření instančního objektu:

```bash
az ad sp create-for-rbac --name <service_principal_name>
```

Výsledek by měl vypadat takto:

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

Poznamenejte si appId, heslo a ID tenanta.

#### <a name="22-add-corresponding-permissions-to-the-service-principal-created-in-the-above-step"></a>2,2 přidejte odpovídající oprávnění k objektu služby vytvořenému ve výše uvedeném kroku.

![srbac oprávnění udělení obrazovky](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)

1. Přihlaste se ke svému [pracovnímu prostoru Azure synapse Analytics](https://web.azuresynapse.net/) jako správce synapse.

2. V synapse studiu v levém podokně vyberte **spravovat > řízení přístupu** .

3. Kliknutím na tlačítko Přidat v levém horním rohu **přidáte přiřazení role** .

4. Pro rozsah vyberte **pracovní prostor** .

5. V případě role vyberte **operátor synapse COMPUTE** .

6. Pro možnost vybrat uživatele zadejte **<service_principal_name>** a klikněte na instanční objekt.

7. Klikněte na **použít** (počkejte 3 minuty, než se oprávnění projeví.)


### <a name="3-download-the-azure-synapse-prometheus-connector"></a>3. Stáhněte si konektor Azure synapse Prometheus.

Pomocí příkazů nainstalujte konektor Azure synapse Prometheus.

```bash
git clone https://github.com/microsoft/azure-synapse-spark-metrics.git
cd ./azure-synapse-spark-metrics/synapse-prometheus-connector/src
python pip install -r requirements.txt
```

### <a name="4-create-a-config-file-for-azure-synapse-workspaces"></a>4. vytvoření konfiguračního souboru pro pracovní prostory Azure synapse

Vytvořte soubor config. yaml ve složce config a vyplňte následující pole: workspace_name, tenant_id, service_principal_name a service_principal_password.
V konfiguraci YAML můžete přidat několik pracovních prostorů.

```yaml
workspaces:
  - workspace_name: <your_workspace_name>
    tenant_id: <tenant_id>
    service_principal_name: <service_principal_app_id>
    service_principal_password: "<service_principal_password>"
```

### <a name="5-update-the-prometheus-config"></a>5. aktualizace konfigurace Prometheus

Do Prometheus scrape_config přidejte následující konfigurační oddíl a nahraďte <your_workspace_name> na název pracovního prostoru a <path_to_synapse_connector> k naklonované složce synapse-Prometheus-Connector.

```yaml
- job_name: synapse-prometheus-connector
  static_configs:
  - labels:
      __metrics_path__: /metrics
      __scheme__: http
    targets:
    - localhost:8000
- job_name: synapse-workspace-<your_workspace_name>
  bearer_token_file: <path_to_synapse_connector>/output/workspace/<your_workspace_name>/bearer_token
  file_sd_configs:
  - files:
    - <path_to_synapse_connector>/output/workspace/<your_workspace_name>/application_discovery.json
    refresh_interval: 10s
  metric_relabel_configs:
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_application_[0-9]+_[0-9]+_(.+)
    replacement: spark_$1
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_(.+)
    replacement: spark_$1
```


### <a name="6-start-the-connector-in-the-prometheus-server-vm"></a>6. Spusťte konektor na virtuálním počítači Prometheus serveru.

Spusťte na virtuálním počítači s Prometheus serverem konektorový server následujícím způsobem.

```
python main.py
```

Počkejte několik sekund a konektor by měl začít pracovat. A na stránce pro zjišťování služby Prometheus můžete zobrazit "synapse-Prometheus-Connector".

## <a name="use-azure-synapse-prometheus-or-rest-metrics-apis-to-collect-metrics-data"></a>Shromažďování dat metrik pomocí rozhraní API metrik služby Azure synapse Prometheus nebo REST

### <a name="1-authentication"></a>1. ověřování
K získání přístupového tokenu můžete použít tok přihlašovacích údajů klienta. Pro přístup k rozhraní API metrik byste měli získat přístupový token služby Azure AD pro instanční objekt, který má správné oprávnění pro přístup k rozhraním API.

| Parametr     | Povinné | Popis                                                                                                   |
| ------------- | -------- | ------------------------------------------------------------------------------------------------------------- |
| tenant_id     | Ano     | ID tenanta instančního objektu (aplikace) Azure                                                          |
| grant_type    | Ano     | Určuje požadovaný typ udělení. V toku udělení přihlašovacích údajů klienta musí být hodnota client_credentials. |
| client_id     | Ano     | ID aplikace (instanční objekt) aplikace, kterou jste zaregistrovali v Azure Portal nebo Azure CLI.        |
| client_secret | Ano     | Tajný kód generovaný pro aplikaci (instanční objekt)                                                  |
| prostředek      | Ano     | Identifikátor URI prostředku synapse by měl být https://dev.azuresynapse.net                                                  |

```bash
curl -X GET -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'grant_type=client_credentials&client_id=<service_principal_app_id>&resource=<azure_synapse_resource_id>&client_secret=<service_principal_secret>' \
  https://login.microsoftonline.com/<tenant_id>/oauth2/token
```

Odpověď vypadá jako:

```json
{
  "token_type": "Bearer",
  "expires_in": "599",
  "ext_expires_in": "599",
  "expires_on": "1575500666",
  "not_before": "1575499766",
  "resource": "2ff8...f879c1d",
  "access_token": "ABC0eXAiOiJKV1Q......un_f1mSgCHlA"
}
```

### <a name="2-list-running-applications-in-the-azure-synapse-workspace"></a>2. seznam spuštěných aplikací v pracovním prostoru Azure synapse

Pokud chcete získat seznam aplikací Sparku pro pracovní prostor synapse, můžete postupovat podle tohoto dokumentu [monitorování – získat seznam úloh Spark](https://docs.microsoft.com/rest/api/synapse/data-plane/monitoring/getsparkjoblist).


### <a name="3-collect-spark-application-metrics-with-the-prometheus-or-rest-apis"></a>3. Shromážděte metriky aplikací Spark s rozhraními API Prometheus nebo REST.


#### <a name="collect-spark-application-metrics-with-the-prometheus-api"></a>Shromažďování metrik aplikace Spark s rozhraním API Prometheus

Načíst nejnovější metriky zadané aplikace Spark pomocí rozhraní Prometheus API

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/metrics/executors/prometheus?format=html
```

| Parametr          | Povinné | Popis                                                                               |
| ------------------ | -------- | ----------------------------------------------------------------------------------------- |
| endpoint           | Ano     | Koncový bod pro vývoj v pracovním prostoru, například https://myworkspace.dev.azuresynapse.net . |
| livyApiVersion     | Ano     | Platná verze API-Version pro požadavek. V současné době je to 2019-11-01 – Preview                    |
| sparkPoolName      | Ano     | Název fondu Spark.                                                                   |
| sessionId          | Ano     | Identifikátor relace                                                               |
| sparkApplicationId | Ano     | ID aplikace Spark                                                                      |

Vzorový požadavek: 

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/metrics/executors/prometheus?format=html
```

Ukázková odpověď:

Stavový kód: 200 reakce vypadá takto:

```
metrics_executor_rddBlocks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_memoryUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 74992
metrics_executor_diskUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_totalCores{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_maxTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_activeTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 1
metrics_executor_failedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_completedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 2
...

```

#### <a name="collect-spark-application-metrics-with-the-rest-api"></a>Shromažďování metrik aplikace Spark pomocí REST API

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/executors
```

| Parametr          | Povinné | Popis                                                                               |
| ------------------ | -------- | ----------------------------------------------------------------------------------------- |
| endpoint           | Ano     | Koncový bod pro vývoj v pracovním prostoru, například https://myworkspace.dev.azuresynapse.net . |
| livyApiVersion     | Ano     | Platná verze API-Version pro požadavek. V současné době je to 2019-11-01 – Preview                    |
| sparkPoolName      | Ano     | Název fondu Spark.                                                                   |
| sessionId          | Ano     | Identifikátor relace                                                               |
| sparkApplicationId | Ano     | ID aplikace Spark                                                                      |

Ukázkový požadavek

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/executors
```

Ukázkový kód stavu odpovědi: 200

```json
[
    {
        "id": "driver",
        "hostPort": "f98b8fc2aea84e9095bf2616208eb672007bde57624:45889",
        "isActive": true,
        "rddBlocks": 0,
        "memoryUsed": 75014,
        "diskUsed": 0,
        "totalCores": 0,
        "maxTasks": 0,
        "activeTasks": 0,
        "failedTasks": 0,
        "completedTasks": 0,
        "totalTasks": 0,
        "totalDuration": 0,
        "totalGCTime": 0,
        "totalInputBytes": 0,
        "totalShuffleRead": 0,
        "totalShuffleWrite": 0,
        "isBlacklisted": false,
        "maxMemory": 15845975654,
        "addTime": "2020-11-16T06:55:06.718GMT",
        "executorLogs": {
            "stdout": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stdout?start=-4096",
            "stderr": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stderr?start=-4096"
        },
        "memoryMetrics": {
            "usedOnHeapStorageMemory": 75014,
            "usedOffHeapStorageMemory": 0,
            "totalOnHeapStorageMemory": 15845975654,
            "totalOffHeapStorageMemory": 0
        },
        "blacklistedInStages": []
    },
    // ...
]
```


### <a name="4-build-your-own-diagnosis-and-monitoring-tools"></a>4. Sestavte si vlastní nástroje pro diagnostiku a monitorování

Rozhraní API Prometheus a rozhraní REST API poskytují bohatou datovou metriku o spuštěných informacích aplikace Spark. Data metriky související s aplikací můžete shromažďovat prostřednictvím rozhraní API Prometheus a rozhraní REST API. A Sestavujte vlastní nástroje pro diagnostiku a monitorování, které jsou vhodnější pro vaše potřeby.
