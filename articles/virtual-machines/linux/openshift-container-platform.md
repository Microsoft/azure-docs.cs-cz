---
title: Nasazení OpenShift kontejneru platformy v Azure | Microsoft Docs
description: Nasaďte OpenShift kontejneru platformy v Azure.
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
ms.openlocfilehash: f1ba6a3d3b9e576d513b55beac4e9365102433e9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
ms.locfileid: "29125737"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Nasazení OpenShift kontejneru platformy v Azure

Můžete použít některou z několika metod k nasazení kontejneru platformy OpenShift v Azure:

- Můžete ručně nasadit komponenty potřebnou infrastrukturu Azure a pak postupujte podle platformy kontejneru OpenShift [dokumentaci](https://docs.openshift.com/container-platform/3.6/welcome/index.html).
- Můžete také použít existující [šablony Resource Manageru](https://github.com/Microsoft/openshift-container-platform/) , zjednodušuje nasazení clusteru OpenShift kontejneru platformy.
- Další možností je použít [nabídka Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Pro všechny možnosti vyžaduje Red Hat se předplatné. Během nasazení instance Red Hat Enterprise Linux je zaregistrován k předplatnému Red Hat a připojené k ID fondu, který obsahuje oprávnění pro platformu OpenShift kontejneru.
Ujistěte se, zda máte platné uživatelské jméno Red Hat odběr Manager (RHSM), heslo a ID fondu. Tyto informace můžete ověřit, po přihlášení k https://access.redhat.com.

## <a name="deploy-by-using-the-openshift-container-platform-resource-manager-template"></a>Nasazení pomocí šablony správce prostředků platformy OpenShift kontejneru

Nasazení pomocí šablony Resource Manageru, použijte soubor parametrů k poskytování vstupní parametry. Chcete-li přizpůsobit některou z položek nasazení, které nejsou zahrnuty pomocí vstupní parametry, rozvětvit úložiště GitHub a změňte odpovídající položky.

Některé běžné možnosti přizpůsobení zahrnovat, ale nejsou omezeny na:

- Virtuální síť CIDR (proměnné v azuredeploy.json)
- Velikost virtuálního počítače bastionu (proměnné v azuredeploy.json)
- Zásady vytváření názvů (proměnné v azuredeploy.json)
- Specifika OpenShift clusteru, upravit pomocí souboru hosts (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Konfigurace souboru parametrů

Použití `appId` hodnotu z objektu služby, který jste dříve vytvořili pro `aadClientId` parametr. 

Následující příklad vytvoří soubor parametrů s názvem azuredeploy.parameters.json se všechny požadované vstupy.

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

Nahraďte položky v závorkách s konkrétní informace.

### <a name="deploy-by-using-azure-cli"></a>Nasazení pomocí rozhraní příkazového řádku Azure

> [!NOTE] 
> Tento příkaz vyžaduje rozhraní příkazového řádku Azure 2.0.8 nebo novější. Verze rozhraní příkazového řádku s můžete ověřit `az --version` příkaz. K aktualizaci verze rozhraní příkazového řádku, najdete v části [nainstalovat Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

Následující příklad nasadí do skupiny prostředků s názvem myResourceGroup, s názvem nasazení myOpenShiftCluster OpenShift clusteru a všechny související prostředky. Šablona se odkazuje přímo z úložiště GitHub a místní parametrů, které se používá soubor s názvem azuredeploy.parameters.json souboru.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Nasazení trvá nejméně 30 minut v závislosti na celkový počet uzlů, které jsou nasazeny. Adresa URL konzoly OpenShift a název DNS hlavní výtisků OpenShift na terminálu po dokončení nasazení.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="deploy-by-using-the-openshift-container-platform-azure-marketplace-offer"></a>Nasazení pomocí nabídku OpenShift kontejneru platformy Azure Marketplace

Nejjednodušší způsob, jak nasadit OpenShift kontejneru platformy do Azure se má používat [nabídka Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Toto je nejjednodušší možnost, ale také má omezený schopnosti přizpůsobení. Nabídka zahrnuje tři možnosti konfigurace:

- **Malé**: nasadí cluster bez vysoké dostupnosti (HA) s jeden hlavní uzel, uzel infrastruktury jeden, dva uzly aplikace a jeden bastionu uzel. Všechny uzly jsou standardní velikosti DS2v2 virtuálních počítačů. Tento cluster vyžaduje 10 Celkový počet jader a je ideální pro méně rozsáhlé otestování.
- **Střední**: nasadí cluster služby HA s tři hlavní uzly, dva uzly infrastruktury, čtyři uzly aplikace a jeden bastionu uzlu. Všechny uzly s výjimkou uzlu bastionu jsou standardní velikosti DS3v2 virtuálních počítačů. Uzel bastionu je standardní DS2v2. Tento cluster vyžaduje 38 jader.
- **Velké**: nasadí cluster služby HA s tři hlavní uzly, dva uzly infrastruktury, šesti uzlů aplikace a jeden bastionu uzlu. Hlavní server a infrastrukturou uzly jsou standardní velikosti DS3v2 virtuálních počítačů. Uzly aplikace jsou standardní velikosti virtuálních počítačů DS4v2 a uzel bastionu je standardní DS2v2. Tento cluster vyžaduje 70 jader.

Konfigurace Azure Cloud Solution Provider je volitelné pro střední a velké clusteru velikosti. Malá velikost není poskytnuta možnost pro konfiguraci Azure Cloud Solution Provider.

## <a name="connect-to-the-openshift-cluster"></a>Připojte se ke clusteru OpenShift

Po dokončení nasazení připojit ke konzole OpenShift s prohlížeči pomocí `OpenShift Console Uri`. Můžete alternativně připojí k hlavní OpenShift pomocí následujícího příkazu:

```bash
$ ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Použití [odstranění skupiny az](/cli/azure/group#az_group_delete) příkaz, který má-li odebrat skupinu prostředků, OpenShift clusteru a všechny související prostředky, když jste už nepotřebují.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

- [Úlohy po nasazení](./openshift-post-deployment.md)
- [Řešení potíží s OpenShift nasazení v Azure](./openshift-troubleshooting.md)
- [Začínáme s platformou OpenShift kontejneru](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
