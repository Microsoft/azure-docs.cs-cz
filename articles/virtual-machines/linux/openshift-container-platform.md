---
title: Nasazení OpenShift Container Platform v Azure | Dokumentace Microsoftu
description: Nasazení OpenShift Container Platform v Azure.
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
ms.openlocfilehash: 48b6287fef673c5f335531b6f230993969fc9e1c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996328"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Nasazení OpenShift Container Platform v Azure

Pro nasazení OpenShift Container Platform v Azure můžete použít některou z několika metod:

- Můžete ručně nasadit komponenty potřebnou infrastrukturu Azure a pak postupujte podle OpenShift Container Platform [dokumentaci](https://docs.openshift.com/container-platform/3.10/welcome/index.html).
- Můžete také použít existující [šablony Resource Manageru](https://github.com/Microsoft/openshift-container-platform/) , která zjednodušuje nasazení OpenShift Container Platform clusteru.
- Další možností je použít [nabídky Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Pro všechny možnosti se vyžaduje předplatné Red Hat. Během nasazování instance Red Hat Enterprise Linux je zaregistrované u předplatného Red Hat a připojený k ID fondu, který obsahuje oprávnění pro OpenShift Container Platform.
Ujistěte se, že máte platný Red Hat předplatné správce (RHSM) uživatelské jméno, heslo a ID fondu. Tyto informace můžete ověřit tak, že přihlášení k https://access.redhat.com.

## <a name="deploy-by-using-the-openshift-container-platform-resource-manager-template"></a>Nasazení pomocí šablony OpenShift Container Platform Resource Manageru

Nasazení pomocí šablony Resource Manageru, použijte soubor parametrů slouží k poskytování vstupní parametry. Chcete-li přizpůsobit kteroukoliv z položky nasazení, které nejsou pokryty pomocí vstupní parametry, rozvětvit úložiště GitHub se vzorovými a změnit odpovídající položky.

Některé běžné možnosti vlastního nastavení zahrnují, ale nejsou omezené na:

- Virtuální síť CIDR (proměnné v azuredeploy.json)
- Velikost virtuálního počítače bastionu (proměnné v azuredeploy.json)
- Zásady vytváření názvů (proměnné v azuredeploy.json)
- OpenShift specifika clusteru, se mění prostřednictvím souboru hosts (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Konfigurace souboru parametrů

Použití `appId` hodnotu z objektu služby, který jste vytvořili dříve pro `aadClientId` parametru. 

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
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "true"
        },
        "enableCockpit": {
            "value": "false"
        },
        "rhsmUsernamePasswordOrActivationKey": {
            "value": "usernamepassword"
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
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

Nahraďte položky uzavřený v závorkách s konkrétní informace.

### <a name="deploy-by-using-azure-cli"></a>Nasazení pomocí rozhraní příkazového řádku Azure

> [!NOTE] 
> Následující příkaz vyžaduje Azure CLI.8 nebo novější. Verzi rozhraní příkazového řádku pomocí můžete ověřit `az --version` příkazu. Pokud chcete aktualizovat verzi rozhraní příkazového řádku, naleznete v tématu [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

Následující příklad nasadí do skupinu prostředků myResourceGroup, s názvem nasazení myOpenShiftCluster OpenShift cluster a všechny související prostředky. Šablona se odkazuje přímo z úložiště GitHub se vzorovými a místní parametrů, které se používá soubor s názvem souboru azuredeploy.parameters.json.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Nasazení trvá aspoň 30 minut v závislosti na celkový počet nasazených uzlů. Adresa URL konzoly OpenShift a název DNS hlavního vytiskne OpenShift do terminálu po dokončení nasazení.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="deploy-by-using-the-openshift-container-platform-azure-marketplace-offer"></a>Nasazení pomocí nabídky OpenShift Container Platform Azure Marketplace

Nejjednodušší způsob, jak nasadit OpenShift Container Platform v Azure je použít [nabídky Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Toto je nejjednodušší možnost, ale je také má omezené možnosti přizpůsobení. Nabídka zahrnuje tři možnosti konfigurace:

- **Malé**: nasadí cluster bez vysoké dostupnosti (HA) s jeden hlavní uzel, infrastruktury uzly, dva uzly aplikaci a jeden bastion uzlu. Všechny uzly jsou standardní velikosti DS2v2 virtuálních počítačů. Tento cluster vyžaduje 10 Celkový počet jader a je ideální pro méně rozsáhlé otestování.
- **Střední**: nasadí cluster vysokou dostupnost pomocí tří hlavních uzlů, dva infrastruktury uzly, čtyři uzly aplikace a bastionu jeden uzel. Všechny uzly s výjimkou uzlu bastionu jsou standardní velikosti DS3v2 virtuálních počítačů. Uzel bastionu je standardní DS2v2. Tento cluster vyžaduje 38 jader.
- **Velké**: nasadí cluster vysokou dostupnost pomocí tří hlavních uzlů, dva infrastruktury uzly, šesti aplikace uzlů a uzlů jeden bastionu. Hlavní server a infrastruktury uzly jsou standardní velikosti virtuálních počítačů DS3v2. Uzly aplikace jsou standardní velikosti virtuálních počítačů DS4v2 a uzel bastionu je standardní DS2v2. Tento cluster vyžaduje 70 jader.

Konfigurace Azure Cloud Solution Provider je nepovinné pro cluster střední a velké velikosti. Velikost malý cluster neuděluje možnost pro konfiguraci Azure Cloud Solution Provider.

## <a name="connect-to-the-openshift-cluster"></a>Připojte se ke clusteru Openshiftu

Až se nasazení dokončí, připojení ke konzole OpenShift v prohlížeči pomocí `OpenShift Console Uri`. Alternativně můžete připojit k hlavnímu serveru OpenShift pomocí následujícího příkazu:

```bash
$ ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Použití [odstranění skupiny az](/cli/azure/group#az_group_delete) příkazu k odebrání skupiny prostředků, clusteru OpenShift a všechny související prostředky, pokud jste už nepotřebujete.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

- [Úlohy po nasazení](./openshift-post-deployment.md)
- [Řešení potíží s nasazení OpenShift v Azure](./openshift-troubleshooting.md)
- [Začínáme s OpenShift Container Platform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
