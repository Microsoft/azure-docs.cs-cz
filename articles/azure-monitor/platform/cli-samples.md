---
title: Ukázky rychlého spuštění příkazového příkazu Azure Monitor
description: Ukázkové příkazy příkazového příkazu příkazu příkazu pro funkce Azure Monitoru. Azure Monitor je služba Microsoft Azure, která umožňuje odesílat upozornění, volat webové adresy URL na základě hodnot nakonfigurovaných telemetrických dat a automatické škálování cloudových služeb, virtuálních počítačů a webových aplikací.
ms.subservice: ''
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: 69687cee8b8a907b82f2c848242ac64d54dedb87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248928"
---
# <a name="azure-monitor-cli-quick-start-samples"></a>Ukázky rychlého spuštění příkazového příkazu Azure Monitor
Tento článek ukazuje ukázkové příkazy rozhraní příkazového řádku (CLI), které vám pomohou získat přístup k funkcím Azure Monitoru. Azure Monitor umožňuje automatické škálování cloudových služeb, virtuálních počítačů a webových aplikací a odesílat upozornění nebo volat webové adresy URL na základě hodnot nakonfigurovaných telemetrických dat.

## <a name="prerequisites"></a>Požadavky

Pokud jste ještě nenainstalovali azure cli, postupujte podle pokynů pro [instalaci azure CLI](/cli/azure/install-azure-cli). Azure Cloud [Shell](/azure/cloud-shell) můžete také použít ke spuštění příkazového příkazového příkazu jako interaktivní prostředí ve vašem prohlížeči. Podívejte se na úplný odkaz na všechny dostupné příkazy v [odkazu rozhraní příkazu Azure Monitor .](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest) 

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.
Prvním krokem je přihlášení k účtu Azure.

```azurecli
az login
```

Po spuštění tohoto příkazu se musíte přihlásit pomocí pokynů na obrazovce. Všechny příkazy fungují v kontextu výchozího předplatného.

Chcete-li uvést podrobnosti o aktuálním předplatném, použijte následující příkaz.

```azurecli
az account show
```

Chcete-li změnit pracovní kontext na jiné předplatné, použijte následující příkaz.

```azurecli
az account set -s <Subscription ID or name>
```

Chcete-li zobrazit seznam všech podporovaných příkazů azure monitoru, proveďte následující kroky.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Zobrazení protokolu aktivit pro předplatné

Chcete-li zobrazit seznam událostí protokolu aktivit, proveďte následující kroky.

```azurecli
az monitor activity-log list
```

Chcete-li zobrazit všechny dostupné možnosti, vyzkoušejte následující možnosti.

```azurecli
az monitor activity-log list -h
```

Zde je příklad seznamu protokolů podle skupiny prostředků.

```azurecli
az monitor activity-log list --resource-group <group name>
```

Příklad seznamu protokolů podle volajícího

```azurecli
az monitor activity-log list --caller myname@company.com
```

Příklad seznamu protokolů volajícího na typu prostředku v rozsahu dat

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Práce s výstrahami 
> [!NOTE]
> V cli jsou v současné době podporovány pouze výstrahy (klasické). 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Získání pravidel výstrah (klasické) ve skupině prostředků

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Vytvoření pravidla upozornění metriky (klasické)

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Odstranění pravidla výstrahy (klasické)

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Profily protokolů

Informace v této části slouží k práci s profily protokolu.

### <a name="get-a-log-profile"></a>Získání profilu protokolu

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Přidání profilu protokolu s uchováním

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Přidání profilu protokolu s uchováváním informací a EventHub

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

Informace v této části slouží k práci s diagnostickým nastavením.

### <a name="get-a-diagnostic-setting"></a>Získání diagnostického nastavení

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-setting"></a>Vytvoření diagnostického nastavení 

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

### <a name="delete-a-diagnostic-setting"></a>Odstranění diagnostického nastavení

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Automatické škálování

Informace v této části slouží k práci s nastavením automatického škálování. Tyto příklady je třeba upravit.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Získání nastavení automatického škálování pro skupinu prostředků

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Získání nastavení automatického škálování podle názvu ve skupině prostředků

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Nastavení automatického škálování

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```

