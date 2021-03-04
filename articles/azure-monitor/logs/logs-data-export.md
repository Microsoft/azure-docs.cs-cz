---
title: Export dat pracovního prostoru Log Analytics v Azure Monitor (Preview)
description: Log Analytics data export umožňuje průběžně exportovat data vybraných tabulek z pracovního prostoru Log Analytics do účtu služby Azure Storage nebo do Azure Event Hubs v průběhu shromažďování.
ms.subservice: logs
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurecli
author: bwren
ms.author: bwren
ms.date: 02/07/2021
ms.openlocfilehash: df165b83a6635fbcf72c94a4d16cbdf16c337636
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713588"
---
# <a name="log-analytics-workspace-data-export-in-azure-monitor-preview"></a>Export dat pracovního prostoru Log Analytics v Azure Monitor (Preview)
Export dat v pracovním prostoru Log Analytics v Azure Monitor umožňuje průběžně exportovat data z vybraných tabulek v pracovním prostoru Log Analytics do účtu služby Azure Storage nebo Event Hubs Azure jako shromážděná. Tento článek poskytuje podrobné informace o této funkci a postupu konfigurace exportu dat ve vašich pracovních prostorech.

## <a name="overview"></a>Přehled
Po nakonfigurování exportu dat pro váš pracovní prostor Log Analytics se všechna nová data odesílaná do vybraných tabulek v pracovním prostoru automaticky exportují do účtu úložiště v hodinových připojeních objektů BLOB nebo do centra událostí téměř v reálném čase.

![Přehled exportu dat](media/logs-data-export/data-export-overview.png)

Všechna data ze zahrnutých tabulek se exportují bez filtru. Když například nakonfigurujete pravidlo exportu dat pro tabulku *SecurityEvent* , všechna data odesílaná do tabulky *SecurityEvent* se exportují počínaje z času konfigurace.


## <a name="other-export-options"></a>Další možnosti exportu
Export dat Log Analytics pracovního prostoru průběžně exportuje data z pracovního prostoru Log Analytics. K dalším možnostem exportu dat pro konkrétní scénáře patří následující:

- Plánovaný export z dotazu protokolu pomocí aplikace logiky To se podobá funkci exportu dat, ale umožňuje odeslat filtrovaná nebo agregovaná data do služby Azure Storage. Tato metoda i když se vztahuje k [omezením dotazů protokolu](../service-limits.md#log-analytics-workspaces), najdete v tématu [archivace dat z Log Analytics pracovního prostoru do Azure Storage pomocí aplikace logiky](logs-export-logic-app.md).
- Jednou při exportu do místního počítače pomocí skriptu PowerShellu. Viz [Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).


## <a name="limitations"></a>Omezení

- Konfiguraci lze provést v současnosti pomocí požadavků CLI nebo REST. Azure Portal nebo PowerShell ještě nejsou podporované.
- Možnost v rozhraní příkazového ```--export-all-tables``` řádku a REST není podporována a bude odebrána. Seznam tabulek v pravidlech exportu byste měli zadat explicitně.
- Podporované tabulky jsou aktuálně omezeny na konkrétní v níže uvedené části [podporované tabulky](#supported-tables) . 
- Pokud pravidlo exportu dat obsahuje nepodporovanou tabulku, operace bude úspěšná, ale pro tuto tabulku nebudou exportována žádná data, dokud není tabulka podporovaná. 
- Pokud pravidlo exportu dat obsahuje tabulku, která neexistuje, dojde k chybě s chybou ```Table <tableName> does not exist in the workspace``` .
- Pracovní prostor Log Analytics může být v libovolné oblasti s výjimkou následujících:
  - Oblasti Azure Government
  - Japonsko – západ
  - Brazílie – jihovýchod
  - Norsko – východ
  - Spojené arabské emiráty sever
- V pracovním prostoru můžete vytvořit dvě pravidla exportu – v může být jedno pravidlo pro centrum událostí a jedno pravidlo k účtu úložiště.
- Cílový účet úložiště nebo centrum událostí musí být ve stejné oblasti jako pracovní prostor Log Analytics.
- Názvy tabulek, které mají být exportovány, nemohou být pro účet úložiště delší než 60 znaků a v centru událostí nejsou delší než 47 znaků. Tabulky s delšími názvy nebudou exportovány.
- Podpora připojení objektů BLOB pro Azure Data Lake Storage je teď ve [verzi Public Preview omezená](https://azure.microsoft.com/updates/append-blob-support-for-azure-data-lake-storage-preview/) .

## <a name="data-completeness"></a>Úplnost dat
Export dat bude pokračovat v pokusu o odeslání dat po dobu až 30 minut v případě, že cíl není k dispozici. Pokud není po 30 minutách k dispozici, budou data zahozena, dokud nebude cíl k dispozici.

## <a name="cost"></a>Náklady
Pro funkci exportu dat se momentálně neúčtují žádné další poplatky. Ceny za export dat budou v budoucnu ohlášeny a oznámení poskytované před zahájením fakturace. Pokud se rozhodnete, že budete exportovat data i po uplynutí období oznámení, bude se vám účtovat příslušná sazba.

## <a name="export-destinations"></a>Exportovat cíle

### <a name="storage-account"></a>Účet úložiště
Data se odesílají do účtů úložiště, když dosáhnou Azure Monitor a ukládají se v hodinových doplňovací objektech blob. Konfigurace exportu dat vytvoří kontejner pro každou tabulku v účtu úložiště s názvem, pod *kterým následuje název* tabulky. Například tabulka *SecurityEvent* by se poslala do kontejneru s názvem *am-SecurityEvent*.

Cesta k objektu BLOB účtu úložiště je *WorkspaceResourceId =/Subscriptions/Subscription-ID/ResourceGroups/ \<resource-group\> /providers/Microsoft.operationalinsights/Workspaces/ \<workspace\> /y = \<four-digit numeric year\> /m = \<two-digit numeric month\> /d = \<two-digit numeric day\> /h = \<two-digit 24-hour clock hour\> /m = 00/PT1H.jsna*. Vzhledem k tomu, že se doplňovací objekty blob omezí na 50 tis zápisy do úložiště, počet exportovaných objektů BLOB může být prodloužený, pokud je počet připojení vysoký. Vzor pro pojmenování objektů BLOB v takovém případě by byl PT1H_ #. JSON, kde # je přírůstkový počet objektů BLOB.

Formát dat účtu úložiště jsou [řádky JSON](../essentials/resource-logs-blob-format.md). To znamená, že každý záznam je oddělený novým řádkem, bez pole vnějších záznamů a žádné čárky mezi záznamy JSON. 

[![Ukázková data úložiště](media/logs-data-export/storage-data.png)](media/logs-data-export/storage-data.png#lightbox)

Export dat Log Analytics může zapisovat doplňovací objekty blob do neměnných účtů úložiště, pokud mají povolené zásady uchovávání *informací na základě* času. To umožňuje psát nové bloky do doplňovacího objektu BLOB a přitom zachovat ochranu neměnnosti a dodržování předpisů. Viz [Povolení chráněných objektů BLOB pro zápis](../../storage/blobs/storage-blob-immutable-storage.md#allow-protected-append-blobs-writes).

> [!NOTE]
> Podpora připojení objektu BLOB pro úložiště Azure Data Lake je teď dostupná ve verzi Preview ve všech oblastech Azure. Před vytvořením pravidla exportu pro Azure Data Lake úložiště [Zaregistrujte se do omezené verze Public Preview](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4mEEwKhLjlBjU3ziDwLH-pURDk2NjMzUTVEVzU5UU1XUlRXSTlHSlkxQS4u) . Export nebude bez tohoto zápisu fungovat.

### <a name="event-hub"></a>Centrum událostí
Data se odesílají do centra událostí téměř v reálném čase, protože dosáhne Azure Monitor. Centrum událostí se vytvoří pro každý datový typ, který exportujete s názvem, *za nímž následuje název tabulky* . Například tabulka *SecurityEvent* se odeslala do centra událostí s názvem *am-SecurityEvent*. Pokud chcete, aby se exportovaná data dostala na konkrétní centrum událostí, nebo pokud máte tabulku s názvem, který překračuje limit počtu znaků 47, můžete zadat vlastní název centra událostí a exportovat do něj všechna data pro definované tabulky.

> [!IMPORTANT]
> [Počet podporovaných Center událostí na obor názvů je 10](../../event-hubs/event-hubs-quotas#common-limits-for-all-tiers). Pokud exportujete více než 10 tabulek, zadejte vlastní název centra událostí pro export všech tabulek do tohoto centra událostí. 

Požadavky:
1. SKU centra událostí úrovně Basic podporuje [omezení](../../event-hubs/event-hubs-quotas.md#basic-vs-standard-tiers) velikosti menší události a některé protokoly v pracovním prostoru můžou přesáhnout a vyřadit. Jako cíl exportu doporučujeme použít centrum událostí Standard nebo vyhrazené.
2. Objem exportovaných dat se v průběhu času často zvětšuje a škálování centra událostí je potřeba zvýšit, aby se zpracovávala větší přenosové rychlosti, a vyhnout se tak scénářům omezování a latenci dat. K automatickému horizontálnímu navýšení kapacity a navýšení počtu jednotek propustnosti a splnění potřeb použití byste měli použít funkci automatického rozšíření Event Hubs. Podrobnosti najdete v tématu [Automatické horizontální navýšení kapacity Event Hubs jednotky propustnosti Azure](../../event-hubs/event-hubs-auto-inflate.md) .

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

Tento poskytovatel prostředků bude pravděpodobně již zaregistrován pro většinu Azure Monitorch uživatelů. Pokud to chcete ověřit, klikněte na **odběry** v Azure Portal. Vyberte své předplatné a pak klikněte na **poskytovatelé prostředků** v části **Nastavení** v nabídce. Vyhledejte **Microsoft. Insights**. Pokud je jeho stav **zaregistrován**, je již zaregistrován. V takovém případě ji zaregistrujte kliknutím na **Registrovat** .

K registraci poskytovatele prostředků můžete použít také kteroukoli z dostupných metod, jak je popsáno v tématu [poskytovatelé a typy prostředků Azure](../../azure-resource-manager/management/resource-providers-and-types.md). Následuje ukázkový příkaz pomocí prostředí PowerShell:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.insights
```

### <a name="allow-trusted-microsoft-services"></a>Povolení důvěryhodných služeb Microsoftu
Pokud jste nakonfigurovali účet úložiště tak, aby povoloval přístup z vybraných sítí, musíte přidat výjimku, aby Azure Monitor mohl zapisovat do účtu. Z **bran firewall a virtuálních sítí** pro svůj účet úložiště vyberte možnost **dovolit přístup k tomuto účtu úložiště důvěryhodným službám Microsoftu**.

[![Brány firewall a virtuální sítě v účtu úložiště](media/logs-data-export/storage-account-vnet.png)](media/logs-data-export/storage-account-vnet.png#lightbox)


### <a name="create-or-update-data-export-rule"></a>Vytvořit nebo aktualizovat pravidlo exportu dat
Pravidlo exportu dat definuje data, která se mají exportovat pro sadu tabulek do jednoho cíle. Pro každý cíl můžete vytvořit jedno pravidlo.


# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

–

# <a name="powershell"></a>[PowerShell](#tab/powershell)

–

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí následujícího příkazu rozhraní příkazového řádku můžete zobrazit tabulky v pracovním prostoru. Může vám to usnadnit kopírování tabulek, které chcete, a zahrnutí v pravidle exportu dat.

```azurecli
az monitor log-analytics workspace table list --resource-group resourceGroupName --workspace-name workspaceName --query [].name --output table
```

Pomocí následujícího příkazu vytvořte pravidlo exportu dat do účtu úložiště pomocí rozhraní příkazového řádku.

```azurecli
$storageAccountResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $storageAccountResourceId
```

Pomocí následujícího příkazu můžete vytvořit pravidlo exportu dat do centra událostí pomocí rozhraní příkazového řádku (CLI). Pro každou tabulku se vytvoří samostatné centrum událostí.

```azurecli
$eventHubsNamespacesResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubsNamespacesResourceId
```

Pomocí následujícího příkazu vytvořte pravidlo exportu dat pro konkrétní centrum událostí pomocí rozhraní příkazového řádku. Všechny tabulky jsou exportovány do zadaného názvu centra událostí. 

```azurecli
$eventHubResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name/eventHubName/eventhub-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubResourceId
```

# <a name="rest"></a>[REST](#tab/rest)

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

# <a name="template"></a>[Šablona](#tab/json)

Pomocí následujícího příkazu vytvořte pravidlo exportu dat pro účet úložiště pomocí šablony.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "storageAccountRuleName": {
            "defaultValue": "storage-account-rule-name",
            "type": "string"
        },
        "storageAccountResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
                {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/' , parameters('storageAccountRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('storageAccountResourceId')]"
                      },
                      "tableNames": [
                          "Heartbeat",
                          "InsightsMetrics",
                          "VMConnection",
                          "Usage"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

Pomocí následujícího příkazu vytvořte pravidlo exportu dat do centra událostí pomocí šablony. Pro každou tabulku se vytvoří samostatné centrum událostí.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]"
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

Pomocí následujícího příkazu vytvořte pravidlo exportu dat pro konkrétní centrum událostí pomocí šablony. Všechny tabulky jsou exportovány do zadaného názvu centra událostí.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        },
        "eventhubName": {
            "defaultValue": "event-hub-name",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]",
                          "metaData": {
                              "eventHubName": "[parameters('eventhubName')]"
                          }
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

---

## <a name="view-data-export-rule-configuration"></a>Zobrazit konfiguraci pravidla exportu dat

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

–

# <a name="powershell"></a>[PowerShell](#tab/powershell)

–

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí následujícího příkazu můžete zobrazit konfiguraci pravidla exportu dat pomocí rozhraní příkazového řádku (CLI).

```azurecli
az monitor log-analytics workspace data-export show --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

Pomocí následující žádosti můžete zobrazit konfiguraci pravidla exportu dat pomocí REST API. Žádost by měla použít autorizaci nosných tokenů.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

# <a name="template"></a>[Šablona](#tab/json)

–

---

## <a name="disable-an-export-rule"></a>Zakázat pravidlo exportu

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

–

# <a name="powershell"></a>[PowerShell](#tab/powershell)

–

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pravidla exportu lze zakázat, aby bylo možné zastavit export, pokud nepotřebujete uchovávat data po určitou dobu, například při provádění testování. Pomocí následujícího příkazu zakažte pravidlo exportu dat pomocí rozhraní příkazového řádku.

```azurecli
az monitor log-analytics workspace data-export update --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --enable false
```

# <a name="rest"></a>[REST](#tab/rest)

Pravidla exportu lze zakázat, aby bylo možné zastavit export, pokud nepotřebujete uchovávat data po určitou dobu, například při provádění testování. Pomocí následujícího požadavku zakažte pravidlo exportu dat pomocí REST API. Žádost by měla použít autorizaci nosných tokenů.

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

# <a name="template"></a>[Šablona](#tab/json)

Pravidla exportu lze zakázat, aby bylo možné zastavit export, pokud nepotřebujete uchovávat data po určitou dobu, například při provádění testování. Nastavením ```"enable": false``` v šabloně zakážete export dat.

---

## <a name="delete-an-export-rule"></a>Odstraní pravidlo exportu.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

–

# <a name="powershell"></a>[PowerShell](#tab/powershell)

–

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí následujícího příkazu odstraňte pravidlo exportu dat pomocí rozhraní příkazového řádku.

```azurecli
az monitor log-analytics workspace data-export delete --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

Pomocí následujícího požadavku odstraňte pravidlo exportu dat pomocí REST API. Žádost by měla použít autorizaci nosných tokenů.

```rest
DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

# <a name="template"></a>[Šablona](#tab/json)

–

---

## <a name="view-all-data-export-rules-in-a-workspace"></a>Zobrazit všechna pravidla exportu dat v pracovním prostoru

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

–

# <a name="powershell"></a>[PowerShell](#tab/powershell)

–

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí následujícího příkazu můžete zobrazit všechna pravidla exportu dat v pracovním prostoru pomocí rozhraní příkazového řádku (CLI).

```azurecli
az monitor log-analytics workspace data-export list --resource-group resourceGroupName --workspace-name workspaceName
```

# <a name="rest"></a>[REST](#tab/rest)

Pomocí následující žádosti můžete zobrazit všechna pravidla exportu dat v pracovním prostoru pomocí REST API. Žádost by měla použít autorizaci nosných tokenů.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports?api-version=2020-08-01
```

# <a name="template"></a>[Šablona](#tab/json)

–

---

## <a name="unsupported-tables"></a>Nepodporované tabulky
Pokud pravidlo exportu dat obsahuje nepodporovanou tabulku, konfigurace bude úspěšná, ale pro tuto tabulku nebudou exportována žádná data. Pokud je tabulka později podporována, budou data exportována v daném čase.

Pokud pravidlo exportu dat obsahuje tabulku, která neexistuje, dojde k selhání s chybou tabulka <tableName> v pracovním prostoru neexistuje.


## <a name="supported-tables"></a>Podporované tabulky
Podporované tabulky jsou aktuálně omezené na ty, které jsou uvedené níže. Všechna data z tabulky budou exportována, pokud nejsou zadána omezení. Tento seznam se bude aktualizovat, protože se přidá podpora dalších tabulek.


| Tabulka | Omezení |
|:---|:---|
| AADDomainServicesAccountLogon |  |
| AADDomainServicesAccountManagement |  |
| AADDomainServicesDirectoryServiceAccess |  |
| AADDomainServicesLogonLogoff |  |
| AADDomainServicesPolicyChange |  |
| AADDomainServicesPrivilegeUse |  |
| AADManagedIdentitySignInLogs |  |
| AADNonInteractiveUserSignInLogs |  |
| AADProvisioningLogs |  |
| AADServicePrincipalSignInLogs |  |
| ABSBotRequests |  |
| ACSBillingUsage |  |
| ACSSMSIncomingOperations |  |
| ADAssessmentRecommendation |  |
| ADFActivityRun |  |
| ADFPipelineRun |  |
| ADFTriggerRun |  |
| ADReplicationResult |  |
| ADSecurityAssessmentRecommendation |  |
| ADTDigitalTwinsOperation |  |
| ADTEventRoutesOperation |  |
| ADTModelsOperation |  |
| ADTQueryOperation |  |
| ADXCommand |  |
| ADXQuery |  |
| AegDeliveryFailureLogs |  |
| AegPublishFailureLogs |  |
| Výstrahy |  |
| AmlOnlineEndpointConsoleLog |  |
| ApiManagementGatewayLogs |  |
| AppCenterError |  |
| AppPlatformSystemLogs |  |
| AppServiceAppLogs |  |
| AppServiceAuditLogs |  |
| AppServiceConsoleLogs |  |
| AppServiceFileAuditLogs |  |
| AppServiceHTTPLogs |  |
| AppServicePlatformLogs |  |
| AuditLogs |  |
| AutoscaleEvaluationsLog |  |
| AutoscaleScaleActionsLog |  |
| AWSCloudTrail |  |
| AzureAssessmentRecommendation |  |
| AzureDevOpsAuditing |  |
| BehaviorAnalytics |  |
| BlockchainApplicationLog |  |
| BlockchainProxyLog |  |
| CommonSecurityLog |  |
| ComputerGroup |  |
| ConfigurationData | Částečná podpora – některá data se ingestují prostřednictvím interních služeb, které se pro export nepodporují. Tato část v současnosti chybí v exportu. |
| ContainerImageInventory |  |
| ContainerInventory |  |
| ContainerLog |  |
| ContainerNodeInventory |  |
| ContainerServiceLog |  |
| CoreAzureBackup |  |
| DatabricksAccounts |  |
| DatabricksClusters |  |
| DatabricksDBFS |  |
| DatabricksInstancePools |  |
| DatabricksJobs |  |
| DatabricksNotebook |  |
| DatabricksSecrets |  |
| DatabricksSQLPermissions |  |
| DatabricksSSH |  |
| DatabricksWorkspace |  |
| DnsEvents |  |
| DnsInventory |  |
| Dynamics365Activity |  |
| Událost | Částečná podpora – některá data v této tabulce se ingestují prostřednictvím účtu úložiště. Tato část v současnosti chybí v exportu. |
| ExchangeAssessmentRecommendation |  |
| FailedIngestion |  |
| FunctionAppLogs |  |
| Tep |  |
| HuntingBookmark |  |
| InsightsMetrics | Částečná podpora – některá data se ingestují prostřednictvím interních služeb, které se pro export nepodporují. Tato část v současnosti chybí v exportu. |
| IntuneAuditLogs |  |
| IntuneDevices |  |
| IntuneOperationalLogs |  |
| KubeEvents |  |
| KubeHealth |  |
| KubeMonAgentEvents |  |
| KubeNodeInventory |  |
| KubePodInventory |  |
| KubeServices |  |
| LAQueryLogs |  |
| McasShadowItReporting |  |
| MicrosoftAzureBastionAuditLogs |  |
| MicrosoftDataShareReceivedSnapshotLog |  |
| MicrosoftDataShareSentSnapshotLog |  |
| MicrosoftHealthcareApisAuditLogs |  |
| NWConnectionMonitorPathResult |  |
| NWConnectionMonitorTestResult |  |
| OfficeActivity | Částečná podpora – některá data pro ingestování prostřednictvím webhooků z O365 do LA. Tato část v současnosti chybí v exportu. |
| Operace | Částečná podpora – některá data se ingestují prostřednictvím interních služeb, které se pro export nepodporují. Tato část v současnosti chybí v exportu. |
| Výkon | Částečná podpora – v současné době se podporují jenom data o výkonu Windows. V současné době chybí data o výkonu systému Linux. |
| PowerBIDatasetsTenant |  |
| PowerBIDatasetsWorkspace |  |
| PowerBIDatasetsWorkspacePreview |  |
| SCCMAssessmentRecommendation |  |
| SCOMAssessmentRecommendation |  |
| SecurityAlert |  |
| SecurityBaseline |  |
| SecurityBaselineSummary |  |
| SecurityDetection |  |
| SecurityEvent | Částečná podpora – některá data v této tabulce se ingestují prostřednictvím účtu úložiště. Tato část v současnosti chybí v exportu. |
| SecurityIncident |  |
| SecurityIoTRawEvent |  |
| SecurityNestedRecommendation |  |
| SecurityRecommendation |  |
| SfBAssessmentRecommendation |  |
| SfBOnlineAssessmentRecommendation |  |
| SharePointOnlineAssessmentRecommendation |  |
| SignalRServiceDiagnosticLogs |  |
| SigninLogs |  |
| SPAssessmentRecommendation |  |
| SQLAssessmentRecommendation |  |
| SucceededIngestion |  |
| SynapseBigDataPoolApplicationsEnded |  |
| SynapseBuiltinSqlPoolRequestsEnded |  |
| SynapseGatewayApiRequests |  |
| SynapseIntegrationActivityRuns |  |
| SynapseIntegrationPipelineRuns |  |
| SynapseIntegrationTriggerRuns |  |
| SynapseRbacOperations |  |
| SynapseSqlPoolDmsWorkers |  |
| SynapseSqlPoolExecRequests |  |
| SynapseSqlPoolRequestSteps |  |
| SynapseSqlPoolSqlRequests |  |
| SynapseSqlPoolWaits |  |
| Syslog | Částečná podpora – některá data v této tabulce se ingestují prostřednictvím účtu úložiště. Tato část v současnosti chybí v exportu. |
| ThreatIntelligenceIndicator |  |
| Aktualizace | Částečná podpora – některá data se ingestují prostřednictvím interních služeb, které se pro export nepodporují. Tato část v současnosti chybí v exportu. |
| UpdateRunProgress |  |
| UpdateSummary |  |
| Využití |  |
| Seznamu ke zhlédnutí |  |
| WindowsEvent |  |
| WindowsFirewall |  |
| WireData | Částečná podpora – některá data se ingestují prostřednictvím interních služeb, které se pro export nepodporují. Tato část v současnosti chybí v exportu. |
| WVDCheckpoints |  |
| WVDConnections |  |
| WVDErrors |  |
| WVDFeeds |  |
| WVDManagement |  |


## <a name="next-steps"></a>Další kroky

- [Dotaz na exportovaná data z Azure Průzkumník dat](../logs/azure-data-explorer-query-storage.md).
