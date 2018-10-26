---
title: Nasazení OpenShift Container Platform v Azure | Dokumentace Microsoftu
description: Nasazení OpenShift Container Platform v Azure.
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
ms.openlocfilehash: 21eebb6c27a83b939f321d38026da7d4c39b7071
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085862"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Nasazení OpenShift Container Platform v Azure

Pro nasazení OpenShift Container Platform v Azure můžete použít některou z několika metod:

- Můžete ručně nasadit komponenty potřebnou infrastrukturu Azure a pak postupujte podle [dokumentaci OpenShift Container Platform](https://docs.openshift.com/container-platform).
- Můžete také použít existující [šablony Resource Manageru](https://github.com/Microsoft/openshift-container-platform/) , která zjednodušuje nasazení OpenShift Container Platform clusteru.
- Další možností je použít [nabídky Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Pro všechny možnosti se vyžaduje předplatné Red Hat. Během nasazování instance Red Hat Enterprise Linux je zaregistrované u předplatného Red Hat a připojený k ID fondu, který obsahuje oprávnění pro OpenShift Container Platform.
Ujistěte se, že máte platné uživatelské jméno, heslo a ID fondu Red Hat předplatné správce (RHSM) Můžete použít aktivační klíč, ID organizace a ID fondu. Tyto informace můžete ověřit tak, že přihlášení k https://access.redhat.com.

## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>Nasazení pomocí šablony OpenShift Container Platform Resource Manageru

Pokud chcete nasadit, pomocí šablony Resource Manageru, slouží k poskytování vstupní parametry souboru parametrů. Chcete-li dále přizpůsobit nasazení, rozvětvit úložiště GitHub se vzorovými a změňte příslušné položky.

Některé běžné možnosti vlastního nastavení zahrnují, ale nejsou omezené na:

- Velikost virtuálního počítače bastionu (proměnné v azuredeploy.json)
- Zásady vytváření názvů (proměnné v azuredeploy.json)
- OpenShift specifika clusteru, se mění prostřednictvím souboru hosts (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Konfigurace souboru parametrů

[Šablony OpenShift Container Platform](https://github.com/Microsoft/openshift-container-platform) má několik větví, které jsou k dispozici pro různé verze nástroje OpenShift Container Platform.  Podle svých potřeb, můžete aplikaci nasadit přímo z úložiště nebo můžete vytvořit fork úložiště a provádět vlastní změny šablony nebo skripty před nasazením.

Použití `appId` hodnotu z objektu služby, který jste vytvořili dříve pro `aadClientId` parametru.

Následující příklad ukazuje soubor parametrů s názvem azuredeploy.parameters.json se všechny vstupy.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
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
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "{RHSM Username}"
        },
        "rhsmPasswordOrActivationKey": {
            "value": "{RHSM Password}"
        },
        "rhsmPoolId": {
            "value": "{Pool ID}"
        },
        "rhsmBrokerPoolId": {
            "value": "{Pool ID}"
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
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "routing.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "openshiftvnet"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "mastersubnet"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "infrasubnet"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "nodesubnet"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/masterinfrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.201"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        },
        "proxySettings": {
            "value": "none"
        },
        "httpProxyEntry": {
            "value": "none"
        },
        "httpsProxyEntry": {
            "value": "none"
        },
        "noProxyEntry": {
            "value": "none"
        }
    }
}
```

Nahraďte parametry konkrétní informace.

Dvě různé verze může mít různé parametry, proto ověřte potřebné parametry pro větev, kterou používáte.

### <a name="deploy-using-azure-cli"></a>Nasazení pomocí rozhraní příkazového řádku Azure

> [!NOTE] 
> Následující příkaz vyžaduje Azure CLI 2.0.8 nebo novější. Verzi rozhraní příkazového řádku pomocí můžete ověřit `az --version` příkazu. Pokud chcete aktualizovat verzi rozhraní příkazového řádku, naleznete v tématu [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

Následující příklad nasadí do skupiny prostředků s názvem openshiftrg, s názvem nasazení myOpenShiftCluster OpenShift cluster a všechny související prostředky. Šablona se odkazuje přímo z úložiště GitHub se vzorovými a místní parametrů, které se používá soubor s názvem souboru azuredeploy.parameters.json.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Nasazení trvá aspoň 30 minut na dokončení, na základě celkového počtu nasazených uzlů a možností, které nakonfigurujete. Plně kvalifikovaný název domény Bastionu DNS a adresu URL konzoly OpenShift vytiskne po dokončení nasazení do terminálu.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Pokud nechcete vytížit příkazový řádek čeká na dokončení, přidat nasazení `--no-wait` jako jednu z možností pro nasazení skupiny. Výstup z nasazení můžete získat z webu Azure portal v části nasazení pro skupinu prostředků.
 
## <a name="deploy-using-the-openshift-container-platform-azure-marketplace-offer"></a>Nasazení pomocí nástroje OpenShift Container Platform Azure Marketplace nabídku

Nejjednodušší způsob, jak nasadit OpenShift Container Platform v Azure je použít [nabídky Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Toto je nejjednodušší možnost, ale je také má omezené možnosti přizpůsobení. Nabídka Marketplace obsahuje následující možnosti konfigurace:

- **Hlavní uzly**: hlavní uzly tři (3) se dají konfigurovat typ instance.
- **Uzly infra**: tři (3) Infra uzly s typem instance konfigurovatelné.
- **Uzly**: počet uzlů se dají konfigurovat (2 až 9) a typu instance.
- **Typ disku**: spravované disky se používají.
- **Sítě**: podpora pro nové nebo existující síti, stejně jako vlastní rozsah CIDR.
- **CNS**: CNS je možné povolit.
- **Metriky**: metriky je možné povolit.
- **Protokolování**: je možné povolit protokolování.
- **Zprostředkovatel služby Azure Cloud**: je možné povolit.

## <a name="connect-to-the-openshift-cluster"></a>Připojte se ke clusteru Openshiftu

Až se nasazení dokončí, získání připojení z výstupní sekce nasazení. Připojení ke konzole OpenShift v prohlížeči pomocí `OpenShift Console URL`. Můžete také získat přístup přes SSH k Bastion host. Tady je příklad, kde je uživatelské jméno správce clusteradmin a bastionu veřejné IP adresy DNS plně kvalifikovaný název domény je bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Použití [odstranění skupiny az](/cli/azure/group#az_group_delete) příkazu k odebrání skupiny prostředků, clusteru OpenShift a všechny související prostředky, pokud jste už nepotřebujete.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Další postup

- [Úlohy po nasazení](./openshift-post-deployment.md)
- [Řešení potíží s nasazení OpenShift v Azure](./openshift-troubleshooting.md)
- [Začínáme s OpenShift Container Platform](https://docs.openshift.com/container-platform)

### <a name="documentation-contributors"></a>Přispěvatelů dokumentace

Děkujeme vám Vincenc výkon (vincepower), Alfred Sin (asinn826) za svoje příspěvky na průběžná této dokumentace.