---
title: Monitorování datových továren pomocí Azure Monitor
description: Naučte se používat Azure Monitor k monitorování kanálů Data Factory/Azure povolením diagnostických protokolů s informacemi z Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: 3f97db2e2722d16c3fa780dbe7205813c0e75420
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84655576"
---
# <a name="monitor-and-alert-data-factory-by-using-azure-monitor"></a>Monitorování a Data Factory výstrah pomocí Azure Monitor

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Cloudové aplikace jsou komplexní a obsahují mnoho pohyblivých částí. Monitory poskytují data, která vám pomůžou zajistit, aby vaše aplikace zůstaly v dobrém stavu. Monitory také umožňují vyhnout se potenciálním problémům a řešit předchozí problémy. Data monitorování můžete využít k získání podrobných informací o vašich aplikacích. Tato znalostní báze vám pomůže zlepšit výkon a udržovatelnost aplikace. Pomáhá také automatizovat akce, které jinak vyžadují ruční zásah.

Azure Monitor poskytuje základní metriky a protokoly infrastruktury na základní úrovni pro většinu služeb Azure. Diagnostické protokoly Azure jsou vydávány prostředkem a poskytují bohatou a častou data o provozu daného prostředku. Azure Data Factory může zapisovat diagnostické protokoly v Azure Monitor. Pokud chcete tuto funkci seznámit a předvedení této funkce, podívejte se na následující video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

Další podrobnosti najdete v tématu [přehled Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor).

## <a name="keeping-azure-data-factory-metrics-and-pipeline-run-data"></a>Udržování metrik Azure Data Factory a spuštění dat kanálu

Data Factory ukládá data o běhu kanálů jenom za 45 dní. Použijte Azure Monitor, pokud chcete uchovávat data delší dobu. S monitorováním můžete směrovat diagnostické protokoly pro analýzu na více různých cílů.

* **Účet úložiště**: uložte diagnostické protokoly do účtu úložiště pro auditování nebo ruční kontrolu. Nastavení diagnostiky můžete použít k určení doby uchování ve dnech.
* **Centrum událostí**: Streamujte protokoly do Azure Event Hubs. Protokoly se stanou vstupem do partnerské služby nebo do vlastního řešení pro analýzy, jako je Power BI.
* **Log Analytics**: Analyzujte protokoly pomocí Log Analytics. Data Factory integrace s Azure Monitor je užitečná v následujících scénářích:
  * Chcete zapisovat komplexní dotazy na bohatou sadu metrik, která je publikována nástrojem Data Factory k monitorování. Můžete vytvářet vlastní výstrahy na těchto dotazech prostřednictvím monitorování.
  * Chcete monitorovat napříč datovými továrnami. Data z několika datových továrn můžete směrovat do jednoho pracovního prostoru monitorování.

Můžete použít také účet úložiště nebo obor názvů centra událostí, který není v předplatném prostředku, který vysílá protokoly. Uživatel, který konfiguruje nastavení, musí mít k oběma předplatným vhodný přístup řízení přístupu na základě role (RBAC).

## <a name="configure-diagnostic-settings-and-workspace"></a>Konfigurovat nastavení diagnostiky a pracovní prostor

Vytvořte nebo přidejte nastavení diagnostiky pro datovou továrnu.

1. Na portálu klikněte na monitorování. Vyberte **Nastavení**  >  **diagnostiky**.

1. Vyberte objekt pro vytváření dat, pro který chcete nastavit nastavení diagnostiky.

1. Pokud ve vybrané datové továrně žádná nastavení neexistují, budete vyzváni k vytvoření nastavení. Vyberte **zapnout diagnostiku**.

   ![Vytvořit nastavení diagnostiky, pokud neexistuje žádné nastavení](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Pokud v datové továrně existují nastavení, zobrazí se seznam nastavení, která jsou už nakonfigurovaná u objektu pro vytváření dat. Vyberte **Přidat nastavení diagnostiky**.

   ![Pokud existují nastavení, přidejte nastavení diagnostiky.](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Zadejte název nastavení, vyberte **Odeslat do Log Analytics**a pak vyberte pracovní prostor z **pracovního prostoru Log Analytics**.

    * V režimu _specifickém pro prostředky_ diagnostické protokoly z Azure Data Factory Flow do tabulek _ADFPipelineRun_, _ADFTriggerRun_a _ADFActivityRun_ .
    * V režimu _diagnostiky Azure_ se protokoly diagnostiky flowují do tabulky _AzureDiagnostics_ .

   ![Pojmenujte nastavení a vyberte pracovní prostor Log-Analytics.](media/data-factory-monitor-oms/monitor-oms-image2.png)

    > [!NOTE]
    > Vzhledem k tomu, že tabulka protokolů Azure nemůže mít více než 500 sloupců, **důrazně doporučujeme** vybrat _režim specifický pro daný prostředek_. Další informace najdete v tématu [Log Analytics známá omezení](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics).

1. Vyberte **Uložit**.

Po chvíli se nové nastavení zobrazí v seznamu nastavení pro tuto datovou továrnu. Diagnostické protokoly se do tohoto pracovního prostoru streamují ihned po vygenerování nových dat událostí. Mezi při vygenerování události a jejím zobrazením v Log Analytics může uplynout až 15 minut.

## <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>Instalace řešení Azure Data Factory Analytics z Azure Marketplace

Toto řešení poskytuje souhrn celkového stavu vašich Data Factory, s možnostmi přechodu k podrobnostem a řešení neočekávaných vzorců chování. Díky bohatě vydaným pohledům můžete získat přehled o zpracování klíčů, včetně:

* Souhrnný přehled kanálu Data Factory, spuštění aktivit a triggerů
* Možnost přejít k podrobnostem o spuštění aktivit Data Factory podle typu
* Souhrn kanálu nejvyšší úrovně Data Factory, chyb aktivit

1. Přejít na **Azure Marketplace**, vyberte **analytický** filtr a vyhledejte **Azure Data Factory Analytics (Preview)** .

   ![Přejděte na "Azure Marketplace", zadejte "analytický filtr" a vyberte "Azure Data Factory Analytics (Preview").](media/data-factory-monitor-oms/monitor-oms-image3.png)

1. Podrobnosti o **Azure Data Factory Analytics (Preview)**

   ![Podrobnosti o Azure Data Factory Analytics (Preview)](media/data-factory-monitor-oms/monitor-oms-image4.png)

1. Vyberte **vytvořit** a pak vytvořte nebo vyberte **pracovní prostor Log Analytics**.

   ![Vytváření nového řešení](media/data-factory-monitor-oms/monitor-log-analytics-image-5.png)

### <a name="monitor-data-factory-metrics"></a>Monitorovat Data Factory metriky

Instalace Azure Data Factory Analytics vytvoří ve vybraném pracovním prostoru Log Analytics výchozí sadu zobrazení v části sešity. Výsledkem je, že se aktivují následující metriky:

* Spuštění ADF – 1) spuštění kanálu pomocí Data Factory
* Spuštění ADF – 2) spuštění aktivit podle datového faktoru
* Spuštění ADF – 3) spuštění triggeru podle datového faktoru
* Chyby ADF – 1) horní 10 chyb kanálu podle Data Factory
* Chyby ADF – 2) prvních 10 spuštění aktivit pomocí Data Factory
* Chyby ADF – 3) hlavních 10 chyb triggerů podle Data Factory
* Statistika ADF – 1) spuštění aktivit podle typu
* Statistika ADF – 2) spuštění triggeru podle typu
* Statistika ADF – 3) maximální doba běhu kanálu

![Okno s "sešity (Preview)" a "AzureDataFactoryAnalytics" se zvýrazní](media/data-factory-monitor-oms/monitor-oms-image6.png)

Můžete vizualizovat předchozí metriky, zobrazit dotazy za těmito metrikami, upravit dotazy, vytvořit upozornění a provést další akce.

![Grafická reprezentace kanálu spouštěná službou Data Factory](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure Data Factory Analytics (Preview) odesílá diagnostické protokoly do cílových tabulek _specifických pro prostředky_ . Můžete zapisovat dotazy z následujících tabulek: _ADFPipelineRun_, _ADFTriggerRun_a _ADFActivityRun_.


## <a name="data-factory-metrics"></a>Data Factory metriky

S monitorováním můžete získat přehled o výkonu a stavu úloh Azure. Nejdůležitější typ dat monitorování je metrika, která se také označuje jako čítač výkonu. Metriky generuje většina prostředků Azure. Monitorování nabízí několik způsobů konfigurace a využívání těchto metrik pro monitorování a řešení potíží.

Azure Data Factory verze 2 vygeneruje následující metriky.

| **Metrika**           | **Zobrazovaný název metriky**         | **Jednotce** | **Typ agregace** | **Popis**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | Úspěšné metriky spuštění kanálu | Počet    | Celkem                | Celkový počet spuštění kanálu, které byly úspěšně dokončeny během minutového okna. |
| PipelineFailedRuns   | Neúspěšná metrika spuštění kanálu    | Počet    | Celkem                | Celkový počet spuštění kanálu, které selhaly během minutového okna.    |
| ActivitySucceededRuns | Úspěšná aktivita spustí metriky | Počet    | Celkem                | Celkový počet spuštění aktivit, které byly úspěšně dokončeny během minutového okna.  |
| ActivityFailedRuns   | Neúspěšná aktivita spustí metriky    | Počet    | Celkem                | Celkový počet spuštění aktivit, které selhaly během minutového okna.     |
| TriggerSucceededRuns | Úspěšná aktivační událost spustí metriky  | Počet    | Celkem                | Celkový počet spuštěných aktivačních událostí, které byly úspěšně dokončeny během minutového okna.   |
| TriggerFailedRuns    | Neúspěšná aktivační událost spustí metriky     | Počet    | Celkem                | Celkový počet spuštění triggerů, které selhaly během minutového okna.      |

Pokud chcete získat přístup k metrikám, postupujte podle pokynů v [Azure monitor datovou platformu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

> [!NOTE]
> Emitují se jenom dokončené události, aktivované aktivity a spuštění kanálu. Probíhající a izolovaný prostor nebo spuštění ladění **nejsou vydávány** . 

## <a name="data-factory-alerts"></a>Výstrahy Data Factory

Přihlaste se k Azure Portal a vyberte **monitorování**  >  **výstrah** a vytvořte upozornění.

![Výstrahy v nabídce portálu](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Vytváření výstrah

1. Vyberte **+ nové pravidlo výstrahy** pro vytvoření nové výstrahy.

    ![Nové pravidlo výstrahy](media/monitor-using-azure-monitor/alerts_image4.png)

1. Definujte podmínku upozornění.

    > [!NOTE]
    > V rozevíracím seznamu **filtrovat podle typu prostředku** nezapomeňte vybrat **vše** .

    !["Definování podmínky upozornění" > "vybrat cíl", který otevře podokno vybrat prostředek ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["Definování podmínky upozornění" > "přidat kritéria", která otevře podokno "konfigurovat logiku signálu"](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Podokno konfigurace typu signálu](media/monitor-using-azure-monitor/alerts_image7.png)

1. Zadejte podrobnosti výstrahy.

    ![Podrobnosti upozornění](media/monitor-using-azure-monitor/alerts_image8.png)

1. Definujte skupinu akcí.

    ![Vytvoření pravidla se zvýrazněnou možností nová skupina akcí](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Vytvoří novou skupinu akcí.](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Konfigurace e-mailu, SMS, nabízených oznámení a hlasu](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Definování skupiny akcí](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="set-up-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Nastavení diagnostických protokolů prostřednictvím Azure Monitor REST API

### <a name="diagnostic-settings"></a>Nastavení diagnostiky

Použijte nastavení diagnostiky ke konfiguraci diagnostických protokolů pro nevýpočetní prostředky. Nastavení pro ovládací prvek prostředků má následující funkce:

* Určují, kde jsou odesílány diagnostické protokoly. Mezi příklady patří účet Azure Storage, centrum událostí Azure nebo monitorování protokolů.
* Určují, které kategorie protokolů se odesílají.
* Určují, jak dlouho by měla být každá kategorie protokolů udržována v účtu úložiště.
* Nastavení doby uchovávání na 0 dní znamená, že se protokoly uchovávají trvale. V opačném případě může být hodnota libovolný počet dní od 1 do 2 147 483 647.
* Pokud jsou nastavené zásady uchovávání informací, ale ukládání protokolů v účtu úložiště je zakázané, zásady uchovávání dat nemají žádný vliv. K tomuto stavu může dojít například v případě, že jsou vybrány pouze možnosti Event Hubs nebo monitorovat protokoly.
* Zásady uchovávání informací se používají za den. Hranice mezi dny probíhá po půlnoci koordinovaného světového času (UTC). Na konci dne se odpouštějí i protokoly ze dní, které jsou mimo zásady uchovávání informací. Například pokud máte zásady uchovávání informací jeden den, na začátku dnešního dne se odpouštějí protokoly od dřív než včera.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Povolení diagnostických protokolů prostřednictvím Azure Monitor REST API

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Vytvoří nebo aktualizuje nastavení diagnostiky v REST API monitorování.

##### <a name="request"></a>Žádost

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Hlavičky

* Nahraďte `{api-version}` za `2016-09-01` (Jak velká může být moje znalostní báze?).
* Nahraďte `{resource-id}` ID prostředku, pro který chcete upravit nastavení diagnostiky. Další informace najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Nastavte `Content-Type` hlavičku na `application/json` .
* Nastavte autorizační hlavičku na webový token JSON, který jste získali z Azure Active Directory (Azure AD). Další informace najdete v tématu [ověřování požadavků](../active-directory/develop/authentication-scenarios.md).

##### <a name="body"></a>Text

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| Vlastnost | Typ | Popis |
| --- | --- | --- |
| **storageAccountId** |Řetězec | ID prostředku účtu úložiště, do kterého chcete odeslat diagnostické protokoly. |
| **serviceBusRuleId** |Řetězec | ID pravidla sběrnice (Service-Bus) pro obor názvů sběrnice, ve kterém chcete mít Event Hubs vytvořené pro diagnostické protokoly pro streamování. ID pravidla má formát `{service bus resource ID}/authorizationrules/{key name}` .|
| **workspaceId** | Komplexní typ | Pole časových období metriky a jejich zásady uchovávání. Hodnota této vlastnosti je prázdná. |
|**metriky**| Hodnoty parametrů běhu kanálu, které se mají předat vyvolanému kanálu| Objekt JSON, který mapuje názvy parametrů na hodnoty argumentu. |
| **protokolování**| Komplexní typ| Název kategorie diagnostického protokolu pro typ prostředku. Chcete-li získat seznam kategorií diagnostického protokolu pro určitý prostředek, proveďte operaci získat diagnostiku – nastavení. |
| **kategorií**| Řetězec| Pole kategorií protokolů a jejich zásady uchovávání. |
| **timeGrain** | Řetězec | Členitost metrik, která je zachycena ve formátu ISO 8601 Duration. Hodnota vlastnosti musí být `PT1M` , což znamená jednu minutu. |
| **umožněn**| Logická hodnota | Určuje, zda je pro tento prostředek povolena kolekce metriky nebo kategorie protokolu. |
| **retentionPolicy**| Komplexní typ| Popisuje zásady uchovávání informací pro kategorii metrik nebo protokolů. Tato vlastnost se používá jenom pro účty úložiště. |
|**denní**| Int| Počet dní, po které se budou metriky nebo protokoly uchovávat Pokud je hodnota vlastnosti 0, protokoly se uchovávají trvale. Tato vlastnost se používá jenom pro účty úložiště. |

##### <a name="response"></a>Odpověď

200 OK.


```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Získat informace o nastavení diagnostiky v REST API monitorování

##### <a name="request"></a>Žádost

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Hlavičky

* Nahraďte `{api-version}` za `2016-09-01` (Jak velká může být moje znalostní báze?).
* Nahraďte `{resource-id}` ID prostředku, pro který chcete upravit nastavení diagnostiky. Další informace najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Nastavte `Content-Type` hlavičku na `application/json` .
* Nastavte autorizační hlavičku na webový token JSON, který jste získali ze služby Azure AD. Další informace najdete v tématu [ověřování požadavků](../active-directory/develop/authentication-scenarios.md).

##### <a name="response"></a>Odpověď

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}

```
Další informace najdete v tématu [nastavení diagnostiky](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

## <a name="schema-of-logs-and-events"></a>Schéma protokolů a událostí

### <a name="monitor-schema"></a>Monitorování schématu

#### <a name="activity-run-log-attributes"></a>Atributy protokolu spuštění aktivit

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Vlastnost | Typ | Popis | Příklad |
| --- | --- | --- | --- |
| **Obsah** |Řetězec | Úroveň diagnostických protokolů. Pro protokoly spuštění aktivit nastavte vlastnost hodnota na 4. | `4` |
| **correlationId** |Řetězec | Jedinečné ID pro sledování konkrétního požadavku. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **interval** | Řetězec | Čas události ve formátu TimeSpan UTC `YYYY-MM-DDTHH:MM:SS.00000Z` . | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| Řetězec| ID spuštění aktivity. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| Řetězec| ID spuštění kanálu | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**Prostředku**| Řetězec | ID přidružené k prostředku datové továrny | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**kategorií**| Řetězec | Kategorie diagnostických protokolů. Nastavte hodnotu vlastnosti na `ActivityRuns` . | `ActivityRuns` |
|**úroveň**| Řetězec | Úroveň diagnostických protokolů. Nastavte hodnotu vlastnosti na `Informational` . | `Informational` |
|**operationName**| Řetězec | Název aktivity se stavem. Pokud je aktivita spouštěcí prezenční signál, hodnota vlastnosti je `MyActivity -` . Pokud je aktivita koncovým prezenčním signálem, hodnota vlastnosti je `MyActivity - Succeeded` . | `MyActivity - Succeeded` |
|**profilace**| Řetězec | Název kanálu. | `MyPipeline` |
|**Název aktivity activityName**| Řetězec | Název aktivity. | `MyActivity` |
|**Čína**| Řetězec | Čas spuštění aktivity běží ve formátu TimeSpan UTC. | `2017-06-26T20:55:29.5007959Z`|
|**účelu**| Řetězec | Čas ukončení aktivity běží ve formátu TimeSpan UTC. Pokud diagnostický protokol ukazuje, že aktivita začala, ale ještě nebyla ukončena, hodnota vlastnosti je `1601-01-01T00:00:00Z` . | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Kanály – atributy protokolu spuštění

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Vlastnost | Typ | Popis | Příklad |
| --- | --- | --- | --- |
| **Obsah** |Řetězec | Úroveň diagnostických protokolů. Pro protokoly spuštění aktivit nastavte vlastnost hodnota na 4. | `4` |
| **correlationId** |Řetězec | Jedinečné ID pro sledování konkrétního požadavku. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **interval** | Řetězec | Čas události ve formátu TimeSpan UTC `YYYY-MM-DDTHH:MM:SS.00000Z` . | `2017-06-28T21:00:27.3534352Z` |
|**runId**| Řetězec| ID spuštění kanálu | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**Prostředku**| Řetězec | ID přidružené k prostředku datové továrny | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**kategorií**| Řetězec | Kategorie diagnostických protokolů. Nastavte hodnotu vlastnosti na `PipelineRuns` . | `PipelineRuns` |
|**úroveň**| Řetězec | Úroveň diagnostických protokolů. Nastavte hodnotu vlastnosti na `Informational` . | `Informational` |
|**operationName**| Řetězec | Název kanálu spolu s jeho stavem. Po dokončení spuštění kanálu je hodnota vlastnosti `Pipeline - Succeeded` . | `MyPipeline - Succeeded`. |
|**profilace**| Řetězec | Název kanálu. | `MyPipeline` |
|**Čína**| Řetězec | Čas spuštění aktivity běží ve formátu TimeSpan UTC. | `2017-06-26T20:55:29.5007959Z`. |
|**účelu**| Řetězec | Čas ukončení aktivity běží ve formátu TimeSpan UTC. Pokud diagnostický protokol zobrazuje aktivitu, která byla spuštěna, ale ještě nebyla ukončena, hodnota vlastnosti je `1601-01-01T00:00:00Z` .  | `2017-06-26T20:55:29.5007959Z` |
|**stav**| Řetězec | Konečný stav spuštění kanálu. Možné hodnoty vlastností jsou `Succeeded` a `Failed` . | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Trigger-spustit atributy protokolu

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}

```

| Vlastnost | Typ | Popis | Příklad |
| --- | --- | --- | --- |
| **Obsah** |Řetězec | Úroveň diagnostických protokolů. Pro protokoly spuštění aktivit nastavte vlastnost hodnota na 4. | `4` |
| **correlationId** |Řetězec | Jedinečné ID pro sledování konkrétního požadavku. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **interval** | Řetězec | Čas události ve formátu TimeSpan UTC `YYYY-MM-DDTHH:MM:SS.00000Z` . | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| Řetězec| ID spuštění triggeru. | `08587023010602533858661257311` |
|**Prostředku**| Řetězec | ID přidružené k prostředku datové továrny | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**kategorií**| Řetězec | Kategorie diagnostických protokolů. Nastavte hodnotu vlastnosti na `PipelineRuns` . | `PipelineRuns` |
|**úroveň**| Řetězec | Úroveň diagnostických protokolů. Nastavte hodnotu vlastnosti na `Informational` . | `Informational` |
|**operationName**| Řetězec | Název triggeru s konečným stavem, který označuje, zda se aktivační událost úspěšně aktivovala. Pokud byl prezenční signál úspěšný, hodnota vlastnosti je `MyTrigger - Succeeded` . | `MyTrigger - Succeeded` |
|**triggerName**| Řetězec | Název triggeru | `MyTrigger` |
|**triggerType**| Řetězec | Typ triggeru Možné hodnoty vlastností jsou `Manual Trigger` a `Schedule Trigger` . | `ScheduleTrigger` |
|**triggerEvent**| Řetězec | Událost triggeru | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**Čína**| Řetězec | Čas spuštění triggeru, který se spouští ve formátu TimeSpan UTC. | `2017-06-26T20:55:29.5007959Z`|
|**stav**| Řetězec | Konečný stav ukazující, zda se aktivační událost úspěšně aktivovala. Možné hodnoty vlastností jsou `Succeeded` a `Failed` . | `Succeeded`|

### <a name="log-analytics-schema"></a>Log Analytics schéma

Log Analytics dědí schéma z monitorování s následujícími výjimkami:

* První písmeno v každém názvu sloupce je velkými písmeny. Například název sloupce "ID korelace" v monitorování je "ID korelace" v Log Analytics.
* Neexistuje žádný sloupec Level.
* Dynamický sloupec Properties (vlastnosti) se zachová jako následující dynamický typ objektu BLOB JSON.

    | Azure Monitor sloupec | Log Analytics sloupec | Typ |
    | --- | --- | --- |
    | $. Properties. UserProperties | UserProperties | Dynamická |
    | $. Properties. Anotac | Poznámky | Dynamická |
    | $. Properties. Vstup | Vstup | Dynamická |
    | $. Properties. Výkonem | Výstup | Dynamická |
    | $. Properties. Chyba. errorCode | ErrorCode | int |
    | $. Properties. Chyba. zpráva | Chybová | řetězec |
    | $. Properties. Chyba | Chyba | Dynamická |
    | $. Properties. Předchůdci | Předchůdci | Dynamická |
    | $. Properties. Ukazatelů | Parametry | Dynamická |
    | $.properties.SystemParameters | Třídy SystemParameters | Dynamická |
    | $. Properties. Značky | Značky | Dynamická |


## <a name="next-steps"></a>Další kroky
[Programové monitorování a Správa kanálů](monitor-programmatically.md)
