---
title: Shromažďování a vizualizace metrik a protokolů pomocí Azure Log Analytics (Náhled)
description: Naučte se, jak povolit integrovaný konektor Azure Log Analytics pro shromažďování a odesílání Apache Sparkch metrik a protokolů aplikací do vašeho pracovního prostoru Azure Log Analytics.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 03/25/2021
ms.custom: references_regions
ms.openlocfilehash: 243618192593d93bba9d5229e7becfb2af62ce32
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108541"
---
# <a name="tutorial-use-azure-log-analytics-to-collect-and-visualize-metrics-and-logs-preview"></a>Kurz: použití služby Azure Log Analytics ke shromažďování a vizualizaci metrik a protokolů (Preview)

V tomto kurzu se dozvíte, jak povolit synapse konektor služby Azure Log Analytics pro shromažďování a odesílání Apache Spark metriky a protokolů aplikací do vašeho [pracovního prostoru azure Log Analytics](/azure/azure-monitor/logs/quick-create-workspace). Pak můžete využít sešit Azure monitor k vizualizaci metrik a protokolů.

## <a name="configure-azure-log-analytics-workspace-information-in-synapse-studio"></a>Konfigurace informací o pracovním prostoru Azure Log Analytics v synapse studiu

### <a name="step-1-create-an-azure-log-analytics-workspace"></a>Krok 1: vytvoření pracovního prostoru Azure Log Analytics

Pomocí následujících dokumentů můžete vytvořit Log Analytics pracovní prostor:
- [Vytvoření pracovního prostoru Log Analytics na webu Azure Portal](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace)
- [Vytvoření pracovního prostoru Log Analytics pomocí Azure CLI](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace-cli)
- [Vytvoření a konfigurace pracovního prostoru Log Analytics v Azure Monitor pomocí prostředí PowerShell](https://docs.microsoft.com/azure/azure-monitor/logs/powershell-workspace-configuration)

### <a name="step-2-prepare-a-spark-configuration-file"></a>Krok 2: Příprava konfiguračního souboru Spark

#### <a name="option-1-configure-with-azure-log-analytics-workspace-id-and-key"></a>Možnost 1. Konfigurace pomocí ID a klíče pracovního prostoru Azure Log Analytics 

Zkopírujte následující konfiguraci Sparku, uložte ji jako **"spark_loganalytics_conf.txt"** a naplňte parametry do těchto parametrů:

   - `<LOG_ANALYTICS_WORKSPACE_ID>`: ID pracovního prostoru Azure Log Analytics.
   - `<LOG_ANALYTICS_WORKSPACE_KEY>`: Klíč Azure Log Analytics: **Azure Portal > Log Analytics správy >ch agentů pracovního prostoru Azure > primární klíč**

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.secret <LOG_ANALYTICS_WORKSPACE_KEY>
```

#### <a name="option-2-configure-with-an-azure-key-vault"></a>Možnost 2. Konfigurace pomocí Azure Key Vault

> [!NOTE]
>
> Pro uživatele, kteří budou odesílat aplikace Spark, je nutné udělit oprávnění ke čtení tajného klíče. Přečtěte si prosím [, jak poskytnout přístup k Key Vault klíčům, certifikátům a tajným klíčům pomocí řízení přístupu na základě role Azure](https://docs.microsoft.com/azure/key-vault/general/rbac-guide) .

Chcete-li nakonfigurovat Azure Key Vault pro uložení klíče pracovního prostoru, postupujte podle následujících kroků:

1. Vytvořte a přejděte k trezoru klíčů v Azure Portal
2. Na stránkách nastavení Key Vault vyberte **tajné klíče**.
3. Klikněte na **Vygenerovat/importovat**.
4. Na obrazovce **Vytvoření tajného kódu** zvolte následující hodnoty:
   - **Název**: zadejte název tajného klíče, zadejte `"SparkLogAnalyticsSecret"` jako výchozí.
   - **Hodnota**: zadejte **<LOG_ANALYTICS_WORKSPACE_KEY>** pro tajný klíč.
   - U ostatních hodnot ponechte jejich výchozí nastavení. Klikněte na **Vytvořit**.
5. Zkopírujte následující konfiguraci Sparku, uložte ji jako **"spark_loganalytics_conf.txt"** a naplňte parametry do těchto parametrů:

   - `<LOG_ANALYTICS_WORKSPACE_ID>`: ID pracovního prostoru Azure Log Analytics.
   - `<AZURE_KEY_VAULT_NAME>`: Název Azure Key Vault, který jste nakonfigurovali.
   - `<AZURE_KEY_VAULT_SECRET_KEY_NAME>` (Volitelné): název tajného kódu v Azure Key Vault pro klíč pracovního prostoru, výchozí: "SparkLogAnalyticsSecret".

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
```

> [!NOTE]
>
> Do trezoru klíčů Azure můžete také uložit Log Analytics ID pracovního prostoru. Přečtěte si výše uvedené kroky a uložte ID pracovního prostoru s tajným názvem `"SparkLogAnalyticsWorkspaceId"` . Nebo použijte konfiguraci `spark.synapse.logAnalytics.keyVault.key.workspaceId` k zadání tajného názvu ID pracovního prostoru v trezoru klíčů Azure.

#### <a name="option-3-configure-with-an-azure-key-vault-linked-service"></a>Možnost 3. Konfigurace pomocí propojené služby Azure Key Vault

> [!NOTE]
>
> Pro pracovní prostor synapse je nutné udělit oprávnění ke čtení tajného klíče. Přečtěte si prosím [, jak poskytnout přístup k Key Vault klíčům, certifikátům a tajným klíčům pomocí řízení přístupu na základě role Azure](https://docs.microsoft.com/azure/key-vault/general/rbac-guide) .

Pokud chcete v synapse studiu nakonfigurovat propojenou službu Azure Key Vault pro uložení klíče pracovního prostoru, postupujte podle těchto pokynů:

1. Postupujte podle všech kroků v `Option 2. Configure with an Azure Key Vault` části.
2. Vytvořte propojenou službu trezoru klíčů Azure v synapse studiu:

    a. Přejděte na **synapse Studio > spravovat > propojené služby**, klikněte na tlačítko **Nový** .

    b. Do vyhledávacího pole zadejte **Azure Key Vault** .

    c. Zadejte název propojené služby.

    d. Vyberte svůj Trezor klíčů Azure. Klikněte na **Vytvořit**.

3. Přidejte `spark.synapse.logAnalytics.keyVault.linkedServiceName` položku do konfigurace Sparku.

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
spark.synapse.logAnalytics.keyVault.linkedServiceName <LINKED_SERVICE_NAME>
```

#### <a name="available-spark-configuration"></a>Dostupná konfigurace Sparku

| Název konfigurace                                  | Výchozí hodnota                | Description                                                                                                                                                                                                |
| --------------------------------------------------- | ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Spark. synapse. logAnalytics. Enabled                  | false (nepravda)                        | Pokud chcete povolit jímku Azure Log Analytics pro aplikace Spark, pravda. V ostatních případech False.                                                                                                                  |
| Spark. synapse. logAnalytics. ID pracovního prostoru              | -                            | Cílové ID pracovního prostoru Azure Log Analytics                                                                                                                                                          |
| Spark. synapse. logAnalytics. Secret                   | -                            | Cílový tajný klíč pracovního prostoru Azure Log Analytics                                                                                                                                                      |
| Spark. synapse. logAnalytics. klíčů trezor. linkedServiceName   | -                            | Název propojené služby trezoru klíčů Azure pro ID a klíč pracovního prostoru Azure Log Analytics                                                                                                                       |
| spark.synapse.logAnalytics.keyVault.name            | -                            | Název trezoru klíčů Azure pro ID a klíč Azure Log Analytics                                                                                                                                                |
| Spark. synapse. logAnalytics. klíčů trezor. Key. ID pracovního prostoru | SparkLogAnalyticsWorkspaceId | Tajný název trezoru klíčů Azure pro ID pracovního prostoru Azure Log Analytics                                                                                                                                       |
| Spark. synapse. logAnalytics. klíčů trezor. Key. Secret      | SparkLogAnalyticsSecret      | Tajný název trezoru klíčů Azure pro klíč pracovního prostoru Azure Log Analytics                                                                                                                                      |
| Spark. synapse. logAnalytics. klíčů trezor. uriSuffix       | ods.opinsights.azure.com     | Cílová [přípona identifikátoru URI][uri_suffix]v pracovním prostoru Azure Log Analytics. Pokud váš pracovní prostor Azure Log Analytics není v globálním prostředí Azure, je potřeba aktualizovat příponu identifikátoru URI podle příslušného cloudu. |

> [!NOTE]  
> - Pro cloudy Azure Čína by měl být parametr Spark. synapse. logAnalytics. klíčů trezor. uriSuffix "ods.opinsights.azure.cn". 
> - Pro cloudy Azure gov by měl být parametr "Spark. synapse. logAnalytics. klíčů. uriSuffix" "ods.opinsights.azure.us". 

[uri_suffix]: https://docs.microsoft.com/azure/azure-monitor/logs/data-collector-api#request-uri


### <a name="step-3-upload-your-spark-configuration-to-a-spark-pool"></a>Krok 3: nahrání konfigurace Sparku do fondu Spark
Konfigurační soubor můžete nahrát do fondu synapse Spark v synapse studiu.

   1. Přejděte do fondu Apache Spark ve službě Azure synapse Studio (Správa-> Apache Spark fondy).
   2. Klikněte na tlačítko **"..."** na pravé straně Apache Spark fondu.
   3. Vyberte konfiguraci Apache Spark 
   4. Klikněte na **nahrát** a vyberte vytvořený **spark_loganalytics_conf.txt** .
   5. Klikněte na **nahrát** a **použít**.

      > [!div class="mx-imgBorder"]
      > ![Konfigurace fondu Spark](./media/apache-spark-azure-log-analytics/spark-pool-configuration.png)

> [!NOTE] 
>
> Veškerá aplikace Spark odeslaná do výše uvedeného fondu Spark bude používat nastavení konfigurace k nabízení metrik a protokolů aplikace Spark do zadaného pracovního prostoru Azure Log Analytics.

## <a name="submit-a-spark-application-and-view-the-logs-and-metrics-in-azure-log-analytics"></a>Odeslání aplikace Spark a zobrazení protokolů a metrik v Azure Log Analytics

 1. Aplikaci Spark můžete odeslat do fondu Spark nakonfigurovaném v předchozím kroku pomocí některého z následujících způsobů:
    - Spusťte Poznámkový blok synapse studia. 
    - Odešlete úlohu synapse Apache Spark Batch prostřednictvím definice úlohy Spark.
    - Spuštění kanálu, který obsahuje aktivitu Spark.

 2. Přejít na zadaný pracovní prostor služby Azure Log Analytics a po spuštění aplikace Spark zobrazit metriky a protokoly aplikace

## <a name="use-the-sample-azure-log-analytics-workbook-to-visualize-the-metrics-and-logs"></a>Použití ukázkového sešitu Azure Log Analytics k vizualizaci metrik a protokolů

1. [Stáhněte si sešit](https://aka.ms/SynapseSparkLogAnalyticsWorkbook) sem.
2. Otevřete a **zkopírujte** obsah souboru sešitu.
3. Přejít do sešitu Azure Log Analytics ([Azure Portal](https://portal.azure.com/) > Log Analytics sešity > pracovní prostor)
4. Otevřete **"prázdný"** sešit Azure Log Analytics v režimu **"Rozšířený editor"** (stiskněte ikonu </>).
5. **Vložte** přes libovolný JSON, který existuje.
6. Pak klikněte na **použít** a pak na **Úpravy**.

    > [!div class="mx-imgBorder"]
    > ![nový sešit](./media/apache-spark-azure-log-analytics/new-workbook.png)

    > [!div class="mx-imgBorder"]
    > ![importovat sešit](./media/apache-spark-azure-log-analytics/import-workbook.png)

Pak odešlete aplikaci Apache Spark do nakonfigurovaného fondu Spark. Po přechodu aplikace do běžícího stavu vyberte běžící aplikaci v rozevíracím seznamu sešit.

> [!div class="mx-imgBorder"]
> ![imange sešitu](./media/apache-spark-azure-log-analytics/workbook.png)

A můžete přizpůsobit sešit pomocí dotazu Kusto a nakonfigurovat výstrahy.

> [!div class="mx-imgBorder"]
> ![dotazy a výstrahy kusto](./media/apache-spark-azure-log-analytics/kusto-query-and-alerts.png)

## <a name="sample-kusto-queries"></a>Ukázkové dotazy Kusto

1. Příklad dotazování na události Spark.

   ```kusto
   SparkListenerEvent_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | order by TimeGenerated desc
   | limit 100 
   ```

2. Příklad pro dotazování ovladače aplikace Spark a prováděcích protokolů

   ```kusto
   SparkLoggingEvent_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | order by TimeGenerated desc
   | limit 100
   ```

3. Příklad metrik dotazů Spark.

   ```kusto
   SparkMetrics_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | where name_s endswith "jvm.total.used"
   | summarize max(value_d) by bin(TimeGenerated, 30s), executorId_s
   | order by TimeGenerated asc
   ```

## <a name="create-and-manage-alerts-using-azure-log-analytics"></a>Vytváření a správa výstrah pomocí Azure Log Analytics

Azure Monitor výstrahy umožňují uživatelům používat dotaz Log Analytics k vyhodnocování metrik a zaznamenání všech nastavených frekvencí a vyvolat výstrahu na základě výsledků.

Další informace najdete v tématu [Vytvoření, zobrazení a správa výstrah protokolu pomocí Azure monitor](https://docs.microsoft.com/azure/azure-monitor/alerts/alerts-log).

## <a name="limitation"></a>Omezení

 - Pracovní prostor Azure synapse Analytics se zapnutou [spravovanou virtuální sítí](https://docs.microsoft.com/azure/synapse-analytics/security/synapse-workspace-managed-vnet) není podporován.
 - Následující oblasti nejsou aktuálně podporovány:
   - USA – východ 2
   - Norsko – východ
   - Spojené arabské emiráty sever

## <a name="next-steps"></a>Další kroky

 - Naučte se [používat fond Apache Spark bez serveru v nástroji synapse Studio](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-apache-spark-pool-studio).
 - Naučte se, jak [Spustit aplikaci Spark v poznámkovém bloku](https://docs.microsoft.com/azure/synapse-analytics/spark/apache-spark-development-using-notebooks).
 - Naučte se [vytvářet Apache Spark definice úloh v nástroji synapse Studio](https://docs.microsoft.com/azure/synapse-analytics/spark/apache-spark-job-definitions).