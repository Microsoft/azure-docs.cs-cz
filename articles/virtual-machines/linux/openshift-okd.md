---
title: Nasazení OKD v Azure | Dokumentace Microsoftu
description: Nasazení OKD v Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: 571190324c5a0844624bd8a838cd103317fb53ca
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55729145"
---
# <a name="deploy-okd-in-azure"></a>Nasazení OKD v Azure

Můžete použít jednu ze dvou způsobů nasazení OKD (dříve OpenShift Origin) v Azure:

- Můžete ručně nasadit všechny součásti potřebné infrastruktury Azure a potom postupujte podle [OKD dokumentaci](https://docs.okd.io).
- Můžete také použít existující [šablony Resource Manageru](https://github.com/Microsoft/openshift-origin) , která zjednodušuje nasazení OKD clusteru.

## <a name="deploy-using-the-okd-template"></a>Nasazení pomocí šablony OKD

Pokud chcete nasadit, pomocí šablony Resource Manageru, slouží k poskytování vstupní parametry souboru parametrů. Chcete-li dále přizpůsobit nasazení, rozvětvit úložiště GitHub se vzorovými a změňte příslušné položky.

Některé běžné možnosti vlastního nastavení zahrnují, ale nejsou omezené na:

- Velikost virtuálního počítače bastionu (proměnné v azuredeploy.json)
- Zásady vytváření názvů (proměnné v azuredeploy.json)
- OpenShift specifika clusteru, se mění prostřednictvím souboru hosts (deployOpenShift.sh)

[OKD šablony](https://github.com/Microsoft/openshift-origin) má k dispozici pro různé verze OKD několika větví.  Podle svých potřeb, můžete aplikaci nasadit přímo z úložiště nebo může vytvořit fork úložiště a vlastní změny před nasazením.

Použití `appId` hodnotu z instančního objektu, který jste vytvořili dříve pro `aadClientId` parametru.

Následuje příklad souboru parametrů s názvem azuredeploy.parameters.json se všechny vstupy.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "storageKind": {
            "value": "managed"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

Nahraďte parametry konkrétní informace.

Dvě různé verze může mít různé parametry proto zkontrolujte, zda potřebné parametry pro větev používáte.

### <a name="deploy-using-azure-cli"></a>Nasazení pomocí rozhraní příkazového řádku Azure


> [!NOTE] 
> Následující příkaz vyžaduje Azure CLI 2.0.8 nebo novější. Verzi rozhraní příkazového řádku pomocí můžete ověřit `az --version` příkazu. Pokud chcete aktualizovat verzi rozhraní příkazového řádku, naleznete v tématu [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Následující příklad nasadí do skupiny prostředků s názvem openshiftrg, s názvem nasazení myOpenShiftCluster OKD cluster a všechny související prostředky. Šablona se odkazuje přímo z úložiště GitHub se při použití parametrů místní soubor s názvem azuredeploy.parameters.json.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Nasazení trvá aspoň 30 minut na dokončení, na základě celkového počtu nasazených uzlů. Adresa URL konzoly OpenShift a název DNS hlavního vytiskne OpenShift do terminálu po dokončení nasazení. Alternativně můžete zobrazit část Outputs následujícím nasazení z webu Azure portal.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Pokud nechcete vytížit příkazový řádek čeká na dokončení, přidat nasazení `--no-wait` jako jednu z možností pro nasazení skupiny. Výstup z nasazení můžete získat z webu Azure portal v části nasazení pro skupinu prostředků.

## <a name="connect-to-the-okd-cluster"></a>Připojte se ke clusteru OKD

Až se nasazení dokončí, připojení ke konzole OpenShift s používáním vašeho prohlížeče `OpenShift Console Url`. Můžete také získat přístup přes SSH k hlavnímu serveru OKD. Tady je příklad, který používá výstup z nasazení:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Použití [odstranění skupiny az](/cli/azure/group) příkazu k odebrání skupiny prostředků, clusteru OpenShift a všechny související prostředky, pokud jste už nepotřebujete.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Další postup

- [Úlohy po nasazení](./openshift-post-deployment.md)
- [Řešení potíží s nasazení Openshiftu](./openshift-troubleshooting.md)
- [Začínáme s OKD](https://docs.okd.io)
