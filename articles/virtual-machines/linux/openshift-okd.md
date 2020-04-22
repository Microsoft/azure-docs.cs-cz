---
title: Nasazení OKD v Azure
description: Nasaďte OKD v Azure.
author: haroldwongms
manager: joraio
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/15/2019
ms.author: haroldw
ms.openlocfilehash: d7d251370aefdfadc0b77a67f6dad1be2dcb9e9a
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759447"
---
# <a name="deploy-okd-in-azure"></a>Nasazení OKD v Azure

Můžete použít jeden ze dvou způsobů nasazení OKD (dříve OpenShift Origin) v Azure:

- Můžete ručně nasadit všechny potřebné součásti infrastruktury Azure a potom postupujte podle [dokumentace OKD](https://docs.okd.io).
- Můžete také použít existující [šablonu Správce prostředků,](https://github.com/Microsoft/openshift-origin) která zjednodušuje nasazení clusteru OKD.

## <a name="deploy-using-the-okd-template"></a>Nasazení pomocí šablony OKD

Chcete-li nasadit pomocí šablony Správce prostředků, použijte soubor parametrů k zadání vstupních parametrů. Chcete-li dále přizpůsobit nasazení, rozviněte úložiště GitHub a změňte příslušné položky.

Některé běžné možnosti vlastního nastavení zahrnují, ale nejsou omezeny na:

- Velikost virtuálního počítače bašty (proměnná v azuredeploy.json)
- Konvence pojmenování (proměnné v azuredeploy.json)
- Specifika clusteru OpenShift, upravená prostřednictvím souboru hosts (deployOpenShift.sh)

[Šablona OKD](https://github.com/Microsoft/openshift-origin) má k dispozici více větví pro různé verze OKD.  Na základě vašich potřeb můžete nasadit přímo z repo nebo můžete rozklopit repo a provést vlastní změny před nasazením.

Použijte `appId` hodnotu z instančního objektu, který jste vytvořili `aadClientId` dříve pro parametr.

Následuje příklad souboru parametrů s názvem azuredeploy.parameters.json se všemi požadovanými vstupy.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

Nahraďte parametry konkrétními informacemi.

Různé verze mohou mít různé parametry, proto ověřte potřebné parametry pro pobočku, kterou používáte.

### <a name="deploy-using-azure-cli"></a>Nasazení s využitím rozhraní příkazového řádku Azure


> [!NOTE] 
> Následující příkaz vyžaduje Azure CLI 2.0.8 nebo novější. Můžete ověřit verzi rozhraní příkazového příkazu pomocí příkazu. `az --version` Pokud chcete aktualizovat verzi příkazového příkazového příkazu, [přečtěte si informace o instalaci příkazového příkazového příkazu k webu Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Následující příklad nasazuje cluster OKD a všechny související prostředky do skupiny prostředků s názvem openshiftrg s názvem deployment name myOpenShiftCluster. Šablona je odkazována přímo z úložiště GitHub při použití souboru místních parametrů s názvem azuredeploy.parameters.json.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Nasazení trvá nejméně 30 minut na dokončení, na základě celkového počtu nasazených uzlů. Adresa URL konzoly OpenShift a název DNS předlohy OpenShift se po dokončení nasazení vytisknou do terminálu. Případně můžete zobrazit výstupní část nasazení z webu Azure Portal.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Pokud nechcete svázat příkazový řádek čekání na dokončení nasazení, přidejte `--no-wait` jako jednu z možností pro nasazení skupiny. Výstup z nasazení lze načíst z portálu Azure v části nasazení pro skupinu prostředků.

## <a name="connect-to-the-okd-cluster"></a>Připojení ke clusteru OKD

Po dokončení nasazení se připojte ke konzoli OpenShift `OpenShift Console Url`pomocí prohlížeče pomocí . Případně můžete SSH na master OKD. Následuje příklad, který používá výstup z nasazení:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pomocí příkazu [delete skupiny az](/cli/azure/group) odeberte skupinu prostředků, cluster OpenShift a všechny související prostředky, pokud už nejsou potřeba.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Další kroky

- [Úlohy po nasazení](./openshift-container-platform-3x-post-deployment.md)
- [Poradce při potížích s nasazením OpenShift](./openshift-container-platform-3x-troubleshooting.md)
- [Začínáme s OKD](https://docs.okd.io)
