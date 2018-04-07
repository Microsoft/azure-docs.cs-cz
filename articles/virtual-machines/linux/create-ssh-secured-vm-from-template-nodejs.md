---
title: Vytvoření virtuálního počítače s Linuxem pomocí šablony Azure s Azure CLI 1.0 | Microsoft Docs
description: Vytvoření virtuálního počítače s Linuxem v Azure pomocí Azure CLI 1.0 a šablonu Azure Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: v-livech
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 620adcba9e136aa56e7a4867196ce5b9249b6072
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-create-a-linux-vm-using-the-azure-cli-10-an-azure-resource-manager-template"></a>Postup vytvoření virtuálního počítače s Linuxem pomocí Azure CLI 1.0 šablonu Azure Resource Manager
Tento článek ukazuje, jak rychle nasadit virtuální počítač s Linuxem pomocí Azure CLI 1.0 a šablonu Azure Resource Manager. Tento článek vyžaduje:

* účet Azure ([získejte bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/))
* [Azure CLI 1.0](../../cli-install-nodejs.md) přihlášení `azure login`.
* Rozhraní příkazového řádku Azure *musí být v* režimu Azure Resource Manager`azure config mode arm`.

Šablonu virtuálního počítače s Linuxem můžete rychle nasadit také pomocí webu [Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions-to-complete-the-task"></a>Verze rozhraní příkazového řádku pro dokončení úlohy
K dokončení úlohy můžete využít jednu z následujících verzí rozhraní příkazového řádku:

- [Azure CLI 1.0](#quick-command-summary) – naše rozhraní příkazového řádku pro classic a resource správu modelech nasazení (v tomto článku)
- [Azure CLI 2.0](create-ssh-secured-vm-from-template.md) – naše rozhraní příkazového řádku nové generace pro model nasazení správy prostředků

## <a name="quick-command-summary"></a>Rychlý přehled příkazu
```azurecli
azure group create \
    -n myResourceGroup \
    -l westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## <a name="detailed-walkthrough"></a>Podrobný postup
Šablony umožňují vytvořit virtuální počítače na platformě Azure s nastavením, které si pak při spouštění upravíte, například uživatelská jména a názvy hostitelů. Pro tento článek spouštíme šablonu Azure využívající virtuální počítač Ubuntu společně se skupinu zabezpečení sítě (NSG) s portem 22 otevřeným pro SSH.

Šablony Azure Resource Manageru jsou soubory JSON, které jde použít k jednoduchým jednorázovým úlohám, jako je spuštění virtuálního počítače Ubuntu VM pro účely tohoto článku.  Šablony Azure jde také použít k vytvoření složitých konfigurací Azure pro celé prostředí, jako je sada pro nasazení testovacího, vývojového nebo produkčního prostředí.

## <a name="create-the-linux-vm"></a>Vytvoření virtuálního počítače s Linuxem
Následující příklad kódu ukazuje, jak voláním příkazu `azure group create` vytvořit skupinu prostředků a zároveň nasadit virtuální počítač s Linuxem se zabezpečením SSH pomocí [této šablony Azure Resource Manageru](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Je třeba myslet na to, že v příkladu musíte použít názvy, které jsou jedinečné pro vaše prostředí. Tento příklad používá *myResourceGroup* jako název skupiny prostředků a *Můjvp* jako název virtuálního počítače.

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

Výstup by měl vypadat jako následující výstupní blok:

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== myAdminUser@myVM
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Tento příklad nasazuje virtuální počítač pomocí parametru `--template-uri`.  Můžete taky šablonu stáhnout nebo ji vytvořit místně a předat ji pomocí parametru `--template-file`cesta k souboru šablony. Rozhraní Azure CLI vás požádá o parametry vyžadované šablonou.

## <a name="next-steps"></a>Další postup
V [galerii šablon](https://azure.microsoft.com/documentation/templates/) se podívejte, jaké aplikační architektury nasadit jako další.

