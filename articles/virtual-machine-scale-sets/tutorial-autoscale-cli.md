---
title: Kurz – Automatické škálování škálovací sady pomocí Azure CLI 2.0 | Microsoft Docs
description: Zjistěte, jak pomocí Azure CLI 2.0 automaticky škálovat škálovací sadu virtuálních počítačů s ohledem na zvyšující a snižující se požadavky na CPU.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6f184ac0b2af3a66affecd1a3a9c247a96e616f8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Kurz: Automatické škálování škálovací sady virtuálních počítačů pomocí Azure CLI 2.0
Při vytváření škálovací sady definujete počet instancí virtuálních počítačů, které chcete spouštět. S měnícími se požadavky na aplikaci můžete počet instancí virtuálních počítačů automaticky zvyšovat nebo snižovat. Možnost automatického škálování umožňuje držet krok s požadavky zákazníků nebo reagovat na změny výkonu aplikace v průběhu jejího životního cyklu. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Použití automatického škálování u škálovací sady
> * Vytvoření a použití pravidel automatického škálování
> * Zátěžový test instancí virtuálních počítačů a aktivace pravidel automatického škálování
> * Opětovné automatické horizontální snížení kapacity po snížení požadavků

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.29 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="create-a-scale-set"></a>Vytvoření škálovací sady
S vytvářením pravidel automatického škálování vám pomůže definice několika parametrů pro ID vašeho předplatného, název skupiny prostředků a škálovací sady a umístění:

```azurecli-interactive
sub=$(az account show --query id -o tsv)
resourcegroup_name="myResourceGroup"
scaleset_name="myScaleSet"
location_name="eastus"
```

Následujícím způsobem vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#create):

```azurecli-interactive
az group create --name $resourcegroup_name --location $location_name
```

Teď vytvořte škálovací sadu virtuálních počítačů pomocí příkazu [az vmss create](/cli/azure/vmss#create). Následující příklad vytvoří škálovací sadu se *2* instancemi a vygeneruje klíče SSH, pokud ještě neexistují:

```azurecli-interactive
az vmss create \
  --resource-group $resourcegroup_name \
  --name $scaleset_name \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --instance-count 2 \
  --admin-username azureuser \
  --generate-ssh-keys
```


## <a name="define-an-autoscale-profile"></a>Definice profilu automatického škálování
Pravidla automatického škálování se nasazují jako JSON (JavaScript Object Notation) pomocí Azure CLI 2.0. Podívejme se na jednotlivé části tohoto profilu automatického škálování a pak vytvoříme kompletní příklad.

Začátek profilu automatického škálování definuje výchozí, minimální a maximální kapacitu škálovací sady. Následující příklad nastaví výchozí a minimální kapacitu na *2* instance virtuálních počítačů a maximální kapacitu na *10*:

```json
{
  "name": "autoscale rules",
  "capacity": {
    "minimum": "2",
    "maximum": "10",
    "default": "2"
  }
}
```


## <a name="create-a-rule-to-autoscale-out"></a>Vytvoření pravidla automatického horizontálního navýšení kapacity
Pokud se požadavky na vaši aplikaci zvýší, zvýší se i zatížení instancí virtuálních počítačů ve škálovací sadě. Pokud je toto zvýšené zatížení konzistentní, a nejedná se pouze o krátkou poptávku, můžete nakonfigurovat pravidla automatického škálování pro zvýšení počtu instancí virtuálních počítačů ve škálovací sadě. Po vytvoření těchto instancí virtuálních počítačů a nasazení aplikací do nich začne škálovací sada distribuovat provoz prostřednictvím nástroje pro vyrovnávání zatížení. Můžete řídit, které metriky se mají monitorovat, například CPU nebo disk, jak dlouho musí zatížení aplikace dosahovat dané prahové hodnoty a kolik instancí virtuálních počítačů se má do škálovací sady přidat.

Teď vytvořte pravidlo, které zvýší počet instancí virtuálních počítačů ve škálovací sadě, pokud je průměrné zatížení CPU vyšší než 70 % po dobu 5 minut. Když se pravidlo aktivuje, počet instancí virtuálních počítačů se zvýší o tři.

Pro toto pravidlo se používají následující parametry:

| Parametr         | Vysvětlení                                                                                                         | Hodnota           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | Metrika výkonu, která se má monitorovat a na kterou se mají použít akce škálovací sady.                                                   | Procento CPU  |
| *timeGrain*       | Četnost shromažďování metrik pro účely analýzy.                                                                   | 1 minuta        |
| *timeAggregation* | Definuje způsob agregace shromážděných metrik pro účely analýzy.                                                | Průměr         |
| *timeWindow*      | Doba, která se monitoruje před porovnáním metrik a prahových hodnot.                                   | 5 minut       |
| *operator*        | Operátor sloužící k porovnání dat metriky s prahovou hodnotou.                                                     | Větší než    |
| *threshold*       | Hodnota, která způsobí aktivaci akce pravidlem automatického škálování.                                                      | 70 %             |
| *direction*       | Definuje, jestli se má po použití pravidla kapacita škálovací sady horizontálně snížit nebo zvýšit.                                              | Zvýšit        |
| *type*            | Určuje, že se má počet instancí virtuálních počítačů změnit o určitou hodnotu.                                    | Počet změn    |
| *value*           | Počet instancí virtuálních počítačů, jejichž kapacita se má po použití pravidla horizontálně snížit nebo zvýšit.                                             | 3               |
| *cooldown*        | Doba, po kterou se má počkat před opětovným použitím pravidla, aby akce automatického škálování měly dostatek času se projevit. | 5 minut       |

Následující příklad definuje pravidlo pro horizontální navýšení kapacity počtu instancí virtuálních počítačů. Parametr *metricResourceUri* používá dříve definované proměnné pro ID předplatného, název skupiny prostředků a název škálovací sady:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT5M",
    "timeAggregation": "Average",
    "operator": "GreaterThan",
    "threshold": 70
  },
  "scaleAction": {
    "direction": "Increase",
    "type": "ChangeCount",
    "value": "3",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-a-rule-to-autoscale-in"></a>Vytvoření pravidla automatického horizontálního snížení kapacity
Večer nebo o víkendu se požadavky na vaši aplikaci můžou snížit. Pokud je toto snížené zatížení po určitou dobu konzistentní, můžete nakonfigurovat pravidla automatického škálování pro snížení počtu instancí virtuálních počítačů ve škálovací sadě. Tato akce horizontálního snížení kapacity sníží náklady na provoz škálovací sady, protože budete spouštět pouze takový počet instancí, který je potřeba ke zpracování aktuálních požadavků.

Vytvořte další pravidlo, které sníží počet instancí virtuálních počítačů ve škálovací sadě, pokud se průměrné zatížení CPU sníží pod 30 % po dobu 5 minut. Následující příklad definuje pravidlo pro horizontální snížení kapacity počtu instancí virtuálních počítačů o jednu. Parametr *metricResourceUri* používá dříve definované proměnné pro ID předplatného, název skupiny prostředků a název škálovací sady:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT5M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "ChangeCount",
    "value": "1",
    "cooldown": "PT5M"
  }
}
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Použití pravidel automatického škálování na škálovací sadu
Posledním krokem je použít profil a pravidla automatického škálování na vaši škálovací sadu. Vaše škálovací sada pak bude moct horizontálně snížit nebo zvýšit svou kapacitu na základě požadavků na aplikaci. Následujícím způsobem použijte profil automatického škálování pomocí příkazu [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create). Celý následující JSON používá profil a pravidla uvedená v předchozích částech:

```azurecli-interactive
az monitor autoscale-settings create \
    --resource-group $resourcegroup_name \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "autoscale by percentage based on CPU usage",
          "capacity": {
            "minimum": "2",
            "maximum": "10",
            "default": "2"
          },
          "rules": [
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "ChangeCount",
                "value": "3",
                "cooldown": "PT5M"
              }
            },
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
              },
              "scaleAction": {
                "direction": "Decrease",
                "type": "ChangeCount",
                "value": "1",
                "cooldown": "PT5M"
              }
            }
          ]
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="generate-cpu-load-on-scale-set"></a>Generování zatížení CPU ve škálovací sadě
Pokud chcete pravidla automatického škálování otestovat, vygenerujte nějaké zatížení CPU instancí virtuálních počítačů ve škálovací sadě. Toto simulované zatížení CPU způsobí, že pravidla automatického škálování provedou horizontální navýšení kapacity a zvýší počet instancí virtuálních počítačů. S následným snížením zatížení CPU pravidla automatického škálování provedou horizontální snížení kapacity a sníží počet instancí virtuálních počítačů.

Nejprve vypište adresu a porty pro připojení k instancím virtuálních počítačů ve škálovací sadě pomocí příkazu [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info):

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group $resourcegroup_name \
  --name $scaleset_name
```

Následující příklad výstupu ukazuje název instance, veřejnou IP adresu nástroje pro vyrovnávání zatížení a číslo portu, na které pravidla překladu adres směrují provoz:

```json
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

Připojte se přes SSH k první instanci virtuálního počítače. Pomocí parametru `-p` zadejte vlastní veřejnou IP adresu a číslo portu uvedené ve výstupu předchozího příkazu:

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50001
```

Po přihlášení nainstalujte nástroj **stress**. Spusťte *10* pracovních procesů **stress**, které vygenerují zatížení CPU. Tyto pracovní procesy budou spuštěné *420* sekund, což je dostatečná doba na to, aby pravidla automatického škálování implementovala požadovanou akci.

```azurecli-interactive
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Jakmile nástroj **stress** zobrazí podobný výstup: *stress: info: [2688] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, stisknutím klávesy *Enter* se vraťte do příkazového řádku.

Pokud chcete potvrdit, že nástroj **stress** generuje zatížení CPU, prozkoumejte zatížení aktivního systému pomocí nástroje **top**:

```azuecli-interactive
top
```

Ukončete nástroj **top** a pak ukončete připojení k instanci virtuálního počítače. Nástroj **stress** zůstane na instanci virtuálního počítače spuštěný.

```azurecli-interactive
Ctrl-c
exit
```

Připojte se k druhé instanci virtuálního počítače s použitím čísla portu uvedeného ve výstupu předchozího příkazu [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info):

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50003
```

Nainstalujte a spusťte nástroj **stress** a pak na této druhé instanci spusťte deset pracovních procesů.

```azurecli-interactive
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Jakmile nástroj **stress** zobrazí podobný výstup: *stress: info: [2713] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, opět se stisknutím klávesy *Enter* vraťte do příkazového řádku.

Ukončete připojení k druhé instanci virtuálního počítače. Nástroj **stress** zůstane na instanci virtuálního počítače spuštěný.

```azurecli-interactive
exit
```

## <a name="monitor-the-active-autoscale-rules"></a>Monitorování aktivních pravidel automatického škálování
K monitorování počtu instancí virtuálních počítačů ve škálovací sadě použijte nástroj **watch**. Zahájení procesu horizontálního navýšení kapacity na základě zatížení CPU generovaného nástrojem **stress** na jednotlivých instancích virtuálních počítačů pravidly automatického škálování trvá přibližně 5 minut:

```azurecli-interactive
watch az vmss list-instances \
  --resource-group $resourcegroup_name \
  --name $scaleset_name \
  --output table
```

Po dosažení prahové hodnoty CPU pravidla automatického škálování navýší počet instancí virtuálních počítačů ve škálovací sadě. Následující výstup ukazuje tři virtuální počítače vytvořené při automatickém horizontálním navýšení kapacity škálovací sady:

```bash
Every 2.0s: az vmss list-instances --resource-group myResourceGroup --name myScaleSet --output table

  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            MYRESOURCEGROUP  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             MYRESOURCEGROUP  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             MYRESOURCEGROUP  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Jakmile se nástroj **stress** na původních instancích virtuálních počítačů zastaví, průměrné zatížení CPU se vrátí do normálu. Po dalších 5 minutách pak pravidla automatického škálování horizontálně sníží kapacitu počtu instancí virtuálních počítačů. Akce horizontálního snížení kapacity odeberou nejprve instance virtuálních počítačů s nejvyšším ID. Když škálovací sada používá skupiny nebo zóny dostupnosti, akce pro horizontální snížení kapacity se mezi tyto instance virtuálních počítačů rovnoměrně rozloží. Následující příklad výstupu ukazuje jednu instanci virtuálního počítače odstraněnou při automatickém horizontálním snížení kapacity škálovací sady:

```bash
           6  True                  eastus      myScaleSet_6  Deleting             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Stisknutím `Ctrl-c` ukončete nástroj *watch*. Kapacita škálovací sady se nadále bude každých 5 minut horizontálně snižovat odebráním jedné instance virtuálního počítače, dokud se nedosáhne minimálního počtu 2 instancí.


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete odebrat škálovací sadu a další prostředky, odstraňte skupinu prostředků a všechny její prostředky pomocí příkazu [az group delete](/cli/azure/group#az_group_delete). Parametr `--no-wait` vrátí řízení na příkazový řádek bez čekání na dokončení operace. Parametr `--yes` potvrdí, že chcete prostředky odstranit, aniž by se na to zobrazoval další dotaz.

```azurecli-interactive
az group delete --name $resourcegroup_name --yes --no-wait
```


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak automaticky horizontálně snižovat nebo zvyšovat kapacitu škálovací sady pomocí Azure CLI 2.0:

> [!div class="checklist"]
> * Použití automatického škálování u škálovací sady
> * Vytvoření a použití pravidel automatického škálování
> * Zátěžový test instancí virtuálních počítačů a aktivace pravidel automatického škálování
> * Opětovné automatické horizontální snížení kapacity po snížení požadavků

Další příklady škálovacích sad virtuálních počítačů v akci najdete v následujících ukázkových skriptech Azure CLI 2.0:

> [!div class="nextstepaction"]
> [Ukázkové skripty Azure CLI 2.0 pro škálovací sady](cli-samples.md)