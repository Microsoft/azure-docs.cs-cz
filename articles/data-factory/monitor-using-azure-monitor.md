---
title: Monitorování datových továren pomocí Azure Monitor
description: Naučte se používat Azure Monitor k monitorování kanálů Data Factory/Azure povolením diagnostických protokolů s informacemi z Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 09456c06f2a171ec32c1b885dc2b4e475fea5371
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550293"
---
# <a name="monitor-and-alert-data-factory-by-using-azure-monitor"></a>Monitorování a Data Factory výstrah pomocí Azure Monitor

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Cloudové aplikace jsou komplexní a obsahují mnoho pohyblivých částí. Monitory poskytují data, která vám pomůžou zajistit, aby vaše aplikace zůstaly v dobrém stavu. Monitory také umožňují vyhnout se potenciálním problémům a řešit předchozí problémy. Data monitorování můžete využít k získání podrobných informací o vašich aplikacích. Tato znalostní báze vám pomůže zlepšit výkon a udržovatelnost aplikace. Pomáhá také automatizovat akce, které jinak vyžadují ruční zásah.

Azure Monitor poskytuje základní metriky a protokoly infrastruktury na základní úrovni pro většinu služeb Azure. Diagnostické protokoly Azure jsou vydávány prostředkem a poskytují bohatou a častou data o provozu daného prostředku. Azure Data Factory (ADF) může zapisovat diagnostické protokoly v Azure Monitor. Pokud chcete tuto funkci seznámit a předvedení této funkce, podívejte se na následující video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

Další informace najdete v tématu [přehled Azure monitor](../azure-monitor/overview.md).

## <a name="keeping-azure-data-factory-metrics-and-pipeline-run-data"></a>Udržování metrik Azure Data Factory a spuštění dat kanálu

Data Factory ukládá data o běhu kanálů jenom za 45 dní. Použijte Azure Monitor, pokud chcete uchovávat data delší dobu. S monitorováním můžete směrovat diagnostické protokoly pro analýzu na více různých cílů.

* **Účet úložiště**: uložte diagnostické protokoly do účtu úložiště pro auditování nebo ruční kontrolu. Nastavení diagnostiky můžete použít k určení doby uchování ve dnech.
* **Centrum událostí**: Streamujte protokoly do Azure Event Hubs. Protokoly se stanou vstupem do řešení partner Service nebo vlastní analýzy, jako je Power BI.
* **Log Analytics**: Analyzujte protokoly pomocí Log Analytics. Data Factory integrace s Azure Monitor je užitečná v následujících scénářích:
  * Chcete zapisovat komplexní dotazy na bohatou sadu metrik, která je publikována nástrojem Data Factory k monitorování. Můžete vytvářet vlastní výstrahy na těchto dotazech prostřednictvím monitorování.
  * Chcete monitorovat napříč datovými továrnami. Data z několika datových továrn můžete směrovat do jednoho pracovního prostoru monitorování.

Můžete použít také účet úložiště nebo obor názvů centra událostí, který není v předplatném prostředku, který vysílá protokoly. Uživatel, který konfiguruje nastavení, musí mít k oběma předplatným odpovídající přístup na základě role Azure (Azure RBAC).

## <a name="configure-diagnostic-settings-and-workspace"></a>Konfigurovat nastavení diagnostiky a pracovní prostor

Vytvořte nebo přidejte nastavení diagnostiky pro datovou továrnu.

1. Na portálu klikněte na monitorování. Vyberte **Nastavení**  >  **diagnostiky**.

1. Vyberte objekt pro vytváření dat, pro který chcete nastavit nastavení diagnostiky.

1. Pokud ve vybrané datové továrně žádná nastavení neexistují, budete vyzváni k vytvoření nastavení. Vyberte **zapnout diagnostiku**.

   ![Vytvořit nastavení diagnostiky, pokud neexistuje žádné nastavení](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Pokud v datové továrně existují nastavení, zobrazí se seznam nastavení, která jsou už nakonfigurovaná u objektu pro vytváření dat. Vyberte **Přidat nastavení diagnostiky**.

   ![Pokud existují nastavení, přidejte nastavení diagnostiky.](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Zadejte název nastavení, vyberte **Odeslat do Log Analytics** a pak vyberte pracovní prostor z **pracovního prostoru Log Analytics**.

    * V režimu _diagnostiky Azure_ se protokoly diagnostiky flowují do tabulky _AzureDiagnostics_ .

    * V režimu _specifickém pro prostředky_ diagnostické protokoly z Azure Data Factory Flow do následujících tabulek:
      - _ADFActivityRun_
      - _ADFPipelineRun_
      - _ADFTriggerRun_
      - _ADFSSISIntegrationRuntimeLogs_
      - _ADFSSISPackageEventMessageContext_
      - _ADFSSISPackageEventMessages_
      - _ADFSSISPackageExecutableStatistics_
      - _ADFSSISPackageExecutionComponentPhases_
      - _ADFSSISPackageExecutionDataStatistics_

      Můžete vybrat různé protokoly, které jsou relevantní pro vaše úlohy, pro odeslání do Log Analytics tabulek. Pokud například nepoužíváte služba SSIS (SQL Server Integration Services) (SSIS) vůbec, nemusíte vybírat žádné protokoly SSIS. Pokud chcete protokolovat SSIS Integration Runtime (IR) operace spuštění/zastavení/údržby, můžete vybrat SSIS INFRAČERVENé protokoly. Pokud vyvoláte SSIS spuštění balíčků prostřednictvím T-SQL v SQL Server Management Studio (SSMS), SQL Server agenta nebo jiných určených nástrojů, můžete vybrat protokoly balíčku SSIS. Pokud vyvoláte spouštění balíčků SSIS prostřednictvím aktivit balíčku Execute SSIS v kanálech ADF, můžete vybrat všechny protokoly.

    * Vyberete-li možnost _AllMetrics_, budou k dispozici různé metriky ADF, které vám umožní monitorovat nebo vyvolávat výstrahy, včetně metrik pro aktivity ADF, kanál a triggery spuštění a také pro operace SSIS IR a spuštění balíčku SSIS.

   ![Pojmenujte nastavení a vyberte pracovní prostor Log-Analytics.](media/data-factory-monitor-oms/monitor-oms-image2.png)

    > [!NOTE]
    > Vzhledem k tomu, že tabulka protokolů Azure nemůže mít více než 500 sloupců, **důrazně doporučujeme** vybrat _režim specifický pro daný prostředek_. Další informace najdete v tématu [referenční informace o protokolech AzureDiagnostics](/azure-monitor/reference/tables/azurediagnostics#additionalfields-column).

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

Při instalaci tohoto řešení se vytvoří výchozí sada zobrazení v části sešity vybraného Log Analytics pracovního prostoru. V důsledku toho se aktivují následující metriky:

* Spuštění ADF – 1) spuštění kanálu pomocí Data Factory
* Spuštění ADF – 2) spuštění aktivit pomocí Data Factory
* Spuštění ADF – 3) spuštění triggeru pomocí Data Factory
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
> Azure Data Factory Analytics (Preview) odesílá diagnostické protokoly do cílových tabulek _specifických pro prostředky_ . Můžete zapisovat dotazy z následujících tabulek: _ADFPipelineRun_, _ADFTriggerRun_ a _ADFActivityRun_.

## <a name="data-factory-metrics"></a>Data Factory metriky

S monitorováním můžete získat přehled o výkonu a stavu úloh Azure. Nejdůležitější typ dat monitorování je metrika, která se také označuje jako čítač výkonu. Metriky generuje většina prostředků Azure. Monitorování nabízí několik způsobů konfigurace a využívání těchto metrik pro monitorování a řešení potíží.

Tady jsou některé metriky vydávané Azure Data Factory verze 2:

| **Metrika**                           | **Zobrazovaný název metriky**                  | **Jednotka** | **Typ agregace** | **Popis**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| ActivityCancelledRuns                 | Zrušené metriky spuštění aktivit           | Počet    | Celkem                | Celkový počet spuštění aktivit, které byly zrušeny během minutového okna. |
| ActivityFailedRuns                   | Neúspěšná aktivita spustí metriky             | Počet    | Celkem                | Celkový počet spuštění aktivit, které selhaly během minutového okna. |
| ActivitySucceededRuns                | Úspěšná aktivita spustí metriky          | Počet    | Celkem                | Celkový počet spuštění aktivit, které byly úspěšně dokončeny během minutového okna. |
| PipelineCancelledRuns                 | Zrušené metriky spuštění kanálu           | Počet    | Celkem                | Celkový počet spuštění kanálu, které byly zrušeny během minutového okna. |
| PipelineFailedRuns                   | Neúspěšná metrika spuštění kanálu             | Počet    | Celkem                | Celkový počet spuštění kanálu, které selhaly během minutového okna. |
| PipelineSucceededRuns                | Úspěšné metriky spuštění kanálu          | Počet    | Celkem                | Celkový počet spuštění kanálu, které byly úspěšně dokončeny během minutového okna. |
| TriggerCancelledRuns                  | Zrušené aktivační události spustí metriky            | Počet    | Celkem                | Celkový počet spuštění triggerů, které byly zrušeny během minutového okna. |
| TriggerFailedRuns                    | Neúspěšná aktivační událost spustí metriky              | Počet    | Celkem                | Celkový počet spuštění triggerů, které selhaly během minutového okna. |
| TriggerSucceededRuns                 | Úspěšná aktivační událost spustí metriky           | Počet    | Celkem                | Celkový počet spuštěných aktivačních událostí, které byly úspěšně dokončeny během minutového okna. |
| SSISIntegrationRuntimeStartCancelled  | Zrušené metriky spuštění SSIS Integration runtime           | Počet    | Celkem                | Celkový počet spuštění SSIS Integration runtime, které byly zrušeny během minutového okna. |
| SSISIntegrationRuntimeStartFailed    | Nepovedlo se spustit metriky spuštění SSIS Integration runtime             | Počet    | Celkem                | Celkový počet SSIS Integration runtime se spustí v minutovém okně. |
| SSISIntegrationRuntimeStartSucceeded | Úspěšná metriky spuštění SSIS Integration runtime          | Počet    | Celkem                | Celkový počet SSIS Integration runtime se spustí v minutovém okně. |
| SSISIntegrationRuntimeStopStuck      | Zastavení metriky SSIS Integration runtime               | Počet    | Celkem                | Celkový počet SSIS Integration runtime se zastaví v minutovém okně. |
| SSISIntegrationRuntimeStopSucceeded  | Úspěšné SSIS Integration runtime – metriky zastavení           | Počet    | Celkem                | Celkový počet SSIS Integration runtime se zastaví v průběhu minutového okna. |
| SSISPackageExecutionCancelled         | Zrušené metriky spuštění balíčku SSIS  | Počet    | Celkem                | Celkový počet spuštěných SSIS balíčků, které byly zrušeny během minutového okna. |
| SSISPackageExecutionFailed           | Neúspěšné metriky spuštění balíčku SSIS    | Počet    | Celkem                | Celkový počet spuštěných SSIS balíčků, které selhaly během minutového okna. |
| SSISPackageExecutionSucceeded        | Úspěšné metriky spuštění balíčku SSIS | Počet    | Celkem                | Celkový počet spuštěných SSIS balíčků, které byly úspěšně dokončeny během minutového okna. |

Pokud chcete získat přístup k metrikám, postupujte podle pokynů v [Azure monitor datovou platformu](../azure-monitor/data-platform.md).

> [!NOTE]
> Emitují se jenom události z dokončených, aktivované aktivity a spuštění kanálu. Probíhající a ladicí běhy **nejsou generovány** . Na druhé straně se generují události ze **všech** spuštění balíčků SSIS, včetně těch, které jsou dokončené a probíhající, bez ohledu na jejich metody vyvolání. Můžete například vyvolat spouštění balíčků na Azure-Enabled SQL Server Data Tools (SSDT), pomocí T-SQL v SSMS, agenta SQL Server nebo jiných určených nástrojů a jako aktivované nebo ladění spuštění aktivit balíčku SSIS v kanálech ADF.

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

    ![Podrobnosti výstrahy](media/monitor-using-azure-monitor/alerts_image8.png)

1. Definujte skupinu akcí.

    ![Vytvoření pravidla se zvýrazněnou možností nová skupina akcí](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Vytvoří novou skupinu akcí.](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Konfigurace e-mailu, SMS, nabízených oznámení a hlasu](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Definice skupiny akcí](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="set-up-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Nastavení diagnostických protokolů prostřednictvím Azure Monitor REST API

### <a name="diagnostic-settings"></a>Nastavení diagnostiky

Použijte nastavení diagnostiky ke konfiguraci diagnostických protokolů pro prostředky, které nejsou výpočetními prostředky. Nastavení pro ovládací prvek prostředků má následující funkce:

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
* Nastavte autorizační hlavičku na webový token JSON, který jste získali z Azure Active Directory (Azure AD). Další informace najdete v tématu [ověřování požadavků](../active-directory/develop/authentication-vs-authorization.md).

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

| Vlastnost | Typ | Description |
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
* Nastavte autorizační hlavičku na webový token JSON, který jste získali ze služby Azure AD. Další informace najdete v tématu [ověřování požadavků](../active-directory/develop/authentication-vs-authorization.md).

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
Další informace najdete v tématu [nastavení diagnostiky](/rest/api/monitor/diagnosticsettings).

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
| **ID** |Řetězec | Jedinečné ID pro sledování konkrétního požadavku. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
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
| **ID** |Řetězec | Jedinečné ID pro sledování konkrétního požadavku. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
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
| **ID** |Řetězec | Jedinečné ID pro sledování konkrétního požadavku. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
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

#### <a name="ssis-integration-runtime-log-attributes"></a>Atributy protokolu SSIS Integration runtime

Tady jsou atributy protokolu SSIS a operace spuštění/zastavení/údržby IR.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "resultType": "",
   "properties": {
      "message": ""
   },
   "resourceId": ""
}
```

| Vlastnost                   | Typ   | Popis                                                   | Příklad                        |
| -------------------------- | ------ | ------------------------------------------------------------- | ------------------------------ |
| **interval**                   | Řetězec | Čas události ve formátu UTC: `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Řetězec | Název vaší operace SSIS IR                            | `Start/Stop/Maintenance` |
| **kategorií**               | Řetězec | Kategorie diagnostických protokolů                               | `SSISIntegrationRuntimeLogs` |
| **ID**          | Řetězec | Jedinečné ID pro sledování konkrétní operace             | `f13b159b-515f-4885-9dfa-a664e949f785Deprovision0059035558` |
| **dataFactoryName**        | Řetězec | Název vašeho ADF                                          | `MyADFv2` |
| **integrationRuntimeName** | Řetězec | Název SSIS IR                                      | `MySSISIR` |
| **úroveň**                  | Řetězec | Úroveň diagnostických protokolů                                  | `Informational` |
| **Hodnotu**             | Řetězec | Výsledek operace SSIS IR                          | `Started/InProgress/Succeeded/Failed` |
| **zpráva**                | Řetězec | Výstupní zpráva vaší operace SSIS IR                  | `The stopping of your SSIS integration runtime has succeeded.` |
| **Prostředku**             | Řetězec | Jedinečné ID prostředku ADF                            | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-message-context-log-attributes"></a>Atributy protokolu kontextu zprávy události SSIS

Tady jsou atributy protokolu podmínek souvisejících se zprávami o událostech, které jsou vygenerované SSIS spouštěními balíčků na SSIS IR. Poskytují podobné informace jako [tabulka kontextu zprávy události SSIS Catalog (SSISDB) nebo zobrazení](/sql/integration-services/system-views/catalog-event-message-context) , které zobrazuje hodnoty za běhu mnoha vlastností balíčku SSIS. Vygenerují se, když vyberete `Basic/Verbose` úroveň protokolování a užitečnost pro ladění a kontrolu kompatibility.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "contextDepth": "",
      "packagePath": "",
      "contextType": "",
      "contextSourceName": "",
      "contextSourceId": "",
      "propertyName": "",
      "propertyValue": ""
   },
   "resourceId": ""
}
```

| Vlastnost                   | Typ   | Popis                                                          | Příklad                        |
| -------------------------- | ------ | -------------------------------------------------------------------- | ------------------------------ |
| **interval**                   | Řetězec | Čas události ve formátu UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`        | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Řetězec | Tato nastavení se nastaví na `YourSSISIRName-SSISPackageEventMessageContext`       | `mysqlmissisir-SSISPackageEventMessageContext` |
| **kategorií**               | Řetězec | Kategorie diagnostických protokolů                                      | `SSISPackageEventMessageContext` |
| **ID**          | Řetězec | Jedinečné ID pro sledování konkrétní operace                    | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | Řetězec | Název vašeho ADF                                                 | `MyADFv2` |
| **integrationRuntimeName** | Řetězec | Název SSIS IR                                             | `MySSISIR` |
| **úroveň**                  | Řetězec | Úroveň diagnostických protokolů                                         | `Informational` |
| **operationId**            | Řetězec | Jedinečné ID pro sledování konkrétní operace v SSISDB          | `1` (1 znamená operace týkající se balíčků, které **nejsou** uložené v SSISDB nebo vyvolané prostřednictvím T-SQL) |
| **contextDepth**           | Řetězec | Hloubka kontextu zprávy události                              | `0` (0 znamená kontext před spuštěním spuštění balíčku, 1 znamená kontext při výskytu chyby a zvyšuje se, jak je kontext dále z chyby) |
| **packagePath**            | Řetězec | Cesta k objektu balíčku jako zdroj kontextu zprávy události      | `\Package` |
| **contextType**            | Řetězec | Typ objektu balíčku jako zdroj kontextu zprávy události      | `60`(Další informace naleznete v tématu [Další typy kontextu](/sql/integration-services/system-views/catalog-event-message-context#remarks)) |
| **contextSourceName**      | Řetězec | Název objektu balíčku jako zdroj kontextu zprávy události      | `MyPackage` |
| **contextSourceId**        | Řetězec | Jedinečné ID objektu balíčku jako zdroj kontextu zprávy události | `{E2CF27FB-EA48-41E9-AF6F-3FE938B4ADE1}` |
| **propertyName**           | Řetězec | Název vlastnosti balíčku pro zdroj kontextu zprávy události   | `DelayValidation` |
| **Objekt**          | Řetězec | Hodnota vlastnosti balíčku pro zdroj kontextu zprávy události  | `False` |
| **Prostředku**             | Řetězec | Jedinečné ID prostředku ADF                                   | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-messages-log-attributes"></a>Atributy protokolu zpráv událostí SSIS

Tady jsou atributy protokolu událostí, které jsou vygenerované SSIS spouštěními balíčků na SSIS IR. Poskytují podobné informace jako [tabulka nebo zobrazení zpráv událostí SSISDB](/sql/integration-services/system-views/catalog-event-messages) , které zobrazují podrobný text nebo metadata zpráv událostí. Jsou vygenerovány na jakékoli úrovni protokolování s výjimkou `None` .

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "messageTime": "",
      "messageType": "",
      "messageSourceType": "",
      "message": "",
      "packageName": "",
      "eventName": "",
      "messageSourceName": "",
      "messageSourceId": "",
      "subcomponentName": "",
      "packagePath": "",
      "executionPath": "",
      "threadId": ""
   }
}
```

| Vlastnost                   | Typ   | Popis                                                        | Příklad                        |
| -------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **interval**                   | Řetězec | Čas události ve formátu UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Řetězec | Tato nastavení se nastaví na `YourSSISIRName-SSISPackageEventMessages`           | `mysqlmissisir-SSISPackageEventMessages` |
| **kategorií**               | Řetězec | Kategorie diagnostických protokolů                                    | `SSISPackageEventMessages` |
| **ID**          | Řetězec | Jedinečné ID pro sledování konkrétní operace                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | Řetězec | Název vašeho ADF                                               | `MyADFv2` |
| **integrationRuntimeName** | Řetězec | Název SSIS IR                                           | `MySSISIR` |
| **úroveň**                  | Řetězec | Úroveň diagnostických protokolů                                       | `Informational` |
| **operationId**            | Řetězec | Jedinečné ID pro sledování konkrétní operace v SSISDB        | `1` (1 znamená operace týkající se balíčků, které **nejsou** uložené v SSISDB nebo vyvolané prostřednictvím T-SQL) |
| **messageTime**            | Řetězec | Čas, kdy se zpráva události vytvoří ve formátu UTC          | `2017-06-28T21:00:27.3534352Z` |
| **messageType**            | Řetězec | Typ zprávy události                                     | `70`(Další informace najdete v tématu [Další typy zpráv](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks)) |
| **messageSourceType**      | Řetězec | Typ zdroje zprávy události                              | `20`(Další informace naleznete v tématu [Další typy zdrojů zpráv](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks)) |
| **zpráva**                | Řetězec | Text zprávy události                                     | `MyPackage:Validation has started.` |
| **soubor s balíčkem**            | Řetězec | Název provedeného souboru balíčku                             | `MyPackage.dtsx` |
| **eventName**              | Řetězec | Název související události run-time                                 | `OnPreValidate` |
| **messageSourceName**      | Řetězec | Název součásti balíčku jako zdroj zprávy události         | `Data Flow Task` |
| **messageSourceId**        | Řetězec | Jedinečné ID součásti balíčku jako zdroj zprávy události    | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |
| **subcomponent**       | Řetězec | Název součásti toku dat jako zdroj zprávy události       | `SSIS.Pipeline` |
| **packagePath**            | Řetězec | Cesta k objektu balíčku jako zdroj zprávy události            | `\Package\Data Flow Task` |
| **executionPath**          | Řetězec | Úplná cesta z nadřazeného balíčku pro spuštěnou součást            | `\Transformation\Data Flow Task` (Tato cesta také zachycuje iterace komponenty) |
| **IDvlákna**               | Řetězec | Jedinečné ID vlákna, které bylo provedeno při zaznamenání zprávy o události | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |

#### <a name="ssis-executable-statistics-log-attributes"></a>Atributy protokolu statistiky spustitelných souborů SSIS

Tady jsou atributy protokolu spustitelných statistik generovaných spouštěním balíčků SSIS na SSIS IR, kde jsou spustitelné soubory kontejnery nebo úkoly v toku řízení balíčků. Poskytují podobné informace jako [SSISDBou tabulku statistik nebo zobrazení](/sql/integration-services/system-views/catalog-executable-statistics) , které zobrazují řádek pro každý spuštěný spustitelný soubor, včetně jeho iterací. Vygenerují se na úrovni protokolování s výjimkou `None` a užitečnou pro identifikaci kritických bodů a selhání na úrovni úlohy.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "executionPath": "",
      "startTime": "",
      "endTime": "",
      "executionDuration": "",
      "executionResult": "",
      "executionValue": ""
   },
   "resourceId": ""
}
```

| Vlastnost                   | Typ   | Popis                                                      | Příklad                        |
| -------------------------- | ------ | ---------------------------------------------------------------- | ------------------------------ |
| **interval**                   | Řetězec | Čas události ve formátu UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`    | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Řetězec | Tato nastavení se nastaví na `YourSSISIRName-SSISPackageExecutableStatistics`  | `mysqlmissisir-SSISPackageExecutableStatistics` |
| **kategorií**               | Řetězec | Kategorie diagnostických protokolů                                  | `SSISPackageExecutableStatistics` |
| **ID**          | Řetězec | Jedinečné ID pro sledování konkrétní operace                | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | Řetězec | Název vašeho ADF                                             | `MyADFv2` |
| **integrationRuntimeName** | Řetězec | Název SSIS IR                                         | `MySSISIR` |
| **úroveň**                  | Řetězec | Úroveň diagnostických protokolů                                     | `Informational` |
| **executionId**            | Řetězec | Jedinečné ID pro sledování konkrétního spuštění v SSISDB      | `1` (1 znamená provádění související s balíčky, které **nejsou** uložené v SSISDB/vyvolané prostřednictvím T-SQL) |
| **executionPath**          | Řetězec | Úplná cesta z nadřazeného balíčku pro spuštěnou součást          | `\Transformation\Data Flow Task` (Tato cesta také zachycuje iterace komponenty) |
| **Spuštění**              | Řetězec | Čas, kdy spustitelný soubor vstoupí do fáze předběžného spuštění ve formátu UTC  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | Řetězec | Čas, kdy spustitelný soubor vstoupí do fáze po spuštění ve formátu UTC | `2017-06-28T21:00:27.3534352Z` |
| **executionDuration**      | Řetězec | Doba běhu spustitelného souboru v milisekundách                   | `1,125` |
| **executionResult**        | Řetězec | Výsledek spuštění spustitelného souboru                                 | `0` (0 znamená úspěch, 1 znamená selhání, 2 znamená dokončení a 3 znamená zrušení) |
| **executionValue**         | Řetězec | Uživatelem definovaná hodnota vrácená spuštěním spustitelného souboru            | `1` |
| **Prostředku**             | Řetězec | Jedinečné ID prostředku ADF                               | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-component-phases-log-attributes"></a>Atributy protokolu fází provádění SSIS

Tady jsou atributy protokolu běhových statistik pro součásti toku dat, které jsou generovány SSIS spouštěními balíčků na SSIS IR. Poskytují podobné informace jako [tabulka nebo zobrazení fáze komponenty SSISDB](/sql/integration-services/system-views/catalog-execution-component-phases) , která zobrazuje čas strávený součástmi toku dat ve všech fázích spuštění. Vygenerují se, když vyberete `Performance/Verbose` úroveň protokolování a užitečnou pro zachytávání statistik spuštění toku dat.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "subcomponentName": "",
      "phase": "",
      "startTime": "",
      "endTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Vlastnost                   | Typ   | Popis                                                         | Příklad                        |
| -------------------------- | ------ | ------------------------------------------------------------------- | ------------------------------ |
| **interval**                   | Řetězec | Čas události ve formátu UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`       | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Řetězec | Tato nastavení se nastaví na `YourSSISIRName-SSISPackageExecutionComponentPhases` | `mysqlmissisir-SSISPackageExecutionComponentPhases` |
| **kategorií**               | Řetězec | Kategorie diagnostických protokolů                                     | `SSISPackageExecutionComponentPhases` |
| **ID**          | Řetězec | Jedinečné ID pro sledování konkrétní operace                   | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | Řetězec | Název vašeho ADF                                                | `MyADFv2` |
| **integrationRuntimeName** | Řetězec | Název SSIS IR                                            | `MySSISIR` |
| **úroveň**                  | Řetězec | Úroveň diagnostických protokolů                                        | `Informational` |
| **executionId**            | Řetězec | Jedinečné ID pro sledování konkrétního spuštění v SSISDB         | `1` (1 znamená provádění související s balíčky, které **nejsou** uložené v SSISDB/vyvolané prostřednictvím T-SQL) |
| **soubor s balíčkem**            | Řetězec | Název provedeného souboru balíčku                              | `MyPackage.dtsx` |
| **/TN**               | Řetězec | Název spouštěné úlohy toku dat                                 | `Data Flow Task` |
| **subcomponent**       | Řetězec | Název součásti toku dat                                     | `Derived Column` |
| **fázové**                  | Řetězec | Název fáze provádění                                         | `AcquireConnections` |
| **Spuštění**              | Řetězec | Čas spuštění fáze spuštění ve formátu UTC                  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | Řetězec | Čas ukončení fáze spuštění ve formátu UTC                    | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**          | Řetězec | Cesta spuštění pro úlohu toku dat                            | `\Transformation\Data Flow Task` |
| **Prostředku**             | Řetězec | Jedinečné ID prostředku ADF                                  | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-data-statistics-log-attributes"></a>Atributy protokolu statistiky dat spuštění SSIS

Tady jsou atributy protokolu pohybů dat prostřednictvím každé nohy kanálů toku dat, od nadřazeného až po komponenty, které jsou vygenerované SSIS spouštěními balíčků na SSIS IR. Poskytují podobné informace jako [tabulka statistiky SSISDB provádění dat nebo zobrazení](/sql/integration-services/system-views/catalog-execution-data-statistics) , která zobrazuje počty řádků dat přesunutých prostřednictvím úkolů toku dat. Vygenerují se, když vyberete `Verbose` úroveň protokolování a užitečnou pro výpočet propustnosti toku dat.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "dataflowPathIdString": "",
      "dataflowPathName": "",
      "sourceComponentName": "",
      "destinationComponentName": "",
      "rowsSent": "",
      "createdTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Vlastnost                     | Typ   | Popis                                                        | Příklad                        |
| ---------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **interval**                     | Řetězec | Čas události ve formátu UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**            | Řetězec | Tato nastavení se nastaví na `YourSSISIRName-SSISPackageExecutionDataStatistics` | `mysqlmissisir-SSISPackageExecutionDataStatistics` |
| **kategorií**                 | Řetězec | Kategorie diagnostických protokolů                                    | `SSISPackageExecutionDataStatistics` |
| **ID**            | Řetězec | Jedinečné ID pro sledování konkrétní operace                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**          | Řetězec | Název vašeho ADF                                               | `MyADFv2` |
| **integrationRuntimeName**   | Řetězec | Název SSIS IR                                           | `MySSISIR` |
| **úroveň**                    | Řetězec | Úroveň diagnostických protokolů                                       | `Informational` |
| **executionId**              | Řetězec | Jedinečné ID pro sledování konkrétního spuštění v SSISDB        | `1` (1 znamená provádění související s balíčky, které **nejsou** uložené v SSISDB/vyvolané prostřednictvím T-SQL) |
| **soubor s balíčkem**              | Řetězec | Název provedeného souboru balíčku                             | `MyPackage.dtsx` |
| **/TN**                 | Řetězec | Název spouštěné úlohy toku dat                                | `Data Flow Task` |
| **dataflowPathIdString**     | Řetězec | Jedinečné ID pro cestu toku dat sledování                          | `Paths[SQLDB Table3.ADO NET Source Output]` |
| **dataflowPathName**         | Řetězec | Název cesty toku dat                                         | `ADO NET Source Output` |
| **sourceComponentName**      | Řetězec | Název součásti toku dat, která odesílá data                    | `SQLDB Table3` |
| **destinationComponentName** | Řetězec | Název součásti toku dat, která přijímá data                 | `Derived Column` |
| **rowsSent**                 | Řetězec | Počet řádků odeslaných součástí zdroje                        | `500` |
| **createdTime**              | Řetězec | Čas, kdy se hodnoty v řádcích získávají ve formátu UTC                | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**            | Řetězec | Cesta spuštění pro úlohu toku dat                           | `\Transformation\Data Flow Task` |
| **Prostředku**               | Řetězec | Jedinečné ID prostředku ADF                                 | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

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

## <a name="monitor-ssis-operations-with-azure-monitor"></a>Monitorování operací SSIS pomocí Azure Monitor

Pokud chcete nazvednutím & posunout úlohy SSIS, můžete [ZŘÍDIT SSIS IR v ADF](./tutorial-deploy-ssis-packages-azure.md) , který podporuje:

- Spouštění balíčků nasazených do katalogu SSIS (SSISDB) Azure SQL Database hostovaných serverem/spravovanou instancí (model nasazení projektu)
- Spouštění balíčků nasazených do systému souborů, souborů Azure nebo databáze služby SQL Server Database (MSDB) hostované službou Azure SQL Managed instance (model nasazení balíčku)

Po zřízení můžete [ověřit provozní stav SSIS IR pomocí Azure PowerShell nebo na rozbočovači **monitorování** na portálu ADF](./monitor-integration-runtime.md#azure-ssis-integration-runtime). Pomocí modelu nasazení projektu jsou protokoly spouštění balíčků SSIS uloženy v interních tabulkách nebo zobrazeních SSISDB, takže je můžete dotazovat, analyzovat a vizuálně prezentovat pomocí určených nástrojů jako SSMS. Pomocí modelu nasazení balíčku můžete protokoly spuštění balíčků SSIS ukládat do systému souborů nebo souborů Azure jako soubory CSV, které ještě potřebujete k analýze a zpracování pomocí jiných určených nástrojů, než je budete moct dotazovat, analyzovat a vizuálně prezentovat.

Nyní s [Azure monitor](../azure-monitor/data-platform.md) integrací můžete zadávat dotazy, analyzovat a vizuálně prezentovat všechny metriky a protokoly vygenerované z operací SSIS IR a spouštění balíčků SSIS na Azure Portal. Kromě toho můžete také vyvolávat výstrahy.

### <a name="configure-diagnostic-settings-and-workspace-for-ssis-operations"></a>Konfigurace nastavení diagnostiky a pracovního prostoru pro operace SSIS

Pro odesílání všech metrik a protokolů vygenerovaných z SSISch operací IR a spuštění balíčků SSIS pro Azure Monitor musíte pro [svůj ADF nakonfigurovat nastavení diagnostiky a pracovní prostor](#configure-diagnostic-settings-and-workspace).

### <a name="ssis-operational-metrics"></a>Provozní metriky SSIS

Provozní [metriky](../azure-monitor/essentials/data-platform-metrics.md) SSIS jsou čítače výkonu nebo číselné hodnoty, které popisují stav operací spuštění a zastavení infračerveného přenosu dat SSIS a také provádění balíčků SSIS v určitém bodě v čase. Jsou součástí [metriky ADF v Azure monitor](#data-factory-metrics).

Když konfigurujete nastavení diagnostiky a pracovní prostor pro ADF na Azure Monitor, zaškrtnutím políčka _AllMetrics_ zpřístupníte SSIS provozní metriky pro [interaktivní analýzu pomocí Azure Průzkumník metrik](../azure-monitor/essentials/metrics-getting-started.md), [prezentace na řídicím panelu Azure](../azure-monitor/app/tutorial-app-dashboards.md)a [Upozornění téměř v reálném čase](../azure-monitor/alerts/alerts-metric.md).

![Pojmenujte nastavení a vyberte pracovní prostor Log-Analytics.](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="ssis-operational-alerts"></a>SSIS provozní výstrahy

Pokud chcete vyvolat upozornění na provozní metriky SSIS z portálu ADF, [Vyberte stránku **výstrahy & metriky** v centru **monitorování** ADF a postupujte podle podrobných pokynů](./monitor-visually.md#alerts).

![Vyvolává se SSIS provozní výstrahy z portálu ADF.](media/data-factory-monitor-oms/data-factory-monitor-alerts-ssis.png)

Pokud chcete vygenerovat upozornění na provozní metriky SSIS z Azure Portal, [Vyberte stránku **výstrahy** centra **monitorování** Azure a postupujte](#data-factory-alerts)podle podrobných pokynů.

![Vyvolává se SSIS provozní výstrahy z Azure Portal](media/data-factory-monitor-oms/azure-monitor-alerts-ssis.png)

### <a name="ssis-operational-logs"></a>Provozní protokoly SSIS

Provozní [protokoly](../azure-monitor/logs/data-platform-logs.md) SSIS jsou události generované SSISmi infračervenými operacemi a prováděním balíčků SSIS, které poskytují dostatek kontextu u všech zjištěných problémů a jsou užitečné pro analýzu původní příčiny. 

Když konfigurujete nastavení diagnostiky a pracovní prostor pro ADF v Azure Monitor, můžete vybrat relevantní provozní protokoly SSIS a odeslat je Log Analytics založené na Azure Průzkumník dat. V takovém případě budou k dispozici pro [analýzu pomocí bohatých dotazovacích jazyků](../azure-monitor/logs/log-query-overview.md), [prezentace na řídicím panelu Azure](../azure-monitor/app/tutorial-app-dashboards.md)a [Upozornění téměř v reálném čase](../azure-monitor/alerts/alerts-log.md).

![Pojmenujte nastavení a vyberte pracovní prostor Log-Analytics.](media/data-factory-monitor-oms/monitor-oms-image2.png)

Schémata a obsah protokolů spouštění balíčků SSIS v Azure Monitor a Log Analytics jsou podobné schématům interních tabulek nebo zobrazení SSISDB.

| Kategorie protokolu Azure Monitor          | Log Analytics tabulky                     | SSISDB interní tabulky/zobrazení              |
| ------------------------------------- | ---------------------------------------- | ----------------------------------------- |
| `SSISIntegrationRuntimeLogs`          | `ADFSSISIntegrationRuntimeLogs`          |                                           |
| `SSISPackageEventMessageContext`      | `ADFSSISPackageEventMessageContext`      | `[internal].[event_message_context]`      |
| `SSISPackageEventMessages`            | `ADFSSISPackageEventMessages`            | `[internal].[event_messages]`             |
| `SSISPackageExecutableStatistics`     | `ADFSSISPackageExecutableStatistics`     | `[internal].[executable_statistics]`      |
| `SSISPackageExecutionComponentPhases` | `ADFSSISPackageExecutionComponentPhases` | `[internal].[execution_component_phases]` |
| `SSISPackageExecutionDataStatistics`  | `ADFSSISPackageExecutionDataStatistics`  | `[internal].[execution_data_statistics]`  |

Další informace o atributech a vlastnostech operačního protokolu SSIS najdete v tématu [Azure monitor a Log Analytics schémat pro ADF](#schema-of-logs-and-events).

Vybrané protokoly spuštění balíčků SSIS se vždycky odesílají do Log Analytics bez ohledu na jejich metody vyvolání. Můžete například vyvolat spouštění balíčků v SSDT s povoleným Azure, prostřednictvím T-SQL na SSMS, SQL Server agenta nebo jiných určených nástrojů a jako aktivované nebo ladění spuštění aktivit balíčku SSIS v kanálech ADF.

Při dotazování protokolu SSIS IR na Log Analytics můžete použít vlastnosti **OperationName** a **ResultType** , které jsou nastaveny na `Start/Stop/Maintenance` a v `Started/InProgress/Succeeded/Failed` uvedeném pořadí. 

![Dotazování protokolů operací IR SSIS na Log Analytics](media/data-factory-monitor-oms/log-analytics-query.png)

Při dotazování protokolů spouštění balíčků SSIS na Log Analytics je můžete spojit s použitím  /  / vlastností **ID korelace** OperationId ExecutionID. **OperationId** / **ExecutionID** se vždycky nastaví na `1` pro všechny operace nebo provádění související s balíčky, které **nejsou** uložené v SSISDB nebo vyvolané prostřednictvím T-SQL.

![Dotazování na Log Analytics protokoly spouštění balíčků SSIS](media/data-factory-monitor-oms/log-analytics-query2.png)

## <a name="next-steps"></a>Další kroky
[Programové monitorování a Správa kanálů](monitor-programmatically.md)
