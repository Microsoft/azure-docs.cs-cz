---
title: Ukázky pro rychlý Start pro rozhraní příkazového řádku Azure Monitor
description: Ukázky příkazů rozhraní příkazového řádku pro funkce Azure Monitor. Azure Monitor je služba Microsoft Azure, která umožňuje odesílání oznámení o výstrahách, volání webových adres URL na základě hodnot nakonfigurovaných dat telemetrie a automatického škálování Cloud Services, Virtual Machines a Web Apps.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 05/16/2018
ms.openlocfilehash: 0def528e0c432ecbc706622f0b8c29c2d610c663
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893531"
---
# <a name="azure-monitor-cli-quick-start-samples"></a>Ukázky pro rychlý Start pro rozhraní příkazového řádku Azure Monitor
V tomto článku se dozvíte, jak vzorkovat příkazy rozhraní příkazového řádku (CLI), které vám pomůžou při přístupu k funkcím Azure Monitor. Azure Monitor umožňuje automatické škálování Cloud Services, Virtual Machines a Web Apps a odesílání oznámení o výstrahách nebo volání webových adres URL na základě hodnot nakonfigurovaných dat telemetrie.

## <a name="prerequisites"></a>Předpoklady

Pokud jste rozhraní příkazového řádku Azure ještě nenainstalovali, postupujte podle pokynů pro [instalaci rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). Můžete také použít [Azure Cloud Shell](/azure/cloud-shell) ke spuštění CLI jako interaktivní prostředí v prohlížeči. Podívejte se na úplný odkaz na všechny dostupné příkazy v [odkazu Azure monitor CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). 

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.
Prvním krokem je přihlášení k vašemu účtu Azure.

```azurecli
az login
```

Po spuštění tohoto příkazu se budete muset přihlásit prostřednictvím pokynů na obrazovce. Všechny příkazy fungují v kontextu výchozího předplatného.

Pokud chcete zobrazit seznam podrobností o aktuálním předplatném, použijte následující příkaz.

```azurecli
az account show
```

Chcete-li změnit pracovní kontext na jiné předplatné, použijte následující příkaz.

```azurecli
az account set -s <Subscription ID or name>
```

Chcete-li zobrazit seznam všech podporovaných příkazů Azure Monitor, proveďte následující kroky.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Zobrazení protokolu aktivit pro předplatné

Chcete-li zobrazit seznam událostí protokolu aktivit, proveďte následující kroky.

```azurecli
az monitor activity-log list
```

Pokud chcete zobrazit všechny dostupné možnosti, zkuste následující postup.

```azurecli
az monitor activity-log list -h
```

Tady je příklad, jak zobrazit seznam protokolů podle zdroje.

```azurecli
az monitor activity-log list --resource-group <group name>
```

Příklad pro výpis protokolů podle volajícího

```azurecli
az monitor activity-log list --caller myname@company.com
```

Příklad výpisu protokolů podle volajícího pro typ prostředku v rámci rozsahu dat

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Práce s výstrahami 
> [!NOTE]
> V rozhraní příkazového řádku v tomto okamžiku je podporována pouze výstraha (Classic). 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Získat pravidla upozornění (Classic) ve skupině prostředků

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Vytvoření pravidla výstrahy metriky (klasické)

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Odstraní pravidlo pro upozornění (Classic).

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Profily protokolů

Informace v této části použijte k práci s profily protokolů.

### <a name="get-a-log-profile"></a>Získat profil protokolu

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Přidání profilu protokolu se uchováváním

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Přidání profilu protokolu se uchováváním informací a EventHub

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

Informace v této části použijte k práci s nastavením diagnostiky.

### <a name="get-a-diagnostic-setting"></a>Získat nastavení diagnostiky

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-setting"></a>Vytvoření nastavení diagnostiky 

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

### <a name="delete-a-diagnostic-setting"></a>Odstraní nastavení diagnostiky.

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Automatické škálování

Informace v této části použijte k práci s nastavením automatického škálování. Tyto příklady je potřeba upravit.

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

