---
title: Export dat pracovního prostoru Log Analytics v Azure Monitor (Preview)
description: Log Analytics data export umožňuje průběžně exportovat data vybraných tabulek z pracovního prostoru Log Analytics do účtu služby Azure Storage nebo do Azure Event Hubs v průběhu shromažďování.
ms.subservice: logs
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 10/14/2020
ms.openlocfilehash: 54d5fdf1f6bc905482186475302901c46de0d285
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380122"
---
# <a name="log-analytics-workspace-data-export-in-azure-monitor-preview"></a>Export dat pracovního prostoru Log Analytics v Azure Monitor (Preview)
Export dat v pracovním prostoru Log Analytics v Azure Monitor umožňuje průběžně exportovat data z vybraných tabulek v pracovním prostoru Log Analytics do účtu služby Azure Storage nebo Event Hubs Azure jako shromážděná. Tento článek poskytuje podrobné informace o této funkci a postupu konfigurace exportu dat ve vašich pracovních prostorech.

## <a name="overview"></a>Přehled
Po nakonfigurování exportu dat pro váš pracovní prostor Log Analytics se všechna nová data odesílaná do vybraných tabulek v pracovním prostoru automaticky exportují do svého účtu úložiště v reálném čase, nebo do centra událostí.

![Přehled exportu dat](media/logs-data-export/data-export-overview.png)

Všechna data ze zahrnutých tabulek se exportují bez filtru. Když například nakonfigurujete pravidlo exportu dat pro tabulku *SecurityEvent* , všechna data odesílaná do tabulky *SecurityEvent* se exportují počínaje z času konfigurace.


## <a name="other-export-options"></a>Další možnosti exportu
Export dat Log Analytics pracovního prostoru průběžně exportuje data z pracovního prostoru Log Analytics. K dalším možnostem exportu dat pro konkrétní scénáře patří následující:

- Plánovaný export z dotazu protokolu pomocí aplikace logiky To se podobá funkci exportu dat, ale umožňuje odeslat filtrovaná nebo agregovaná data do služby Azure Storage. Tato metoda je sice v souladu s [omezeními dotazů protokolu](../service-limits.md#log-analytics-workspaces)  , viz [data archivu z pracovního prostoru Log Analytics do úložiště Azure pomocí aplikace logiky](logs-export-logic-app.md).
- Jednorázové export pomocí aplikace logiky [Logic Apps a automatické automatizaci najdete v tématu konektory protokolů Azure monitor](logicapp-flow-connector.md).
- Jednou při exportu do místního počítače pomocí skriptu PowerShellu. Viz [Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).


## <a name="current-limitations"></a>Aktuální omezení

- Konfigurace se teď dá provést jenom pomocí požadavků CLI nebo REST. Nemůžete použít Azure Portal ani PowerShell.
- Možnost v rozhraní příkazového ```--export-all-tables``` řádku a REST není podporována a bude odebrána. Seznam tabulek v pravidlech exportu byste měli zadat explicitně.
- Podporované tabulky jsou aktuálně omezeny na konkrétní v níže uvedené části [podporované tabulky](#supported-tables) . Pokud pravidlo exportu dat obsahuje nepodporovanou tabulku, operace bude úspěšná, ale pro tuto tabulku nebudou exportována žádná data. Pokud pravidlo exportu dat obsahuje tabulku, která neexistuje, dojde k selhání s chybou. ```Table <tableName> does not exist in the workspace.```
- Pracovní prostor Log Analytics může být v libovolné oblasti s výjimkou následujících:
  - Švýcarsko – sever
  - Švýcarsko – západ
  - Oblasti státní správy Azure
- Cílový účet úložiště nebo centrum událostí musí být ve stejné oblasti jako pracovní prostor Log Analytics.
- Názvy tabulek, které mají být exportovány, nemohou být pro účet úložiště delší než 60 znaků a v centru událostí nejsou delší než 47 znaků. Tabulky s delšími názvy nebudou exportovány.

> [!NOTE]
> Log Analytics export dat zapisuje data jako doplňovací objekt blob, který je aktuálně ve verzi Preview pro Azure Data Lake Storage Gen2. Před konfigurací exportu do tohoto úložiště musíte otevřít žádost o podporu. Pro tento požadavek použijte následující podrobnosti.
> - Typ problému: Technický
> - Předplatné: vaše předplatné
> - Služba: Data Lake Storage Gen2
> - Prostředek: název prostředku
> - Shrnutí: probíhá žádost o registraci předplatného pro příjem dat z Log Analytics exportu dat.
> - Typ problému: připojení
> - Podtyp problému: potíže s připojením

## <a name="data-completeness"></a>Úplnost dat
Export dat bude pokračovat v pokusu o odeslání dat po dobu až 30 minut v případě, že cíl není k dispozici. Pokud není po 30 minutách k dispozici, budou data zahozena, dokud nebude cíl k dispozici.

## <a name="cost"></a>Cost
Pro funkci exportu dat se momentálně neúčtují žádné další poplatky. Ceny za export dat budou v budoucnu ohlášeny a oznámení poskytované před zahájením fakturace. Pokud se rozhodnete, že budete exportovat data i po uplynutí období oznámení, bude se vám účtovat příslušná sazba.

## <a name="export-destinations"></a>Exportovat cíle

### <a name="storage-account"></a>Účet úložiště
Data se odesílají do účtů úložiště každou hodinu. Konfigurace exportu dat vytvoří kontejner pro každou tabulku v účtu úložiště s názvem, pod *kterým následuje název* tabulky. Například tabulka *SecurityEvent* by se poslala do kontejneru s názvem *am-SecurityEvent*.

Cesta k objektu BLOB účtu úložiště je *WorkspaceResourceId =/Subscriptions/Subscription-ID/ResourceGroups/ \<resource-group\> /providers/Microsoft.operationalinsights/Workspaces/ \<workspace\> /y = \<four-digit numeric year\> /m = \<two-digit numeric month\> /d = \<two-digit numeric day\> /h = \<two-digit 24-hour clock hour\> /m = 00/PT1H.jsna*. Vzhledem k tomu, že se doplňovací objekty blob omezí na 50 tis zápisy do úložiště, počet exportovaných objektů BLOB může být prodloužený, pokud je počet připojení vysoký. Vzor pro pojmenování objektů BLOB v takovém případě by byl PT1H_ #. JSON, kde # je přírůstkový počet objektů BLOB.

Formát dat účtu úložiště jsou [řádky JSON](diagnostic-logs-append-blobs.md). To znamená, že každý záznam je oddělený novým řádkem, bez pole vnějších záznamů a žádné čárky mezi záznamy JSON. 

[![Ukázková data úložiště](media/logs-data-export/storage-data.png)](media/logs-data-export/storage-data.png#lightbox)

Export dat Log Analytics může zapisovat doplňovací objekty blob do neměnných účtů úložiště, pokud mají povolené zásady uchovávání *informací na základě* času. To umožňuje psát nové bloky do doplňovacího objektu BLOB a přitom zachovat ochranu neměnnosti a dodržování předpisů. Viz [Povolení chráněných objektů BLOB pro zápis](../../storage/blobs/storage-blob-immutable-storage.md#allow-protected-append-blobs-writes).

### <a name="event-hub"></a>Centrum událostí
Data se odesílají do centra událostí téměř v reálném čase, protože dosáhne Azure Monitor. Centrum událostí se vytvoří pro každý datový typ, který exportujete s názvem, *za nímž následuje název tabulky* . Například tabulka *SecurityEvent* se odeslala do centra událostí s názvem *am-SecurityEvent*. Pokud chcete, aby se exportovaná data dostala na konkrétní centrum událostí, nebo pokud máte tabulku s názvem, který překračuje limit počtu znaků 47, můžete zadat vlastní název centra událostí a exportovat do něj všechna data pro definované tabulky.

Objem exportovaných dat se v průběhu času často zvětšuje a škálování centra událostí je potřeba zvýšit, aby se zpracovávala větší přenosové rychlosti, a vyhnout se tak scénářům omezování a latenci dat. K automatickému horizontálnímu navýšení kapacity a navýšení počtu jednotek propustnosti a splnění potřeb použití byste měli použít funkci automatického rozšíření Event Hubs. Podrobnosti najdete v tématu [Automatické horizontální navýšení kapacity Event Hubs jednotky propustnosti Azure](../../event-hubs/event-hubs-auto-inflate.md) .


## <a name="prerequisites"></a>Požadavky
Níže jsou uvedené požadavky, které je nutné před konfigurací Log Analytics exportu dat dokončit.

- Účet úložiště a centrum událostí se už musí vytvořit a musí být ve stejné oblasti jako pracovní prostor Log Analytics. Pokud potřebujete replikovat data do jiných účtů úložiště, můžete použít kteroukoli z [možností redundance Azure Storage](../../storage/common/storage-redundancy.md).  
- Účet úložiště musí být StorageV1 nebo StorageV2. Klasické úložiště se nepodporuje.  
- Pokud jste nakonfigurovali účet úložiště tak, aby povoloval přístup z vybraných sítí, musíte do nastavení svého účtu úložiště přidat výjimku, abyste mohli Azure Monitor zapisovat do svého úložiště.

## <a name="enable-data-export"></a>Povolit export dat
Aby bylo možné povolit Log Analytics exportu dat, je třeba provést následující kroky. Další podrobnosti najdete v následujících částech.

- Zaregistrujte poskytovatele prostředků.
- Povolí důvěryhodné služby společnosti Microsoft.
- Vytvořte jedno nebo více pravidel exportu dat, která definují tabulky k exportu a jejich cíl.

### <a name="register-resource-provider"></a>Registrace poskytovatele prostředků
Následující poskytovatel prostředků Azure musí zaregistrovat pro vaše předplatné, aby bylo možné Log Analytics exportovat data. 

- Microsoft. Insights

Tento poskytovatel prostředků bude pravděpodobně již zaregistrován pro většinu Azure Monitorch uživatelů. Pokud to chcete ověřit, klikněte na **odběry** v Azure Portal. Vyberte své předplatné a pak klikněte na **poskytovatelé prostředků** v části **Nastavení** v nabídce. Vyhledejte **Microsoft. Insights**. Pokud je jeho stav **zaregistrován** , je již zaregistrován. V takovém případě ji zaregistrujte kliknutím na **Registrovat** .

K registraci poskytovatele prostředků můžete použít také kteroukoli z dostupných metod, jak je popsáno v tématu [poskytovatelé a typy prostředků Azure](../../azure-resource-manager/management/resource-providers-and-types.md). Následuje ukázkový příkaz pomocí prostředí PowerShell:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.insights
```

### <a name="allow-trusted-microsoft-services"></a>Povolení důvěryhodných služeb Microsoftu
Pokud jste nakonfigurovali účet úložiště tak, aby povoloval přístup z vybraných sítí, musíte přidat výjimku, aby Azure Monitor mohl zapisovat do účtu. Z **bran firewall a virtuálních sítí** pro svůj účet úložiště vyberte možnost **dovolit přístup k tomuto účtu úložiště důvěryhodným službám Microsoftu**.

[![Brány firewall a virtuální sítě v účtu úložiště](media/logs-data-export/storage-account-vnet.png)](media/logs-data-export/storage-account-vnet.png#lightbox)


### <a name="create-or-update-data-export-rule"></a>Vytvořit nebo aktualizovat pravidlo exportu dat
Pravidlo exportu dat definuje data, která se mají exportovat pro sadu tabulek do jednoho cíle. Můžete vytvořit pravidlo pro každý cíl.

Pomocí následujícího příkazu rozhraní příkazového řádku můžete zobrazit tabulky v pracovním prostoru. Může vám to usnadnit kopírování tabulek, které chcete, a zahrnutí v pravidle exportu dat.
```azurecli
az monitor log-analytics workspace table list -resource-group resourceGroupName --workspace-name workspaceName --query [].name --output table
```

Pomocí následujícího příkazu vytvořte pravidlo exportu dat do účtu úložiště pomocí rozhraní příkazového řádku.

```azurecli
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $storageAccountId
```

Pomocí následujícího příkazu můžete vytvořit pravidlo exportu dat do centra událostí pomocí rozhraní příkazového řádku (CLI).

```azurecli
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubsNamespacesId
```

Pomocí následující žádosti vytvořte pravidlo exportu dat pomocí REST API. Žádost by měla používat autorizaci nosných tokenů a typ obsahu Application/JSON.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

Tělo požadavku určuje cíl tabulek. Následuje ukázkový text žádosti REST pro účet úložiště.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
            "table1",
            "table2" 
        ],
        "enable": true
    }
}
```

Následuje vzorový text žádosti REST pro centrum událostí.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
}
```

Následuje ukázkový text žádosti REST pro centrum událostí, kde je zadaný název centra událostí. V tomto případě se všechna exportovaná data odešlou do tohoto centra událostí.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name",
            "metaData": {
                "EventHubName": "eventhub-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
  }
}
```

## <a name="view-data-export-configuration"></a>Zobrazit konfiguraci exportu dat
Pomocí následujícího příkazu můžete zobrazit konfiguraci pravidla exportu dat pomocí rozhraní příkazového řádku (CLI).

```azurecli
az monitor log-analytics workspace data-export show --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

Pomocí následující žádosti můžete zobrazit konfiguraci pravidla exportu dat pomocí REST API. Žádost by měla použít autorizaci nosných tokenů.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

## <a name="disable-an-export-rule"></a>Zakázat pravidlo exportu
Pravidla exportu lze zakázat, aby bylo možné zastavit export, pokud nepotřebujete uchovávat data po určitou dobu, například při provádění testování. Pomocí následujícího příkazu zakažte pravidlo exportu dat pomocí rozhraní příkazového řádku.

```azurecli
az monitor log-analytics workspace data-export update --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --enable false
```

Pomocí následujícího požadavku zakažte pravidlo exportu dat pomocí REST API. Žádost by měla použít autorizaci nosných tokenů.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
"table1",
    "table2" 
        ],
        "enable": false
    }
}
```

## <a name="delete-an-export-rule"></a>Odstraní pravidlo exportu.
Pomocí následujícího příkazu odstraňte pravidlo exportu dat pomocí rozhraní příkazového řádku.

```azurecli
az monitor log-analytics workspace data-export delete --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

Pomocí následujícího požadavku odstraňte pravidlo exportu dat pomocí REST API. Žádost by měla použít autorizaci nosných tokenů.

```rest
DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

## <a name="view-all-data-export-rules-in-a-workspace"></a>Zobrazit všechna pravidla exportu dat v pracovním prostoru
Pomocí následujícího příkazu můžete zobrazit všechna pravidla exportu dat v pracovním prostoru pomocí rozhraní příkazového řádku (CLI).

```azurecli
az monitor log-analytics workspace data-export list --resource-group resourceGroupName --workspace-name workspaceName
```

Pomocí následující žádosti můžete zobrazit všechna pravidla exportu dat v pracovním prostoru pomocí REST API. Žádost by měla použít autorizaci nosných tokenů.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports?api-version=2020-08-01
```

## <a name="unsupported-tables"></a>Nepodporované tabulky
Pokud pravidlo exportu dat obsahuje nepodporovanou tabulku, konfigurace bude úspěšná, ale pro tuto tabulku nebudou exportována žádná data. Pokud je tabulka později podporována, budou data exportována v daném čase.

Pokud pravidlo exportu dat obsahuje tabulku, která neexistuje, dojde k selhání s chybou. ```Table <tableName> does not exist in the workspace.```


## <a name="supported-tables"></a>Podporované tabulky
Podporované tabulky jsou aktuálně omezené na ty, které jsou uvedené níže. Všechna data z tabulky budou exportována, pokud nejsou zadána omezení. Tento seznam se bude aktualizovat, protože se přidá podpora dalších tabulek.


| Tabulka | Omezení |
|:---|:---|
| AADDomainServicesAccountLogon | |
| AADDomainServicesAccountManagement | |
| AADDomainServicesDirectoryServiceAccess | |
| AADDomainServicesLogonLogoff | |
| AADDomainServicesPolicyChange | |
| AADDomainServicesPrivilegeUse | |
| AADManagedIdentitySignInLogs | |
| AADNonInteractiveUserSignInLogs | |
| AADProvisioningLogs | |
| AADServicePrincipalSignInLogs | |
| ADAssessmentRecommendation | |
| ADFActivityRun | |
| ADFPipelineRun | |
| ADFTriggerRun | |
| ADReplicationResult | |
| ADSecurityAssessmentRecommendation | |
| ADTDigitalTwinsOperation | |
| ADTEventRoutesOperation | |
| ADTModelsOperation | |
| ADTQueryOperation | |
| ADXCommand | |
| ADXQuery | |
| AegDeliveryFailureLogs | |
| AegPublishFailureLogs | |
| Výstrahy |Částečná podpora. Některá data této tabulky se ingestují prostřednictvím účtu úložiště. Tato data se momentálně neexportují. |
| Anomálie | |
| ApiManagementGatewayLogs | |
| AppCenterError | |
| AppPlatformSystemLogs | |
| AppServiceAppLogs | |
| AppServiceAuditLogs | |
| AppServiceConsoleLogs | |
| AppServiceFileAuditLogs | |
| AppServiceHTTPLogs | |
| AppServiceIPSecAuditLogs | |
| AppServicePlatformLogs | |
| AuditLogs | |
| AutoscaleEvaluationsLog | |
| AutoscaleScaleActionsLog | |
| AWSCloudTrail | |
| AzureAssessmentRecommendation | |
| AzureDevOpsAuditing | |
| BehaviorAnalytics | |
| BlockchainApplicationLog | |
| BlockchainProxyLog | |
| BlockchainProxyLog | |
| CommonSecurityLog | |
| CommonSecurityLog | |
| ComputerGroup | |
| ConfigurationData | Částečná podpora. Některá data se ingestují prostřednictvím interních služeb, které se pro export nepodporují. Tato data se momentálně neexportují. |
| ContainerImageInventory | |
| ContainerInventory | |
| ContainerLog | |
| ContainerNodeInventory | |
| ContainerServiceLog | |
| CoreAzureBackup | |
| DatabricksAccounts | |
| DatabricksClusters | |
| DatabricksDBFS | |
| DatabricksInstancePools | |
| DatabricksJobs | |
| DatabricksNotebook | |
| DatabricksSecrets | |
| DatabricksSQLPermissions | |
| DatabricksSSH | |
| DatabricksWorkspace | |
| DnsEvents | |
| DnsInventory | |
| Dynamics365Activity | |
| Událost | Částečná podpora. Některá data této tabulky se ingestují prostřednictvím účtu úložiště. Tato data se momentálně neexportují. |
| ExchangeAssessmentRecommendation | |
| FailedIngestion | |
| FunctionAppLogs | |
| HDInsightAmbariClusterAlerts | |
| HDInsightAmbariSystemMetrics | |
| HDInsightGatewayAuditLogs | |
| HDInsightHadoopAndYarnLogs | |
| HDInsightHadoopAndYarnMetrics | |
| HDInsightHBaseLogs | |
| HDInsightHBaseMetrics | |
| HDInsightHiveAndLLAPLogsSample | |
| HDInsightKafkaLogs | |
| HDInsightKafkaMetrics | |
| HDInsightOozieLogs | |
| HDInsightSecurityLogs | |
| HDInsightSparkApplicationEvents | |
| HDInsightSparkBlockManagerEvents | |
| HDInsightSparkEnvironmentEvents | |
| HDInsightSparkEventsLog | |
| HDInsightSparkExecutorEvents | |
| HDInsightSparkExtraEvents | |
| HDInsightSparkJobEvents | |
| HDInsightSparkLogs | |
| HDInsightSparkSQLExecutionEvents | |
| HDInsightSparkStageEvents | |
| HDInsightSparkStageTaskAccumulables | |
| HDInsightSparkTaskEvents | |
| HDInsightStormLogs | |
| HDInsightStormMetrics | |
| HDInsightStormTopologyMetrics | |
| Tep | |
| HuntingBookmark | |
| InsightsMetrics | Částečná podpora. Některá data se ingestují prostřednictvím interních služeb, které se pro export nepodporují. Tato část v současnosti chybí v exportu. |
| IntuneAuditLogs | |
| IntuneDeviceComplianceOrg | |
| IntuneOperationalLogs | |
| KubeEvents | |
| KubeHealth | |
| KubeMonAgentEvents | |
| KubeNodeInventory | |
| KubePodInventory | |
| KubeServices | |
| KubeServices | |
| LAQueryLogs | |
| McasShadowItReporting | |
| MicrosoftAzureBastionAuditLogs | |
| MicrosoftDataShareReceivedSnapshotLog | |
| MicrosoftDataShareSentSnapshotLog | |
| MicrosoftDataShareShareLog | |
| MicrosoftHealthcareApisAuditLogs | |
| NWConnectionMonitorDestinationListenerResult | |
| NWConnectionMonitorDNSResult | |
| NWConnectionMonitorPathResult | |
| NWConnectionMonitorPathResult | |
| NWConnectionMonitorTestResult | |
| NWConnectionMonitorTestResult | |
| OfficeActivity | Částečná podpora. Některá data pro ingestování prostřednictvím webhooků z Office 365 do Log Analytics. Tato data se momentálně neexportují. |
| Operace | Částečná podpora. Některá data se ingestují prostřednictvím interních služeb, které se pro export nepodporují. Tato data se momentálně neexportují. |
| Výkon | Částečná podpora. V tuto chvíli jsou podporovaná jenom data o výkonu systému Windows. Data o výkonu systému Linux nejsou v současnosti exportována. |
| ProtectionStatus | |
| SCCMAssessmentRecommendation | |
| SCOMAssessmentRecommendation | |
| SecurityAlert | |
| SecurityBaseline | |
| SecurityBaselineSummary | |
| SecurityDetection | |
| SecurityEvent | |
| SecurityIncident | |
| SecurityIoTRawEvent | |
| SecurityNestedRecommendation | |
| SecurityRecommendation | |
| SfBAssessmentRecommendation | |
| SfBOnlineAssessmentRecommendation | |
| SharePointOnlineAssessmentRecommendation | |
| SignalRServiceDiagnosticLogs | |
| SigninLogs | |
| SPAssessmentRecommendation | |
| SQLAssessmentRecommendation | |
| SucceededIngestion | |
| SynapseGatewayEvents | |
| SynapseRBACEvents | |
| Syslog | Částečná podpora. Některá data této tabulky se ingestují prostřednictvím účtu úložiště. Tato data se momentálně neexportují. |
| ThreatIntelligenceIndicator | |
| Aktualizace | Částečná podpora. Některá data se ingestují prostřednictvím interních služeb, které se pro export nepodporují. Tato data se momentálně neexportují. |
| UpdateRunProgress | |
| UpdateSummary | |
| Využití | |
| UserAccessAnalytics | |
| UserPeerAnalytics | |
| Seznamu ke zhlédnutí | |
| WindowsEvent | |
| WindowsFirewall | |
| WireData | Částečná podpora. Některá data se ingestují prostřednictvím interních služeb, které se pro export nepodporují. Tato data se momentálně neexportují. |
| WorkloadMonitoringPerf | |
| WVDAgentHealthStatus | |
| WVDCheckpoints | |
| WVDConnections | |
| WVDErrors | |
| WVDFeeds | |
| WVDManagement | |


## <a name="next-steps"></a>Další kroky

- [Dotaz na exportovaná data z Azure Průzkumník dat](azure-data-explorer-query-storage.md).
