---
title: Úvodní ukázky v Azure Monitor, rozhraní příkazového řádku
description: Ukázkové příkazy rozhraní příkazového řádku pro funkce Azure monitoru. Azure Monitor je služba Microsoft Azure, který umožňuje odeslat oznámení o výstrahách, volání webové adresy URL na základě hodnot nakonfigurované telemetrických dat a automatické škálování cloudových služeb, virtuálních počítačů a webových aplikací.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: robb
ms.component: ''
ms.openlocfilehash: 5fb4e4f754fdac9e72fc9317c7e1ccfa6a08feb2
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53326030"
---
# <a name="azure-monitor-cli-quick-start-samples"></a>Úvodní ukázky v Azure Monitor, rozhraní příkazového řádku
V tomto článku se dozvíte, ukázky, že příkazy rozhraní příkazového řádku (CLI), umožňují přístup k funkcím Azure Monitor. Azure Monitor vám umožní automatické škálování cloudové služby, virtuální počítače a webové aplikace a odesílat oznámení o výstrahách nebo volání webových adres URL na základě hodnot nakonfigurované telemetrická data.

## <a name="prerequisites"></a>Požadavky

Pokud jste ještě nenainstalovali Azure CLI, postupujte podle pokynů pro [instalace rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli). Můžete také použít [Azure Cloud Shell](/azure/cloud-shell) spuštění rozhraní příkazového řádku jako interaktivní prostředí v prohlížeči. Viz úplný přehled všech dostupných příkazů v [referenční informace k Azure CLI monitorování](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). 

## <a name="log-in-to-azure"></a>Přihlášení k Azure
Prvním krokem je přihlašovací jméno ke svému účtu Azure.

```azurecli
az login
```

Po spuštění tohoto příkazu, budete muset přihlásit pomocí pokynů na obrazovce. Všechny příkazy fungují v rámci výchozího předplatného.

Seznam podrobností o aktuálním předplatném, použijte následující příkaz.

```azurecli
az account show
```

Chcete-li změnit pracovního kontextu do jiného předplatného, použijte následující příkaz.

```azurecli
az account set -s <Subscription ID or name>
```

Pokud chcete zobrazit seznam všech podporovaných příkazů Azure Monitor, postupujte takto.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Zobrazení protokolu aktivit pro odběr

Pokud chcete zobrazit seznam události protokolu aktivit, postupujte takto.

```azurecli
az monitor activity-log list
```

Vyzkoušejte následující příkaz a zobrazí všechny dostupné možnosti.

```azurecli
az monitor activity-log list -h
```

Tady je příklad do seznamu protokolů podle skupiny prostředků

```azurecli
az monitor activity-log list --resource-group <group name>
```

Příklad seznamu protokolů volajícím

```azurecli
az monitor activity-log list --caller myname@company.com
```

Příklad seznamu protokolů volajícím na typ prostředku v rámci rozsah dat

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Práce s výstrahami 
> [!NOTE]
> V rozhraní příkazového řádku je momentálně podporován pouze upozornění (klasická). 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Získání pravidla upozornění (klasická) ve skupině prostředků

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Vytvořit pravidlo metriky upozornění (klasická)

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Odstranit pravidlo upozornění (klasická)

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Profily protokolů

Použijte informace v této části pro práci s profily protokolů.

### <a name="get-a-log-profile"></a>Získat profil protokolu

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Přidat profil protokolu s uchováváním informací.

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Přidat profil protokolu s uchování a centra událostí

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>Odebrání profilu protokolu

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>Diagnostika

Použijte informace v této části Postup při nastavení diagnostiky.

### <a name="get-a-diagnostic-setting"></a>Získá nastavení diagnostiky.

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-log-setting"></a>Vytvořte nastavení diagnostických protokolů 

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <storage account ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

### <a name="delete-a-diagnostic-setting"></a>Odstranit nastavení diagnostiky

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Automatické škálování

Použijte informace v této části pro práci s nastavením automatického škálování. Budete muset upravit tyto příklady.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Získat nastavení automatického škálování pro skupinu prostředků

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Získat nastavení automatického škálování podle názvu ve skupině prostředků

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Nastavení automatického škálování

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```
