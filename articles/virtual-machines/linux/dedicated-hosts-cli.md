---
title: Nasazení vyhrazených hostitelů Azure pomocí rozhraní příkazového řádku
description: Nasazení virtuálních počítačů na vyhrazené hostitele pomocí Azure CLI.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: b301012425e0a2590fa5ac22985abe9c96fbd419
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834926"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>Nasazení virtuálních počítačů na vyhrazené hostitele pomocí Azure CLI
 

Tento článek vás provede procesem vytvoření [vyhrazeného hostitele](dedicated-hosts.md) Azure pro hostování virtuálních počítačů. 

Ujistěte se, že máte nainstalovanou verzi Azure CLI 2.0.70 nebo novější a přihlásili jste se k účtu Azure pomocí `az login`. 


## <a name="limitations"></a>Omezení

- Sady škálování virtuálních počítačů se na vyhrazených hostitelích aktuálně nepodporují.
- Počáteční verze podporuje následující řadu virtuálních počítačů: DSv3 a ESv3. 
 

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků 
Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Vytvořte skupinu prostředků pomocí AZ Group Create. Následující příklad vytvoří skupinu prostředků s názvem *myDHResourceGroup* v umístění *východní USA* .

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="create-a-host-group"></a>Vytvoření hostitelské skupiny 

**Skupina hostitelů** je prostředek, který představuje kolekci vyhrazených hostitelů. Vytvoříte skupinu hostitelů v oblasti a zóně dostupnosti a přidáte do ní hostitele. Při plánování vysoké dostupnosti jsou k dispozici další možnosti. U vyhrazených hostitelů můžete použít jednu z následujících možností: 
- Rozsah napříč několika zónami dostupnosti. V takovém případě je nutné mít skupinu hostitelů v každé z zón, které chcete použít.
- Rozložit napříč několika doménami selhání, které jsou namapované na fyzické racky. 
 
V obou případech je nutné zadat počet domén selhání pro skupinu hostitelů. Pokud nechcete rozsah domén selhání ve skupině, použijte počet domén selhání 1. 

Můžete se také rozhodnout použít jak zóny dostupnosti, tak i domény selhání. 

V tomto příkladu použijeme příkaz [AZ VM Host Group Create](/cli/azure/vm/host/group#az-vm-host-group-create) k vytvoření skupiny hostitelů pomocí zón dostupnosti i domén selhání. 

```bash
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

### <a name="other-examples"></a>Další příklady

Pomocí [AZ VM Host Group Create](/cli/azure/vm/host/group#az-vm-host-group-create) můžete také vytvořit skupinu hostitelů v zóně dostupnosti 1 (a žádné domény selhání).

```bash
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
Následující: pomocí [AZ VM Host Group Create](/cli/azure/vm/host/group#az-vm-host-group-create) vytvoří skupinu hostitelů jenom pomocí domén selhání (bude se používat jenom v oblastech, kde se zóny dostupnosti nepodporují). 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Vytvoření hostitele 

Nyní vytvoříme vyhrazeného hostitele ve skupině hostitelů. Kromě názvu pro hostitele je nutné zadat SKU pro hostitele. SKU hostitele zachytí podporovanou řadu virtuálních počítačů a také generování hardwaru pro vyhrazeného hostitele.  Podporovány jsou následující hodnoty SKU: DSv3_Type1 a ESv3_Type1.

Další informace o SKU a cenách hostitelů najdete v tématu [ceny za vyhrazené hostitele Azure](https://aka.ms/ADHPricing).

Pomocí [AZ VM Host Create](/cli/azure/vm/host#az-vm-host-create) Vytvořte hostitele. Pokud pro skupinu hostitelů nastavíte počet domén selhání, budete požádáni o zadání domény selhání pro hostitele.  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače 
Pomocí [AZ VM Create](/cli/azure/vm#az-vm-create)vytvořte virtuální počítač v rámci vyhrazeného hostitele. Pokud jste při vytváření skupiny hostitelů zadali zónu dostupnosti, budete při vytváření virtuálního počítače muset použít stejnou zónu.

```bash
az vm create \
   -n myVM \
   --image debian \
   --generate-ssh-keys \
   --host-group myHostGroup \
   --host myHost \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```
 
> [!WARNING]
> Pokud vytvoříte virtuální počítač na hostiteli, který nemá dostatek prostředků, vytvoří se virtuální počítač ve stavu selhání. 


## <a name="check-the-status-of-the-host"></a>Zkontroluje stav hostitele.

Můžete kontrolovat stav hostitele a počet virtuálních počítačů, které můžete nasadit do hostitele pomocí funkce [AZ VM Host Get-instance-View](/cli/azure/vm/host#az-vm-host-get-instance-view).

```bash
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

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

Tento příkaz vytvoří soubor `myDHResourceGroup.json` v aktuálním pracovním adresáři. Když z této šablony vytvoříte prostředí, zobrazí se výzva k zadání všech názvů prostředků. Tyto názvy můžete vyplnit v souboru šablony přidáním parametru `--include-parameter-default-value` do příkazu `az group export`. Upravte šablonu JSON tak, aby určovala názvy prostředků, nebo vytvořte soubor Parameters. JSON, který určuje názvy prostředků.
 
Pokud chcete vytvořit prostředí ze šablony, použijte příkaz [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create).

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>Vyčištění 

Účtují se vám poplatky za vaše vyhrazené hostitele i v případě, že nejsou nasazené žádné virtuální počítače. Měli byste odstranit všechny hostitele, na které aktuálně nepoužíváte, abyste ušetřili náklady.  

Hostitele můžete odstranit jenom v případě, že ho nepoužívá žádný virtuální počítač. Odstraňte virtuální počítače pomocí [AZ VM Delete](/cli/azure/vm#az-vm-delete).

```bash
az vm delete -n myVM -g myDHResourceGroup
```

Po odstranění virtuálních počítačů můžete hostitele odstranit pomocí [AZ VM Host Delete](/cli/azure/vm/host#az-vm-host-delete).

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
Po odstranění všech hostitelů můžete skupinu hostitelů odstranit pomocí [AZ VM Host Group Delete](/cli/azure/vm/host/group#az-vm-host-group-delete).  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
Celou skupinu prostředků můžete také odstranit v jednom příkazu. Tím se odstraní všechny prostředky vytvořené ve skupině včetně všech virtuálních počítačů, hostitelů a skupin hostitelů.
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Další kroky

- Další informace najdete v tématu Přehled [vyhrazených hostitelů](dedicated-hosts.md) .

- Můžete také vytvořit vyhrazené hostitele pomocí [Azure Portal](dedicated-hosts-portal.md).

- [Zde](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)najdete ukázkovou šablonu, která pro maximální odolnost v oblasti používá zóny i domény selhání.