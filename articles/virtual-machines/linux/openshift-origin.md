---
title: Nasazení OKD v Azure | Dokumentace Microsoftu
description: Nasazení OKD v Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
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
ms.openlocfilehash: 75a02e61adf3e5477b9945afc778e867d5d9c88c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958131"
---
# <a name="deploy-okd-in-azure"></a>Nasazení OKD v Azure

Můžete použít jednu ze dvou způsobů nasazení OKD (dříve OpenShift Origin) v Azure:

- Můžete ručně nasadit všechny součásti potřebné infrastruktury Azure a postupujte podle OKD [dokumentaci](https://docs.okd.io/3.10/welcome/index.html).
- Můžete také použít existující [šablony Resource Manageru](https://github.com/Microsoft/openshift-origin) , která zjednodušuje nasazení OKD clusteru.

## <a name="deploy-by-using-the-okd-template"></a>Nasazení pomocí šablony OKD

Použití `appId` hodnotu z instančního objektu, který jste vytvořili dříve pro `aadClientId` parametru.

Následující příklad vytvoří soubor parametrů s názvem azuredeploy.parameters.json se všechny vstupy.

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
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
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

### <a name="deploy-by-using-azure-cli"></a>Nasazení pomocí rozhraní příkazového řádku Azure


> [!NOTE] 
> Následující příkaz vyžaduje Azure CLI.8 nebo novější. Verzi rozhraní příkazového řádku pomocí můžete ověřit `az --version` příkazu. Pokud chcete aktualizovat verzi rozhraní příkazového řádku, naleznete v tématu [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Následující příklad nasadí do skupinu prostředků myResourceGroup, s názvem nasazení myOpenShiftCluster OKD cluster a všechny související prostředky. Šablona se odkazuje přímo z úložiště GitHub s použitím místní parametry souboru s názvem azuredeploy.parameters.json.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Nasazení trvá aspoň 25 minut na dokončení, v závislosti na celkový počet nasazených uzlů. Adresa URL konzoly OKD a název DNS hlavního vytiskne OpenShift do terminálu po dokončení nasazení.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="connect-to-the-okd-cluster"></a>Připojte se ke clusteru OKD

Až se nasazení dokončí, připojení ke konzole OKD v prohlížeči pomocí `OpenShift Console Uri`. Alternativně můžete připojit k hlavnímu serveru OKD pomocí následujícího příkazu:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Použití [odstranění skupiny az](/cli/azure/group#az_group_delete) příkazu k odebrání skupiny prostředků, clusteru OpenShift a všechny související prostředky, pokud jste už nepotřebujete.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

- [Úlohy po nasazení](./openshift-post-deployment.md)
- [Řešení potíží s nasazení Openshiftu](./openshift-troubleshooting.md)
- [Začínáme s OKD](https://docs.okd.io/latest/getting_started/index.html)
