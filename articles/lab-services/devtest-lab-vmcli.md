---
title: Vytvoření a správa virtuálních počítačů ve službě DevTest Labs pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Zjistěte, jak pomocí Azure DevTest Labs k vytváření a správě virtuálních počítačů pomocí Azure CLI
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
ms.openlocfilehash: 48a30ef86cdb10b540ffe1231294542ccff87255
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895626"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Vytvoření a správa virtuálních počítačů s DevTest Labs pomocí Azure CLI
Tento rychlý start vás provede vytvořením, spuštění, připojení, aktualizace a čištění vývojového počítače ve vaší laboratoři. 

Než začnete:

* Pokud ještě nebyl vytvořen testovacího prostředí, najdete pokyny [tady](devtest-lab-create-lab.md).

* [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud chcete začít, spusťte az login vytvořit připojení k Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Vytvořit a ověřit virtuální počítač 
Před spouštěním příkazů DevTest Labs související nastavení odpovídající kontext Azure s použitím `az account set` příkaz:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

Vytvoření virtuálního počítače pomocí příkazu: `az lab vm create`. Skupiny prostředků pro testovací prostředí, název testovacího prostředí a název virtuálního počítače jsou všechny povinné. Zbývající argumenty měnit v závislosti na typu virtuálního počítače.

Následující příkaz vytvoří bitovou kopii pro Windows z Azure Market Place. Název image je stejné jako při vytváření virtuálního počítače pomocí webu Azure portal. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3’ --admin-username 'AdminUser' --admin-password 'Password1!'
```

Následující příkaz vytvoří virtuální počítač založený na vlastní imagi k dispozici v testovacím prostředí:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

**Typ obrázku** argument byl změněn z **Galerie** k **vlastní**. Název bitové kopie odpovídá, co vidíte, pokud byste chtěli vytvořit virtuální počítač na webu Azure Portal.

Následující příkaz vytvoří virtuálního počítače z image marketplace pomocí ssh ověřování:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

Můžete také vytvářet virtuální počítače na základě vzorců tím, že nastavíte **typ obrázku** parametr **vzorec**. Pokud chcete zvolit konkrétní virtuální sítě pro virtuální počítač, použijte **název virtuální sítě** a **podsítě** parametry. Další informace najdete v tématu [az lab vm vytvořit](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Ověřte, že je k dispozici.
Použití `az lab vm show` příkazu ověřte, že je virtuální počítač k dispozici před spuštěním k němu připojit. 

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
Příkaz v následujícím příkladu spustí virtuální počítač:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Připojte se k virtuálnímu počítači: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) nebo [vzdálené plochy](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Aktualizovat virtuální počítač
Následující ukázkový příkaz platí artefaktů pro virtuální počítač:

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
Následující ukázkový příkaz zastaví virtuální počítač.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Odstranění virtuálního počítače.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Další postup
Viz následující obsah: [Dokumentace k Azure CLI pro Azure DevTest Labs](/cli/azure/lab?view=azure-cli-latest). 