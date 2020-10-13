---
title: Nasazení virtuálních počítačů a škálování instancí sad na vyhrazené hostitele pomocí rozhraní příkazového řádku
description: Nasazení virtuálních počítačů a škálování instancí sad na vyhrazené hostitele pomocí Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/25/2020
ms.author: cynthn
ms.openlocfilehash: d99f8c380b486ed818aff64782ca817dab41c916
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975277"
---
# <a name="deploy-to-dedicated-hosts-using-the-azure-cli"></a>Nasazení na vyhrazené hostitele pomocí Azure CLI
 

Tento článek vás provede procesem vytvoření [vyhrazeného hostitele](../dedicated-hosts.md) Azure pro hostování virtuálních počítačů. 

Ujistěte se, že máte nainstalovanou verzi Azure CLI 2.0.70 nebo novější a přihlásili jste se k účtu Azure pomocí `az login` . 


## <a name="limitations"></a>Omezení

- Typy velikosti a hardwaru, které jsou dostupné pro vyhrazené hostitele, se v jednotlivých oblastech liší. Další informace najdete na [stránce s cenami](https://aka.ms/ADHPricing) hostitele.

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků 
Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Vytvořte skupinu prostředků pomocí AZ Group Create. Následující příklad vytvoří skupinu prostředků s názvem *myDHResourceGroup* v umístění *východní USA* .

```azurecli-interactive
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="list-available-host-skus-in-a-region"></a>Výpis dostupných SKU hostitelů v oblasti

Ne všechny SKU hostitele jsou dostupné ve všech oblastech a zónách dostupnosti. 

Seznamte se s dostupností hostitele a dalšími omezeními nabídky, než začnete zřizovat vyhrazené hostitele. 

```azurecli-interactive
az vm list-skus -l eastus2  -r hostGroups/hosts  -o table  
```
 
## <a name="create-a-host-group"></a>Vytvoření skupiny hostitelů 

**Skupina hostitelů** je prostředek, který představuje kolekci vyhrazených hostitelů. Vytvoříte skupinu hostitelů v oblasti a zóně dostupnosti a přidáte do ní hostitele. Při plánování vysoké dostupnosti jsou k dispozici další možnosti. U vyhrazených hostitelů můžete použít jednu z následujících možností: 
- Rozsah napříč několika zónami dostupnosti. V takovém případě je nutné mít skupinu hostitelů v každé z zón, které chcete použít.
- Rozložit napříč několika doménami selhání, které jsou namapované na fyzické racky. 
 
V obou případech je nutné zadat počet domén selhání pro skupinu hostitelů. Pokud nechcete rozsah domén selhání ve skupině, použijte počet domén selhání 1. 

Můžete se také rozhodnout použít jak zóny dostupnosti, tak i domény selhání. 


V tomto příkladu použijeme příkaz [AZ VM Host Group Create](/cli/azure/vm/host/group#az-vm-host-group-create) k vytvoření skupiny hostitelů pomocí zón dostupnosti i domén selhání. 

```azurecli-interactive
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

Přidejte `--automatic-placement true` parametr, který bude mít vaše virtuální počítače a instance sady škálování automaticky umístěné na hostitelích v rámci skupiny hostitelů. Další informace najdete v tématu [Ruční a automatické umístění ](../dedicated-hosts.md#manual-vs-automatic-placement).

> [!IMPORTANT]
> Automatické umístění je aktuálně ve verzi Public Preview.
>
> Chcete-li se zúčastnit verze Preview, dokončete průzkum registrace ve verzi Preview na adrese [https://aka.ms/vmss-adh-preview](https://aka.ms/vmss-adh-preview) .
>
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. 
>
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="other-examples"></a>Další příklady

Pomocí [AZ VM Host Group Create](/cli/azure/vm/host/group#az-vm-host-group-create) můžete také vytvořit skupinu hostitelů v zóně dostupnosti 1 (a žádné domény selhání).

```azurecli-interactive
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
Následující: pomocí [AZ VM Host Group Create](/cli/azure/vm/host/group#az-vm-host-group-create) vytvoří skupinu hostitelů jenom pomocí domén selhání (bude se používat jenom v oblastech, kde se zóny dostupnosti nepodporují). 

```azurecli-interactive
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Vytvoření hostitele 

Nyní vytvoříme vyhrazeného hostitele ve skupině hostitelů. Kromě názvu pro hostitele je nutné zadat SKU pro hostitele. SKU hostitele zachytí podporovanou řadu virtuálních počítačů a také generování hardwaru pro vyhrazeného hostitele.  

Další informace o SKU a cenách hostitelů najdete v tématu [ceny za vyhrazené hostitele Azure](https://aka.ms/ADHPricing).

Pomocí [AZ VM Host Create](/cli/azure/vm/host#az-vm-host-create) Vytvořte hostitele. Pokud pro skupinu hostitelů nastavíte počet domén selhání, budete požádáni o zadání domény selhání pro hostitele.  

```azurecli-interactive
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače 
Pomocí [AZ VM Create](/cli/azure/vm#az-vm-create)vytvořte virtuální počítač v rámci vyhrazeného hostitele. Pokud jste při vytváření skupiny hostitelů zadali zónu dostupnosti, budete při vytváření virtuálního počítače muset použít stejnou zónu.

```azurecli-interactive
az vm create \
   -n myVM \
   --image debian \
   --host-group myHostGroup \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```

Chcete-li umístit virtuální počítač na konkrétního hostitele, použijte `--host` místo určení skupiny hostitelů možnost `--host-group` .
 
> [!WARNING]
> Pokud vytvoříte virtuální počítač na hostiteli, který nemá dostatek prostředků, vytvoří se virtuální počítač ve stavu selhání. 

## <a name="create-a-scale-set-preview"></a>Vytvoření sady škálování (Preview)

> [!IMPORTANT]
> Virtual Machine Scale Sets na vyhrazených hostitelích je aktuálně ve verzi Public Preview.
>
> Chcete-li se zúčastnit verze Preview, dokončete průzkum registrace ve verzi Preview na adrese [https://aka.ms/vmss-adh-preview](https://aka.ms/vmss-adh-preview) .
>
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. 
>
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Když nasadíte sadu škálování, zadáte skupinu hostitelů.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --host-group myHostGroup \
  --generate-ssh-keys \
  --size Standard_D4s_v3 \
  -g myDHResourceGroup \
  --zone 1
```

Pokud chcete ručně zvolit, který hostitel má nasadit sadu škálování na, přidejte a zadejte `--host` název hostitele.


## <a name="check-the-status-of-the-host"></a>Zkontroluje stav hostitele.

Můžete kontrolovat stav hostitele a počet virtuálních počítačů, které můžete nasadit do hostitele pomocí funkce [AZ VM Host Get-instance-View](/cli/azure/vm/host#az-vm-host-get-instance-view).

```azurecli-interactive
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 Výstup bude vypadat nějak takto:
 
```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```
 
## <a name="export-as-a-template"></a>Exportovat jako šablonu 
Šablonu můžete exportovat, pokud teď chcete vytvořit další vývojové prostředí se stejnými parametry nebo produkčním prostředím, které odpovídá tomuto. Správce prostředků používá šablony JSON, které definují všechny parametry vašeho prostředí. Můžete sestavit celá prostředí odkazem na tuto šablonu JSON. Můžete vytvořit šablony JSON ručně nebo exportovat existující prostředí a vytvořit šablonu JSON. K exportu skupiny prostředků použijte [AZ Group export](/cli/azure/group#az-group-export) .

```azurecli-interactive
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

Tento příkaz vytvoří `myDHResourceGroup.json` soubor v aktuálním pracovním adresáři. Když z této šablony vytvoříte prostředí, zobrazí se výzva k zadání všech názvů prostředků. Tyto názvy můžete vyplnit v souboru šablony přidáním `--include-parameter-default-value` parametru do `az group export` příkazu. Upravte šablonu JSON tak, aby určovala názvy prostředků, nebo vytvořte parameters.jsv souboru, který určuje názvy prostředků.
 
Pokud chcete vytvořit prostředí ze šablony, použijte příkaz [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create).

```azurecli-interactive
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>Vyčištění 

Účtují se vám poplatky za vaše vyhrazené hostitele i v případě, že nejsou nasazené žádné virtuální počítače. Měli byste odstranit všechny hostitele, na které aktuálně nepoužíváte, abyste ušetřili náklady.  

Hostitele můžete odstranit jenom v případě, že ho nepoužívá žádný virtuální počítač. Odstraňte virtuální počítače pomocí [AZ VM Delete](/cli/azure/vm#az-vm-delete).

```azurecli-interactive
az vm delete -n myVM -g myDHResourceGroup
```

Po odstranění virtuálních počítačů můžete hostitele odstranit pomocí [AZ VM Host Delete](/cli/azure/vm/host#az-vm-host-delete).

```azurecli-interactive
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
Po odstranění všech hostitelů můžete skupinu hostitelů odstranit pomocí [AZ VM Host Group Delete](/cli/azure/vm/host/group#az-vm-host-group-delete).  
 
```azurecli-interactive
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
Celou skupinu prostředků můžete také odstranit v jednom příkazu. Tím se odstraní všechny prostředky vytvořené ve skupině včetně všech virtuálních počítačů, hostitelů a skupin hostitelů.
 
```azurecli-interactive
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Další kroky

- Další informace najdete v tématu Přehled [vyhrazených hostitelů](../dedicated-hosts.md) .

- Můžete také vytvořit vyhrazené hostitele pomocí [Azure Portal](../dedicated-hosts-portal.md).

- [Zde](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)najdete ukázkovou šablonu, která pro maximální odolnost v oblasti používá zóny i domény selhání.