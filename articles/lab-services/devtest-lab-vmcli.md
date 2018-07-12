---
title: Vytvoření a správa virtuálních počítačů ve službě DevTest Labs pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Zjistěte, jak pomocí Azure DevTest Labs k vytváření a správě virtuálních počítačů pomocí Azure CLI 2.0
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 0f6713b9b8704e813ab1fd77ab1cf4e71e7f6670
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38235425"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Vytvoření a správa virtuálních počítačů s DevTest Labs pomocí Azure CLI
Tento rychlý start vás provede vytvořením, spuštění, připojení, aktualizace a čištění vývojového počítače ve vaší laboratoři. 

Než začnete:

* Pokud ještě nebyl vytvořen testovacího prostředí, najdete pokyny [tady](devtest-lab-create-lab.md).

* [Instalace CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Pokud chcete začít, spusťte az login vytvořit připojení k Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Vytvořit a ověřit virtuální počítač 
Vytvoření virtuálního počítače z image marketplace pomocí ssh ověřování.
```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```
> [!NOTE]
> Vložit **skupiny prostředků testovacího prostředí** názvem v parametru--& gt; resource-group.
>

Pokud chcete vytvořit virtuální počítač s pomocí vzorců, použít vzorec parametru--v [az lab vm vytvořit](https://docs.microsoft.com/cli/azure/lab/vm#az_lab_vm_create).


Ověřte, že je k dispozici.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>Spuštění a připojení k virtuálnímu počítači
Spuštění virtuálního počítače.
```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```
> [!NOTE]
> Vložit **skupiny prostředků testovacího prostředí** názvem v parametru--& gt; resource-group.
>

Připojte se k virtuálnímu počítači: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) nebo [vzdálené plochy](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Aktualizovat virtuální počítač
Použijte artefakty ve virtuálním počítači.
```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

Přehled artefaktů dostupných v testovacím prostředí.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand "properties(\$expand=artifacts)" --query 'artifacts[].{artifactId: artifactId, status: status}'
```
```json
{
  "artifactId": "/subscriptions/abcdeftgh1213123/resourceGroups/lisalab123RG822645/providers/Microsoft.DevTestLab/labs/lisalab123/artifactSources/public repo/artifacts/linux-install-nodejs",
  "status": "Succeeded"
}
```

## <a name="stop-and-delete-the-virtual-machine"></a>Zastavení a odstranění virtuálního počítače    
Zastavení virtuálního počítače.
```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Odstranění virtuálního počítače.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]