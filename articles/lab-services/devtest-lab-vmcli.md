---
title: Vytváření a správa virtuálních počítačů v DevTest Labs pomocí Azure CLI | Microsoft Docs
description: Naučte se používat Azure DevTest Labs k vytváření a správě virtuálních počítačů pomocí Azure CLI.
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
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: 11ac4e10cbd116ed204a8a11274408f5a5a9b4d9
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183131"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Vytváření a správa virtuálních počítačů pomocí DevTest Labs pomocí Azure CLI
Tento rychlý Start vás provede vytvořením, spuštěním, připojením, aktualizací a vyčištěním vývojového počítače ve vašem testovacím prostředí. 

Než začnete:

* Pokud se testovací prostředí nevytvořilo, najdete [tady](devtest-lab-create-lab.md)pokyny.

* [Nainstalujte rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). Pokud chcete začít, spusťte příkaz AZ Login za účelem vytvoření připojení k Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Vytvořit a ověřit virtuální počítač 
Před spuštěním příkazů souvisejících s DevTest Labs nastavte příslušný kontext Azure pomocí `az account set` příkazu:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

Příkaz k vytvoření virtuálního počítače je: `az lab vm create`. Vyžaduje se skupina prostředků pro testovací prostředí, název testovacího prostředí a název virtuálního počítače. Zbytek argumentů se mění v závislosti na typu virtuálního počítače.

Následující příkaz vytvoří image založenou na Windows z místa na trhu Azure. Název bitové kopie je stejný jako při vytváření virtuálního počítače pomocí Azure Portal. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Následující příkaz vytvoří virtuální počítač založený na vlastní imagi dostupné v testovacím prostředí:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Argument **typu image** se změnil z **Galerie** na **vlastní**. Název Image se shoduje s tím, co vidíte, pokud jste chtěli vytvořit virtuální počítač v Azure Portal.

Následující příkaz vytvoří virtuální počítač z image Marketplace s ověřováním SSH:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

Můžete také vytvořit virtuální počítače založené na vzorcích nastavením parametru pro **typ obrázku** na **vzorec**. Pokud potřebujete pro svůj virtuální počítač zvolit konkrétní virtuální síť, použijte parametry **název virtuální** sítě a **podsítě** . Další informace najdete v tématu [AZ Lab VM Create](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Ověřte, že je virtuální počítač dostupný.
`az lab vm show` Pomocí příkazu ověřte, že je virtuální počítač k dispozici před tím, než začnete a připojíte se k němu. 

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

## <a name="start-and-connect-to-the-virtual-machine"></a>Začněte a připojte se k virtuálnímu počítači.
Následující příklad příkazu spustí virtuální počítač:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Připojení k virtuálnímu počítači: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) nebo [Vzdálená plocha](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Aktualizovat virtuální počítač
Následující vzorový příkaz aplikuje artefakty na virtuální počítač:

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

Vypíše artefakty dostupné v testovacím prostředí.
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
Následující vzorový příkaz zastaví virtuální počítač.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Odstraní virtuální počítač.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Další kroky
Podívejte se na následující obsah: Dokumentace k rozhraní příkazového [řádku Azure pro Azure DevTest Labs](/cli/azure/lab?view=azure-cli-latest). 
