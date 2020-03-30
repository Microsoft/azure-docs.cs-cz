---
title: Nasazení virtuálních počítačů SIP do vyhrazených hostitelů pomocí cli
description: Nasazujte virtuální počítače do vyhrazených hostitelů pomocí příkazového příkazového příkazu k azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: ba40e610e31a1215ac90baf63a04b435b636d68a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127695"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>Nasazení virtuálních počítačů do vyhrazených hostitelů pomocí příkazového příkazového příkazu Azure
 

Tento článek vás provede, jak vytvořit [vyhrazeného hostitele](dedicated-hosts.md) Azure pro hostování virtuálních počítačů.This article guides through you through how to create a Azure dedicated host to host your virtual machines (VMs). 

Ujistěte se, že jste nainstalovali Azure CLI verze 2.0.70 `az login`nebo novější a přihlášení k účtu Azure pomocí . 


## <a name="limitations"></a>Omezení

- Škálovací sady virtuálních strojů nejsou aktuálně podporovány na vyhrazených hostitelích.
- Velikosti a typy hardwaru, které jsou k dispozici pro vyhrazené hostitele, se liší podle oblasti. Další informace najdete na [stránce s cenami](https://aka.ms/ADHPricing) hostitele.
 

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků 
Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Vytvořte skupinu prostředků pomocí vytvoření skupiny az. Následující příklad vytvoří skupinu prostředků s názvem *myDHResourceGroup* v umístění *usa – východ.*

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="create-a-host-group"></a>Vytvoření skupiny hostitelů 

**Skupina hostitelů** je prostředek, který představuje kolekci vyhrazených hostitelů. Vytvoříte skupinu hostitelů v oblasti a zóně dostupnosti a přidáte do ní hostitele. Při plánování vysoké dostupnosti, existují další možnosti. S vyhrazenými hostiteli můžete použít jednu nebo obě následující možnosti: 
- Rozsah mezi více zónami dostupnosti. V takovém případě musíte mít skupinu hostitelů v každé zóně, kterou chcete použít.
- Rozsah mezi více domén ách selhání, které jsou mapovány na fyzické regály. 
 
V obou případech je třeba zadat počet domén selhání pro vaši skupinu hostitelů. Pokud nechcete, aby se ve skupině promítají domény selhání, použijte počet domén selhání 1. 

Můžete se také rozhodnout použít zóny dostupnosti i domény selhání. 

V tomto příkladu použijeme [vytvoření skupiny hostitelů az vm](/cli/azure/vm/host/group#az-vm-host-group-create) k vytvoření skupiny hostitelů pomocí zón dostupnosti i domén selhání. 

```bash
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

### <a name="other-examples"></a>Další příklady

Skupinu [hostitelů az vm](/cli/azure/vm/host/group#az-vm-host-group-create) můžete také použít k vytvoření skupiny hostitelů v zóně dostupnosti 1 (a bez domén selhání).

```bash
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
Následující používá [az vm skupiny hostitelů vytvořit](/cli/azure/vm/host/group#az-vm-host-group-create) skupinu hostitelů pomocí domén selhání pouze (pro použití v oblastech, kde nejsou podporovány zóny dostupnosti). 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Vytvoření hostitele 

Nyní vytvoříme vyhrazeného hostitele ve skupině hostitelů. Kromě názvu hostitele je nutné zadat skladovou položku pro hostitele. Skladová položka hostitele zachycuje podporovanou řadu virtuálních počítače a generaci hardwaru pro vašeho vyhrazeného hostitele.  

Další informace o hostitelských skum a cenách najdete v [tématu Ceny vyhrazeného hostitele Azure](https://aka.ms/ADHPricing).

K vytvoření hostitele [použijte vytvoření hostitele az vm.](/cli/azure/vm/host#az-vm-host-create) Pokud pro skupinu hostitelů nastavíte počet domén selhání, budete vyzváni k zadání domény selhání pro hostitele.  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače 
Vytvořte virtuální počítač v rámci vyhrazeného hostitele pomocí [az vm create](/cli/azure/vm#az-vm-create). Pokud jste při vytváření skupiny hostitelů zadali zónu dostupnosti, musíte při vytváření virtuálního počítače použít stejnou zónu.

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
> Pokud vytvoříte virtuální počítač na hostiteli, který nemá dostatek prostředků, virtuální počítač se vytvoří ve stavu SELHÁNÍ. 


## <a name="check-the-status-of-the-host"></a>Kontrola stavu hostitele

Můžete zkontrolovat stav hostitele a kolik virtuálních počítačů, které můžete stále nasadit do hostitele pomocí [az vm hostitele get-instance view](/cli/azure/vm/host#az-vm-host-get-instance-view).

```bash
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 Výstup bude vypadat podobně jako tento:
 
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
Šablonu můžete exportovat, pokud nyní chcete vytvořit další vývojové prostředí se stejnými parametry nebo produkční prostředí, které odpovídá. Správce prostředků používá šablony JSON, které definují všechny parametry pro vaše prostředí. Vytvoření celé prostředí odkazem na tuto šablonu JSON. Šablony JSON můžete vytvořit ručně nebo exportovat existující prostředí a vytvořit šablonu JSON za vás. K exportu skupiny prostředků použijte [export skupiny az.](/cli/azure/group#az-group-export)

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

Tento příkaz `myDHResourceGroup.json` vytvoří soubor v aktuálním pracovním adresáři. Při vytváření prostředí z této šablony budete vyzváni ke všem názvům prostředků. Tyto názvy můžete naplnit v `--include-parameter-default-value` souboru `az group export` šablony přidáním parametru do příkazu. Upravte šablonu JSON a určete názvy prostředků nebo vytvořte soubor parameters.json, který určuje názvy prostředků.
 
Chcete-li vytvořit prostředí ze šablony, použijte [vytvoření nasazení skupiny AZ](/cli/azure/group/deployment#az-group-deployment-create).

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>Vyčištění 

Poplatky za vyhrazené hostitele se vám účtují i v případě, že se nenasazují žádné virtuální počítače. Měli byste odstranit všechny hostitele, které právě nepoužíváte k úspoře nákladů.  

Hostitele můžete odstranit pouze v případě, že už nejsou virtuální počítače, které by ho používaly. Odstraňte virtuální mích pomocí [az vm delete](/cli/azure/vm#az-vm-delete).

```bash
az vm delete -n myVM -g myDHResourceGroup
```

Po odstranění virtuálních ms můžete odstranit hostitele pomocí [odstranění hostitele az vm](/cli/azure/vm/host#az-vm-host-delete).

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
Po odstranění všech hostitelů můžete skupinu hostitelů odstranit pomocí [odstranění skupiny hostitelů AZ VM](/cli/azure/vm/host/group#az-vm-host-group-delete).  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
Můžete také odstranit celou skupinu prostředků v jednom příkazu. Tím odstraníte všechny prostředky vytvořené ve skupině, včetně všech virtuálních uživatelů, hostitelů a skupin hostitelů.
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Další kroky

- Další informace naleznete v přehledu [vyhrazených hostitelů.](dedicated-hosts.md)

- Vyhrazené hostitele můžete také vytvořit pomocí [portálu Azure](dedicated-hosts-portal.md).

- Zde je [nalezena](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)ukázková šablona , která používá zóny i domény selhání pro maximální odolnost proti chybám v oblasti.