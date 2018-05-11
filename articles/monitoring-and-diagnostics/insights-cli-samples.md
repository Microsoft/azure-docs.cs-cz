---
title: Ukázek Azure rychlý start monitorování 2.0 rozhraní příkazového řádku. | Dokumenty Microsoft
description: Vzorové příkazy rozhraní příkazového řádku 2.0 pro funkce monitorování Azure. Azure monitorování je služba Microsoft Azure, který umožňuje odeslat oznámení o výstrahách, volání webové adresy URL založené na hodnotách nakonfigurované telemetrická data a škálování cloudové služby, virtuální počítače a webové aplikace.
author: kamathashwin
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1653aa81-0ee6-4622-9c65-d4801ed9006f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: ashwink
ms.openlocfilehash: a9c6cc0fb81b094e1c980e4c209184a0c0ebd428
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="azure-monitor-cli-20-quick-start-samples"></a>Ukázek Azure rychlý start monitorování 2.0 rozhraní příkazového řádku
Tento článek ukazuje ukázku, že příkazy rozhraní příkazového řádku (CLI) můžete získat přístup k funkcím Azure monitorování. Azure monitorování umožňuje škálování cloudové služby, virtuální počítače a webových aplikací a odesílat oznámení o výstrahách nebo volání webové adresy URL založené na hodnotách nakonfigurované telemetrická data.

## <a name="prerequisites"></a>Požadované součásti

Pokud jste ještě nenainstalovali rozhraní příkazového řádku Azure, postupujte podle pokynů pro [nainstalovat Azure CLI 2.0](/cli/azure/install-azure-cli). Můžete také použít [prostředí cloudu Azure](/azure/cloud-shell) spustit rozhraní příkazového řádku jako interaktivní možnosti v prohlížeči. 

## <a name="log-in-to-azure"></a>Přihlášení k Azure
Prvním krokem je přihlášení k účtu Azure.

```azurecli
az login
```

Po spuštění tohoto příkazu, budete muset přihlásit pomocí pokynů na obrazovce. Všechny příkazy fungovat v rámci vašeho předplatného výchozí.

K zobrazení seznamu podrobnosti vaším aktuálním předplatným, použijte následující příkaz.

```azurecli
az account show
```

Chcete-li změnit pracovní kontext do jiného předplatného, použijte následující příkaz.

```azurecli
az account set -s <Subscription ID or name>
```

Chcete-li zobrazit seznam všech podporovaných příkazů monitorování Azure, postupujte takto.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Zobrazit protokol aktivity pro předplatné

Chcete-li zobrazit seznam aktivity protokolu události, postupujte takto.

```azurecli
az monitor activity-log list
```

Zkuste následující příkaz a zobrazí všechny dostupné možnosti.

```azurecli
az monitor activity-log list -h
```

Tady je příklad do seznamu protokolů podle resourceGroup

```azurecli
az monitor activity-log list --resource-group <group name>
```

Příklad do seznamu protokolů volající

```azurecli
az monitor activity-log list --caller myname@company.com
```

Příklad do seznamu protokolů volající na typ prostředku, v rámci rozsahu dat.

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Práce s výstrahami 
[!NOTE] Pouze výstrahy (klasické) je v současné době podporovaný v rozhraní příkazového řádku. 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Získat pravidla výstrah (klasické) ve skupině prostředků

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Vytvoření metriky pravidlo výstrahy (klasické)

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Odstranit pravidlo výstrahy (klasické)

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Profily protokolu

Použijte informace v této části pro práci s profily protokolu.

### <a name="get-a-log-profile"></a>Získat profil protokolu

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Přidat profil protokolu s dobou uchování

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Přidat profil protokolu s uchovávání a EventHub

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>Odebrat profil protokolu

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>Diagnostika

Použijte informace v této části pro práci s nastavení diagnostiky.

### <a name="get-a-diagnostic-setting"></a>Získat nastavení diagnostiky

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-log-setting"></a>Vytvořte nastavení protokolů diagnostiky 

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

Použijte informace v této části pro práci s nastavení automatického škálování. Budete muset upravit tyto příklady.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Získat nastavení automatického škálování pro skupinu prostředků.

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
