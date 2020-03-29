---
title: Vytváření a správa virtuálních počítačů v laboratořích DevTest Labs pomocí azure CLI
description: Zjistěte, jak pomocí Azure DevTest Labs vytvářet a spravovat virtuální počítače pomocí azure CLI
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: d3cd104e36cb407e9b1b833335869cac2c69d0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167052"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Vytváření a správa virtuálních počítačů pomocí devtest labs pomocí azure cli
Tento rychlý start vás provede vytvořením, spuštěním, připojením, aktualizací a vyčištěním vývojového počítače v testovacím prostředí. 

Než začnete:

* Pokud laboratoř nebyla vytvořena, pokyny naleznete [zde](devtest-lab-create-lab.md).

* [Nainstalujte příkazové příkazové nebo tonové zaokrocené.](/cli/azure/install-azure-cli) Chcete-li spustit, spusťte az přihlášení k vytvoření připojení s Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Vytvoření a ověření virtuálního počítače 
Před provedením příkazů souvisejících s DevTest Labs nastavte `az account set` příslušný kontext Azure pomocí příkazu:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

Příkaz k vytvoření virtuálního `az lab vm create`počítače je: . Skupina prostředků pro testovací prostředí, název testovacího prostředí a název virtuálního počítače jsou všechny povinné. Zbývající argumenty se mění v závislosti na typu virtuálního počítače.

Následující příkaz vytvoří image založenou na Windows z Azure Market Place. Název bitové kopie je stejný, jako byste viděli při vytváření virtuálního počítače pomocí portálu Azure. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Následující příkaz vytvoří virtuální počítač na základě vlastní image dostupné v testovacím prostředí:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Argument **typu obrázku** se změnil z **galerie** na **vlastní**. Název bitové kopie odpovídá tomu, co vidíte, pokud jste vytvořili virtuální počítač na webu Azure Portal.

Následující příkaz vytvoří virtuální ho virtuálního virtuálního virtuálního virtuálního pracovního místa z image tržiště s ověřováním ssh:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

Virtuální počítače můžete také vytvořit na základě vzorců nastavením parametru **typu image** na **vzorec**. Pokud potřebujete vybrat konkrétní virtuální síť pro váš virtuální počítač, použijte parametry **názvu virtuální sítě** a **podsítě.** Další informace naleznete [v tématu az lab vm create](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Ověřte, že virtuální ho disponibilní.
Pomocí `az lab vm show` příkazu ověřte, že virtuální hod je k dispozici před spuštěním a připojit se k němu. 

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
Následující příklad příkazu spustí virtuální hod:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Připojení k virtuálnímu počítači: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) nebo [vzdálená plocha](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Aktualizace virtuálního počítače
Následující ukázkový příkaz aplikuje artefakty na virtuální hod:

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

### <a name="list-artifacts-available-in-the-lab"></a>Seznam artefaktů dostupných v laboratoři

Chcete-li seznam artefaktů dostupných ve virtuálním provozu v testovacím prostředí, spusťte následující příkazy.

**Cloud Shell - PowerShell**: všimněte\`si použití backtick ( ) před $ v $expand (tj. '$expand):

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**Cloud Shell - Bash**: všimněte\\si použití lomítko ( ) znak před $ v příkazu. 

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(\$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

Ukázkový výstup: 

```json
[
  {
    "artifactId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DevTestLab/labs/<lab name>/artifactSources/public repo/artifacts/windows-7zip",
    "status": "Succeeded"
  }
]
```

## <a name="stop-and-delete-the-virtual-machine"></a>Zastavení a odstranění virtuálního počítače    
Následující ukázkový příkaz zastaví virtuální hod.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Odstraňte virtuální hod.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Další kroky
Podívejte se na následující obsah: [Dokumentace k nastavení chování Azure pro Azure DevTest Labs](/cli/azure/lab?view=azure-cli-latest). 
