---
title: Protokol aktivit Azure
description: Podívejte se na protokol aktivit Azure a odešlete ho do protokolů Azure Monitor, Azure Event Hubs a Azure Storage.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 00e264cea34c7c3e7223b47217ecf5a59b76ba41
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592462"
---
# <a name="azure-activity-log"></a>Protokol aktivit Azure
Protokol aktivit je [protokol platformy](platform-logs-overview.md) v Azure, který poskytuje přehled o událostech na úrovni předplatného. Obsahuje například informace o úpravách prostředků nebo spouštění virtuálních počítačů. Protokol aktivit můžete zobrazit v Azure Portal nebo načíst položky pomocí PowerShellu a rozhraní příkazového řádku. Pro další funkce byste měli vytvořit nastavení diagnostiky, které odešle protokol aktivit do [Azure monitor protokolů](data-platform-logs.md), do Azure Event Hubs předat mimo Azure nebo do Azure Storage k archivaci. Tento článek poskytuje podrobné informace o zobrazení protokolu aktivit a jeho odeslání do různých cílů.

Podrobnosti o vytvoření nastavení diagnostiky najdete v tématu [Vytvoření nastavení diagnostiky pro odesílání protokolů platforem a metrik do různých cílů](diagnostic-settings.md) .

> [!NOTE]
> Položky v protokolu aktivit jsou generované systémem a není možné je změnit ani odstranit.

## <a name="view-the-activity-log"></a>Zobrazit protokol aktivit
K protokolu aktivit můžete získat přístup z většiny nabídek na webu Azure Portal. Na základě nabídky, ze které ho otevřete, se určí počáteční filtr. Pokud ho otevřete z nabídky **monitor** , jediný filtr bude v předplatném. Pokud ho otevřete z nabídky prostředku, filtr se nastaví na tento prostředek. Filtr můžete kdykoli změnit, aby se zobrazily všechny ostatní položky. Kliknutím na **Přidat filtr** přidejte do filtru další vlastnosti.

![Zobrazit protokol aktivit](./media/activity-logs-overview/view-activity-log.png)

Popis kategorií protokolů aktivit najdete v tématu [schéma událostí protokolu aktivit Azure](activity-log-schema.md#categories).

### <a name="view-change-history"></a>Zobrazit historii změn

V případě některých událostí můžete zobrazit historii změn, která ukazuje, jaké změny došlo během této události. Vyberte událost z protokolu aktivit, na kterou chcete hledat hlouběji. Vyberte kartu **historie změn (Preview)** a zobrazte tak související změny s touto událostí.

![Seznam historie změn pro událost](media/activity-logs-overview/change-history-event.png)

Pokud se s událostí nacházejí nějaké přidružené změny, zobrazí se seznam změn, které můžete vybrat. Tím otevřete stránku **historie změn (Preview)** . Na této stránce se zobrazí změny prostředku. V následujícím příkladu vidíte, že virtuální počítač změnil velikosti, ale předchozí velikost virtuálního počítače byla před změnou a s tím, jak byla změněna. Další informace o historii změn najdete v tématu [získání změn prostředků](../../governance/resource-graph/how-to/get-resource-changes.md).

![Stránka historie změn zobrazující rozdíly](media/activity-logs-overview/change-history-event-details.png)


### <a name="other-methods-to-retrieve-activity-log-events"></a>Další metody načtení událostí protokolu aktivit
K událostem protokolu aktivit můžete také přistupovat pomocí následujících metod.

- Pomocí rutiny [Get-AzLog](/powershell/module/az.monitor/get-azlog) načtěte protokol aktivit z PowerShellu. Podívejte se na [ukázky Azure monitor PowerShellu](../samples/powershell-samples.md#retrieve-activity-log).
- Pomocí [AZ monitor Activity-log](/cli/azure/monitor/activity-log) načtěte protokol aktivit z CLI.  Viz [ukázky Azure monitor CLI](../samples/cli-samples.md#view-activity-log).
- K načtení protokolu aktivit z klienta REST použijte [Azure Monitor REST API](/rest/api/monitor/) . 


## <a name="send-to-log-analytics-workspace"></a>Odeslání do pracovního prostoru služby Log Analytics
 Odešlete protokol aktivit do pracovního prostoru Log Analytics, abyste povolili funkce [Azure monitor protokolů](data-platform-logs.md) , které zahrnují následující:

- Korelujte data protokolu aktivit s dalšími daty monitorování shromážděnými pomocí Azure Monitor.
- Konsolidujte položky protokolu z několika předplatných Azure a klientů do jednoho umístění pro účely analýzy dohromady.
- Pomocí dotazů protokolu můžete provádět komplexní analýzy a získat podrobné přehledy o položkách protokolu aktivit.
- Používejte výstrahy protokolu se záznamy aktivit a umožňují komplexnější logiku výstrahy.
- Ukládání záznamů protokolu aktivit po dobu delší než 90 dní.
- Neúčtují se žádné poplatky za příjem dat protokolu aktivit uložené v pracovním prostoru Log Analytics.
- Data protokolu aktivit uložená v Log Analytics pracovním prostoru neúčtují žádné poplatky za uchovávání dat do 90 dnů.

[Vytvořte nastavení diagnostiky](diagnostic-settings.md) pro odeslání protokolu aktivit do pracovního prostoru Log Analytics. Protokol aktivit můžete odeslat z libovolného jednoho předplatného do pěti pracovních prostorů. Shromažďování protokolů napříč tenanty vyžaduje službu [Azure Lighthouse](../../lighthouse/index.yml).

Data protokolu aktivit v Log Analytics pracovním prostoru se ukládají do tabulky s názvem *AzureActivity* , kterou můžete načíst pomocí [dotazu protokolu](../log-query/log-query-overview.md) v [Log Analytics](../log-query/log-analytics-tutorial.md). Struktura této tabulky se liší v závislosti na [kategorii záznamu protokolu](activity-log-schema.md). Popis vlastností tabulky najdete v [referenčních informacích o Azure monitor](/azure/azure-monitor/reference/tables/azureactivity).

Chcete-li například zobrazit počet záznamů protokolu aktivit pro každou kategorii, použijte následující dotaz.

```kusto
AzureActivity
| summarize count() by Category
```

Chcete-li načíst všechny záznamy v kategorii správy, použijte následující dotaz.

```kusto
AzureActivity
| where Category == "Administrative"
```


## <a name="send-to-azure-event-hubs"></a>Odeslat do Azure Event Hubs
Odešlete protokol aktivit do Azure Event Hubs, abyste mohli odesílat položky mimo Azure, například pro SIEM nebo jiná řešení Log Analytics. Události protokolu aktivit z Center událostí se spotřebovávají ve formátu JSON s prvkem, který `records` obsahuje záznamy v každé datové části. Schéma závisí na kategorii a je popsána ve [schématu z účtu úložiště a centra událostí](activity-log-schema.md).

Následuje ukázka výstupních dat z Event Hubs pro protokol aktivit:

``` JSON
{
    "records": [
        {
            "time": "2019-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


## <a name="send-to--azure-storage"></a>Odeslat do služby Azure Storage
Protokol aktivit odešlete do účtu Azure Storage, pokud chcete uchovávat data protokolu déle než 90 dní pro audit, statickou analýzu nebo zálohování. Pokud potřebujete události uchovávat jenom 90 dní nebo méně, nemusíte v účtu úložiště nastavovat archivaci, protože události protokolu aktivit se uchovávají na platformě Azure po dobu 90 dnů.

Když odešlete protokol aktivit do Azure, vytvoří se v účtu úložiště kontejner úložiště hned po výskytu události. Objekty BLOB v kontejneru používají následující konvence vytváření názvů:

```
insights-activity-logs/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Například konkrétní objekt BLOB může mít název podobný následujícímu:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/y=2020/m=06/d=08/h=18/m=00/PT1H.json
```

Každý objekt blob PT1H.json obsahuje objekt blob ve formátu JSON událostí, ke kterým došlo během hodiny zadané v adrese URL objektu blob (například h=12). Během aktuální hodiny se události připojují do souboru PT1H.json, když k nim dojde. Hodnota minut (m = 00) je vždy 00, protože události protokolu prostředku jsou v jednotlivých objektech blob za hodinu rozděleny.

Každá událost je uložená v PT1H.jsv souboru s následujícím formátem, který používá společné schéma nejvyšší úrovně, ale je jinak jedinečný pro každou kategorii, jak je popsáno v tématu  [schéma protokolu aktivit](activity-log-schema.md).

``` JSON
{ "time": "2020-06-12T13:07:46.766Z", "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MV-VM-01", "correlationId": "0f0cb6b4-804b-4129-b893-70aeeb63997e", "operationName": "Microsoft.Resourcehealth/healthevent/Updated/action", "level": "Information", "resultType": "Updated", "category": "ResourceHealth", "properties": {"eventCategory":"ResourceHealth","eventProperties":{"title":"This virtual machine is starting as requested by an authorized user or process. It will be online shortly.","details":"VirtualMachineStartInitiatedByControlPlane","currentHealthStatus":"Unknown","previousHealthStatus":"Unknown","type":"Downtime","cause":"UserInitiated"}}}
```


## <a name="legacy-collection-methods"></a>Starší metody shromažďování
Tato část popisuje starší metody shromažďování protokolu aktivit, které byly použity před nastavením diagnostiky. Pokud používáte tyto metody, měli byste zvážit přechod na nastavení diagnostiky, která poskytují lepší funkčnost a konzistenci s protokoly prostředků.

### <a name="log-profiles"></a>Profily protokolů
Profily protokolu představují starší metodu pro posílání protokolu aktivit do služby Azure Storage nebo centra událostí. Pomocí následujícího postupu můžete pokračovat v práci s profilem protokolu nebo ho zakázat při přípravě na migraci na nastavení diagnostiky.

1. V nabídce **Azure monitor** v Azure Portal vyberte **Protokol aktivit**.
3. Klikněte na **Nastavení diagnostiky**.

   ![Nastavení diagnostiky](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Klikněte na fialový banner pro starší verze prostředí.

    ![Starší verze prostředí](media/diagnostic-settings-subscription/legacy-experience.png)



### <a name="configure-log-profile-using-powershell"></a>Konfigurace profilu protokolu pomocí PowerShellu

Pokud profil protokolu již existuje, musíte nejprve odebrat existující profil protokolu a pak vytvořit nový.

1. Použijte `Get-AzLogProfile` k určení, jestli profil protokolu existuje.  Pokud profil protokolu existuje, poznamenejte si jeho vlastnost *Name* .

1. Pomocí `Remove-AzLogProfile` odeberte profil protokolu pomocí hodnoty z vlastnosti *název* .

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Použijte `Add-AzLogProfile` k vytvoření nového profilu protokolu:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Vlastnost | Povinné | Popis |
    | --- | --- | --- |
    | Název |Ano |Název vašeho profilu protokolu. |
    | StorageAccountId |Ne |ID prostředku účtu úložiště, do kterého se má ukládat protokol aktivit |
    | serviceBusRuleId |Ne |Service Bus ID pravidla pro Service Bus oboru názvů, ve kterém chcete vytvořit centra událostí. Toto je řetězec ve formátu: `{service bus resource ID}/authorizationrules/{key name}` . |
    | Umístění |Ano |Čárkami oddělený seznam oblastí, pro které chcete shromažďovat události protokolu aktivit. |
    | RetentionInDays |Ano |Počet dní, po které se mají události uchovávat v účtu úložiště v rozmezí od 1 do 365. Hodnota nula ukládá protokoly po neomezenou dobu. |
    | Kategorie |Ne |Čárkami oddělený seznam kategorií událostí, které se mají shromáždit. Možné hodnoty jsou _Write_, _Delete_ a _Action_. |

### <a name="example-script"></a>Ukázkový skript
Následuje ukázkový skript prostředí PowerShell pro vytvoření profilu protokolu aktivit, který zapisuje protokol aktivit do účtu úložiště i centra událostí.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -StorageAccountId  $storageAccountId -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Konfigurace profilu protokolu pomocí Azure CLI

Pokud profil protokolu již existuje, musíte nejprve odebrat existující profil protokolu a pak vytvořit nový profil protokolu.

1. Použijte `az monitor log-profiles list` k určení, jestli profil protokolu existuje.
2. Pomocí `az monitor log-profiles delete --name "<log profile name>` odeberte profil protokolu pomocí hodnoty z vlastnosti *název* .
3. Použijte `az monitor log-profiles create` k vytvoření nového profilu protokolu:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Vlastnost | Povinné | Popis |
    | --- | --- | --- |
    | name |Ano |Název vašeho profilu protokolu. |
    | úložiště – ID účtu |Ano |ID prostředku účtu úložiště, do kterého se mají ukládat protokoly aktivit |
    | polohy |Ano |Mezerou oddělený seznam oblastí, pro které chcete shromažďovat události protokolu aktivit. Seznam všech oblastí pro vaše předplatné můžete zobrazit pomocí `az account list-locations --query [].name` . |
    | denní |Ano |Počet dní, po které se mají uchovávat události v rozmezí od 1 do 365. Hodnota nula bude ukládat protokoly po neomezenou dobu (navždy).  Je-li nastavena hodnota nula, parametr Enabled by měl být nastaven na hodnotu false. |
    |enabled | Ano |Pravda nebo nepravda  Slouží k povolení nebo zakázání zásad uchovávání informací.  Pokud je hodnota true, parametr Days musí být hodnota větší než 0.
    | categories |Ano |Prostor – seznam kategorií událostí, které mají být shromážděny. Možné hodnoty jsou Write, DELETE a Action. |


### <a name="log-analytics-workspace"></a>Pracovní prostor služby Log Analytics
Starší metoda pro odeslání protokolu aktivity do pracovního prostoru Log Analytics připojuje protokol v konfiguraci pracovního prostoru. 

1. V nabídce **Log Analytics pracovní prostory** v Azure Portal vyberte pracovní prostor pro shromáždění protokolu aktivit.
1. V části **zdroje dat pracovního prostoru** v nabídce pracovního prostoru vyberte **Protokol aktivit Azure**.
1. Klikněte na předplatné, které chcete připojit.

    ![Snímek obrazovky ukazuje Log Analytics pracovní prostor s vybraným protokolem aktivit Azure.](media/activity-log-collect/workspaces.png)

1. Kliknutím na **připojit** připojte protokol aktivit v předplatném k vybranému pracovnímu prostoru. Pokud je předplatné už připojené k jinému pracovnímu prostoru, odpojte ho kliknutím na **Odpojit** .

    ![Připojit pracovní prostory](media/activity-log-collect/connect-workspace.png)


Chcete-li nastavení zakázat, proveďte stejný postup a kliknutím na tlačítko **Odpojit** odeberte odběr z pracovního prostoru.

### <a name="data-structure-changes"></a>Změny struktury dat
Nastavení diagnostiky odesílají stejná data jako starší metoda, která se používá k odeslání protokolu aktivit s některými změnami struktury tabulky *AzureActivity* .

Sloupce v následující tabulce byly zastaralé v aktualizovaném schématu. Stále existují v *AzureActivity* , ale nebudou mít žádná data. Náhrada pro tyto sloupce není nová, ale obsahuje stejná data jako zastaralé sloupce. Jsou v jiném formátu, takže možná budete muset upravit dotazy protokolů, které je používají. 

| Zastaralý sloupec | Sloupec pro nahrazení |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> V některých případech můžou být hodnoty v těchto sloupcích velkými písmeny. Pokud máte dotaz zahrnující tyto sloupce, měli byste použít [operátor =~](/azure/kusto/query/datatypes-string-operators), aby se při porovnávání nerozlišovala malá a velká písmena.

Do *AzureActivity* v aktualizovaném schématu byly přidány následující sloupce:

- Authorization_d
- Claims_d
- Properties_d

## <a name="activity-log-analytics-monitoring-solution"></a>Řešení monitorování Activity Log Analytics
Řešení Azure Log Analytics monitoring bude brzy zastaralé a nahradí sešit pomocí aktualizovaného schématu v pracovním prostoru Log Analytics. Řešení můžete používat i v případě, že ho už máte povolený, ale dá se použít jenom v případě, že shromažďujete protokol aktivit pomocí nastavení starší verze. 



### <a name="use-the-solution"></a>Použití řešení
K monitorování řešení se dostanete z nabídky **monitor** v Azure Portal. V části **přehledy** vyberte **Další** a otevřete stránku **Přehled** s dlaždicemi řešení. Dlaždice **protokoly aktivit Azure** zobrazuje počet záznamů **AzureActivity** ve vašem pracovním prostoru.

![Dlaždice protokolů aktivit Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Kliknutím na dlaždici **protokoly aktivit Azure** otevřete zobrazení **protokolů aktivit Azure** . Zobrazení obsahuje části vizualizace v následující tabulce. Každá část obsahuje až 10 položek, které odpovídají kritériím této části pro zadaný časový rozsah. Kliknutím na **Zobrazit vše** v dolní části části můžete spustit dotaz protokolu, který vrátí všechny odpovídající záznamy.

![Řídicí panel protokolů aktivit Azure](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>Povolit řešení pro nové odběry
V tuto chvíli už brzy nebude možné do předplatného přidat řešení Activity Log Analytics pomocí Azure Portal. Můžete ji přidat pomocí následujícího postupu se šablonou Správce prostředků. 

1. Zkopírujte následující JSON do souboru s názvem *ActivityLogTemplate*. JSON.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. Nasaďte šablonu pomocí následujících příkazů PowerShellu:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```



## <a name="next-steps"></a>Další kroky

* [Přečtěte si přehled protokolů platforem](platform-logs-overview.md)
* [Kontrola schématu událostí protokolu aktivit](activity-log-schema.md)
* [Vytvoření nastavení diagnostiky pro odesílání protokolů aktivit do jiných cílů](diagnostic-settings.md)
