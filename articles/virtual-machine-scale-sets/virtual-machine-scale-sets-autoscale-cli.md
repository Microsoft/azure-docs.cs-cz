---
title: "Sadách škálování virtuálních počítačů škálování pomocí rozhraní příkazového řádku Azure | Microsoft Docs"
description: "Postup vytvoření pravidla automatického škálování pro škálování virtuálních počítačů, nastaví se 2.0 rozhraní příkazového řádku Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 83e93d9c-cac0-41d3-8316-6016f5ed0ce4
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 8552f6b2723fef2c61d49a34d2d60c2a6c209a32
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Automatické škálování škálování virtuálních počítačů, nastavit pomocí Azure CLI 2.0
Když vytvoříte sadu škálování, definujete se počet instancí virtuálního počítače, které chcete spustit. Podle požadavků vaší aplikaci změní, můžete automaticky zvýšit nebo snížit počet instancí virtuálního počítače. Schopnost škálování umožňuje udržovat tempo s poptávku zákazníků nebo odpověď na změny výkonu aplikace v průběhu cyklu vaší aplikace.

Tento článek ukazuje, jak vytvořit pravidla škálování pomocí Azure CLI 2.0, který sledovat výkon instancí virtuálního počítače ve škálovací sadě. Tato pravidla škálování zvyšte nebo snižte počet instancí virtuálního počítače v reakci na tyto metriky výkonu. Můžete také provést tyto kroky s [prostředí Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) nebo [portál Azure](virtual-machine-scale-sets-autoscale-portal.md).


## <a name="prerequisites"></a>Požadavky
K vytvoření pravidla automatického škálování, budete potřebovat existujícího virtuálního počítače sady škálování. Můžete vytvořit s měřítkem [portál Azure](virtual-machine-scale-sets-create-portal.md), [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md), nebo [prostředí Azure PowerShell](virtual-machine-scale-sets-create-powershell.md).

Aby bylo snazší pro vytvoření pravidel škálování, definujte některé proměnné pro škálovací sadu. V následujícím příkladu definuje proměnné s názvem sad škálování *myScaleSet* ve skupině prostředků s názvem *myResourceGroup* a v *eastus* oblast. Vaše předplatné se získat ID s [az účet zobrazit](/cli/azure/account#az_account_show). Pokud máte více předplatných, které jsou spojené s vaším účtem, vrátí se pouze první předplatné. Názvy a ID předplatného upravte takto:

```azurecli
sub=$(az account show --query id -o tsv)
resourcegroup_name="myResourceGroup"
scaleset_name="myScaleSet"
location_name="eastus"
```

## <a name="define-an-autoscale-profile"></a>Definování profilu škálování
Pravidla automatického škálování jsou nasazeny jako JSON (JavaScript Object Notation) s Azure CLI 2.0. Dokončení formátu JSON, který definuje a nasadí pravidel škálování naleznete dále v tomto článku. 

Definuje výchozí spuštění automatického škálování profilu, nastavená kapacita škály minimální a maximální. Následující příklad nastaví výchozí a minimální, kapacity *2* virtuálních počítačů instancí a maximálně *10*:

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


## <a name="create-a-rule-to-automatically-scale-out"></a>Vytvořit pravidlo, které automaticky škálovat
Pokud vaše aplikace vyžádání zvyšuje, nastavit zatížení instancím virtuálních počítačů ve vaší škálování zvyšuje. Pokud je tato zvýšená zátěž konzistentní, ne jenom stručný vyžádání, můžete nakonfigurovat pravidla škálování zvýšit počet instancí virtuálního počítače ve škálovací sadě. Při vytváření těchto instancí virtuálního počítače a aplikace nasadí, byly sadou škálování začne distribuovat provoz prostřednictvím nástroje pro vyrovnávání zatížení. Můžete určit, jaké metriky, které chcete monitorovat, jako je například procesoru nebo disk, jak dlouho zatížení aplikace musí splňovat danou prahovou hodnotu a kolik instancí virtuálních počítačů pro přidání do měřítka nastavit.

Umožňuje vytvořit pravidlo, které zvýší se počet instancí virtuálního počítače v škálování nastavená, pokud průměrná zatížení procesoru je větší než 70 % po dobu 10 minut. Pokud toto pravidlo aktivuje, je 20 % zvýšit počet instancí virtuálního počítače. V sady škálování s malým počtem instancí virtuálních počítačů, můžete nastavit `type` k *ChangeCount* a zvýšit `value` podle *1* nebo *2* instance. V sady škálování s velký počet instancí virtuálního počítače, zvýšení o 10 % nebo 20 % může být vhodnější instance virtuálních počítačů.

Pro toto pravidlo se používají tyto parametry:

| Parametr         | Vysvětlení                                                                                                         | Hodnota           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | Metrika výkonu ke sledování a škálování použít na nastavit akce.                                                   | Procento CPU  |
| *časovými úseky*       | Jak často metriky se shromažďují pro analýzu.                                                                   | 1 minuta        |
| *Agregace času* | Definuje, jak by měla být agregován shromažďovat metriky pro analýzu.                                                | Průměr         |
| *Hodnota timeWindow*      | Množství času, které jsou monitorovány před porovnání hodnot metriky a prahová hodnota.                                   | 10 minut      |
| *operátor*        | Operátor použit k porovnání metriky data před prahovou hodnotu.                                                     | Větší než    |
| *Prahová hodnota*       | Hodnota, která způsobí, že pravidlo škálování akci aktivovat.                                                      | 70%             |
| *směr*       | Určuje, zda byly sadou škálování by měl škálovat nahoru nebo dolů, když se pravidlo vztahuje.                                             | Zvětšit        |
| *Typ*            | Označuje, že se počet instancí virtuálního počítače by měli měnit procentuální hodnota.                                 | Procentuální změnu  |
| *Hodnota*           | Jak velký počet instancí virtuálního počítače by měl škálovat nahoru nebo dolů, když se pravidlo vztahuje.                                            | 20              |
| *cooldown*        | Množství času, který se má čekat před pravidlo se použije znovu tak, aby akce škálování čas vstoupily v platnost. | 5 minut       |

V následujícím příkladu definuje pravidla pro horizontální škálování počtu instancí virtuálních počítačů. *MetricResourceUri* používá předtím definovaný pro ID předplatného, název skupiny prostředků a škálování, nastavte název proměnné:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT10M",
    "timeAggregation": "Average",
    "operator": "GreaterThan",
    "threshold": 70
  },
  "scaleAction": {
    "direction": "Increase",
    "type": "PercentChangeCount",
    "value": "20",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-a-rule-to-automatically-scale-in"></a>Vytvořit pravidlo, které automaticky škálovat v
Večer nebo o víkendech se mohou snížit, vyžádání vaší aplikace. Je-li tento ke snížení zatížení přes v časovém intervalu konzistentní, můžete nakonfigurovat pravidla automatického škálování pro snížení počtu instancí virtuálních počítačů v sadě škálování. Tato akce škálování v snižuje náklady na provozování vaší škálování nastavena jako spustíte se počet instancí, které jsou nutné ke splnění aktuální vyžádání.

Vytvořte jiné pravidlo, které sníží se počet instancí virtuálního počítače v škálování nastavená, pokud průměrná zatížení procesoru pak klesne pod 30 % po dobu 10 minut. V následujícím příkladu definuje pravidla pro horizontální škálování počtu instancí virtuálních počítačů. *MetricResourceUri* používá předtím definovaný pro ID předplatného, název skupiny prostředků a škálování, nastavte název proměnné:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT10M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "PercentChangeCount",
    "value": "20",
    "cooldown": "PT5M"
  }
}
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Použití pravidel automatického škálování pro sadu škálování
Posledním krokem je při použití profilů automatického škálování a pravidla pro škálovací sadu. Vaše měřítko je poté automaticky škálovat příchozí nebo odchozí v závislosti na aplikaci. Použít profil škálování s [vytvořit nastavení automatického škálování monitorování az](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create) následujícím způsobem. Dokončení JSON používá profil a pravidel uvedených v předchozí části.

```azurecli
az monitor autoscale-settings create \
    --resource-group myResourceGroup \
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
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "PercentChangeCount",
                "value": "20",
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
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
              },
              "scaleAction": {
                "direction": "Decrease",
                "type": "PercentChangeCount",
                "value": "20",
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


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitorování počtu instancí v sadě škálování
Pokud chcete zobrazit počet a stav instancí virtuálních počítačů, zobrazit seznam instancí ve vaší měřítku, s [az vmss seznamu instance](/cli/azure/vmss#az_vmss_list_instances). Stav označuje, pokud je jako měřítka nastavit automaticky horizontálně navýší kapacitu, nebo je zrušení zřízení jako měřítka automaticky přizpůsobí v zřizování instance virtuálního počítače. Následující příklad zobrazení stavu instance virtuálních počítačů pro škálování nastavení s názvem *myScaleSet* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```


## <a name="autoscale-based-on-a-schedule"></a>Automatické škálování podle plánu
V předchozích příkladech automaticky škálovat škálování nastavit příchozí nebo odchozí metriky základní hostitele, jako je například využití procesoru. Můžete také vytvořit pravidla automatické škálování podle plánů. Tato pravidla na základě plánu umožňují automaticky škálovat počet instancí virtuálního počítače před očekávaný nárůst vyžádání aplikace, jako je základní pracovní hodiny a poté automaticky škálovat počet instancí v čase, který předpokládáte méně vyžádání, jako je například víkendu.

Použití pravidla na základě plánu škálování, vytvořte profil JSON, který definuje počet instancí virtuálního počítače ke spuštění pro pevnou počáteční a koncové časový interval. V následujícím příkladu definuje pravidla pro rozšíření Škálováním do *10* instancí v *9* dvanáctihodinového každý pracovní den (pondělí až pátek).

```json
{
  "name": "Scale out during each work day",
  "capacity": {
      "minimum": "10",
      "maximum": "10",
      "default": "10"
  },
  "rules": [],
  "recurrence": {
      "frequency": "Week",
      "schedule": {
          "timeZone": "Pacific Standard Time",
          "days": [
              "Monday",
              "Tuesday",
              "Wednesday",
              "Thursday",
              "Friday"
          ],
          "hours": [
              9
          ],
          "minutes": [
              0
          ]
      }
  }
}
```

Škálování v průběhu večer, vytvořte jiné pravidlo, které určuje menší počet instancí virtuálních počítačů a příslušné počáteční čas.

Následující kompletní příklad definuje pravidla pro škálování a potom měřítka v a pak použije profil škálování s [vytvořit nastavení automatického škálování monitorování az](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create). Tento příklad přepíše pravidla na základě metrika škálování vytvořená v předchozích příkladech. *MetricResourceUri* používá předtím definovaný pro ID předplatného, název skupiny prostředků a škálování, nastavte název proměnné:

```azurecli
az monitor autoscale-settings create \
    --resource-group myResourceGroup \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "Scale out during each work day",
          "capacity": {
            "minimum": "10",
            "maximum": "10",
            "default": "10"
          },
          "rules": [],
          "recurrence": {
            "frequency": "Week",
            "schedule": {
              "timeZone": "Pacific Standard Time",
              "days": [
                "Monday",
                "Tuesday",
                "Wednesday",
                "Thursday",
                "Friday"
              ],
              "hours": [
                9
              ],
              "minutes": [
                0
              ]
            }
          }
        },
        {
          "name": "Scale in during the evening",
          "capacity": {
            "minimum": "3",
            "maximum": "3",
            "default": "3"
          },
          "rules": [],
          "recurrence": {
            "frequency": "Week",
            "schedule": {
              "timeZone": "Pacific Standard Time",
              "days": [
                "Monday",
                "Tuesday",
                "Wednesday",
                "Thursday",
                "Friday"
              ],
              "hours": [
                18
              ],
              "minutes": [
                0
              ]
            }
          }
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak používat automatické škálování pravidla můžete škálovat horizontálně a zvýšit nebo snížit *číslo* instance virtuálních počítačů ve vaší škálování nastavit. Můžete taky škálovat svisle zvýšení nebo snížení instance virtuálního počítače *velikost*. Další informace najdete v tématu [svislé škálování s sady škálování virtuálního počítače](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Informace o tom, jak spravovat instancím virtuálních počítačů najdete v tématu [sadách škálování virtuálních počítačů spravovat pomocí prostředí Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Zjistěte, jak vygenerovat upozornění, když vaše škálování pravidla aktivační událost, najdete v tématu [používat akce škálování k odesílání e-mailu a webhooku oznámení výstrah v monitorování Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Můžete také [protokoly auditu použijte k odesílání e-mailu a webhooku oznámení výstrah v monitorování Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
