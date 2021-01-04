---
title: Nasazení OKD v Azure
description: Nasaďte OKD v Azure.
author: haroldwongms
manager: joraio
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/15/2019
ms.author: haroldw
ms.openlocfilehash: ed74d0829a8372ea8391fcd331eef2ef3af3dae0
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704907"
---
# <a name="deploy-okd-in-azure"></a>Nasazení OKD v Azure

Můžete použít jeden ze dvou způsobů, jak nasadit OKD (dříve OpenShift Origin) v Azure:

- Můžete ručně nasadit všechny nezbytné součásti infrastruktury Azure a pak postupovat podle pokynů v [dokumentaci k OKD](https://docs.okd.io).
- Můžete také použít stávající [šablonu správce prostředků](https://github.com/Microsoft/openshift-origin) , která zjednodušuje nasazení clusteru OKD.

## <a name="deploy-using-the-okd-template"></a>Nasazení pomocí šablony OKD

Chcete-li nasadit pomocí šablony Správce prostředků, použijte soubor parametrů k zadání vstupních parametrů. Pro další přizpůsobení nasazení, rozvětvete úložiště GitHub a změňte příslušné položky.

Mezi běžné možnosti přizpůsobení patří, ale nejsou omezené na:

- Velikost virtuálního počítače bastionu (proměnná v azuredeploy.jszapnutá)
- Zásady vytváření názvů (proměnné v azuredeploy.jszapnuté)
- OpenShift konkrétní clustery, upravené prostřednictvím souboru Hosts (deployOpenShift.sh)

[Šablona OKD](https://github.com/Microsoft/openshift-origin) má k dispozici více větví pro různé verze nástroje OKD.  Na základě vašich potřeb můžete nasadit přímo z úložiště nebo můžete rozvětvit úložiště a před nasazením provést vlastní změny.

Použijte `appId` hodnotu z instančního objektu, který jste vytvořili dříve pro `aadClientId` parametr.

Následuje příklad souboru parametrů s názvem azuredeploy.parameters.jsse všemi požadovanými vstupy.

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

Různé verze mohou mít různé parametry, proto ověřte, zda jsou potřebné parametry pro větev, kterou používáte.

### <a name="deploy-using-azure-cli"></a>Nasazení s využitím rozhraní příkazového řádku Azure


> [!NOTE] 
> Následující příkaz vyžaduje rozhraní příkazového řádku Azure CLI 2.0.8 nebo novější. Verzi rozhraní příkazového řádku můžete ověřit pomocí `az --version` příkazu. Pokud chcete aktualizovat verzi rozhraní příkazového řádku, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Následující příklad nasadí cluster OKD a všechny související prostředky do skupiny prostředků s názvem openshiftrg s názvem nasazení myOpenShiftCluster. Na šablonu se odkazuje přímo z úložiště GitHub při použití souboru místních parametrů s názvem azuredeploy.parameters.jsv.

```azurecli 
az deployment group create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Dokončení nasazení trvá nejméně 30 minut, a to na základě celkového počtu nasazených uzlů. Adresa URL konzoly OpenShift a název DNS hlavního serveru OpenShift se po dokončení nasazení vytisknou do terminálu. Alternativně můžete zobrazit část s výstupy nasazení z Azure Portal.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Pokud nechcete vytvořit vazbu na příkazový řádek, který čeká na dokončení nasazení, přidejte `--no-wait` jako jednu z možností pro nasazení skupiny. Výstup z nasazení lze načíst z Azure Portal v části nasazení pro skupinu prostředků.

## <a name="connect-to-the-okd-cluster"></a>Připojení ke clusteru OKD

Až se nasazení dokončí, připojte se ke konzole OpenShift s prohlížečem pomocí `OpenShift Console Url` . Alternativně můžete k hlavnímu serveru OKD použít SSH. Následuje příklad, který používá výstup z nasazení:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pomocí příkazu [AZ Group Delete](/cli/azure/group) odeberte skupinu prostředků, cluster OpenShift a všechny související prostředky, pokud už je nepotřebujete.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Další kroky

- [Úkoly po nasazení](./openshift-container-platform-3x-post-deployment.md)
- [Řešení potíží s nasazením OpenShift](./openshift-container-platform-3x-troubleshooting.md)
- [Začínáme s OKD](https://docs.okd.io)
