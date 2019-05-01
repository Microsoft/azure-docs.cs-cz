---
title: Nasazení OpenShift Container Platform v Azure | Dokumentace Microsoftu
description: Nasazení OpenShift Container Platform v Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/18/2019
ms.author: haroldw
ms.openlocfilehash: 296bc42313ef80425004d3c9b43c6792cbaf97f4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718257"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Nasazení OpenShift Container Platform v Azure

Pro nasazení OpenShift Container Platform v Azure můžete použít některou z několika metod:

- Můžete ručně nasadit komponenty potřebnou infrastrukturu Azure a pak postupujte podle [dokumentaci OpenShift Container Platform](https://docs.openshift.com/container-platform).
- Můžete také použít existující [šablony Resource Manageru](https://github.com/Microsoft/openshift-container-platform/) , která zjednodušuje nasazení OpenShift Container Platform clusteru.
- Další možností je použít [nabídky Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Pro všechny možnosti se vyžaduje předplatné Red Hat. Během nasazování instance Red Hat Enterprise Linux je zaregistrované u předplatného Red Hat a připojený k ID fondu, který obsahuje oprávnění pro OpenShift Container Platform.
Ujistěte se, že máte platné uživatelské jméno, heslo a ID fondu Red Hat předplatné správce (RHSM) Můžete použít aktivační klíč, ID organizace a ID fondu. Tyto informace můžete ověřit tak, že přihlášení k https://access.redhat.com.


## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>Nasazení pomocí šablony OpenShift Container Platform Resource Manageru

### <a name="private-clusters"></a>Privátní clusterů

Nasazení privátní clusterů OpenShift vyžaduje více než jen nemají přidružený k nástroji pro vyrovnávání zatížení hlavní (Webová konzola) nebo na veřejnou IP adresu infra nástroj pro vyrovnávání zatížení (směrovače).  Soukromý cluster obecně používá vlastní server DNS (ne výchozí Azure DNS), vlastního názvu domény (například contoso.com) a předdefinované virtuálních sítí.  Pro privátní clustery budete muset předem nakonfigurovat virtuální sítě se všemi příslušné podsítě a nastavení serveru DNS.  Pak pomocí **existingMasterSubnetReference**, **existingInfraSubnetReference**, **existingCnsSubnetReference**, a  **existingNodeSubnetReference** zadat existující podsítě pro použití v clusteru.

Pokud je vybrána privátní hlavní (**masterClusterType**= private), statické privátní IP adresa musí být zadáno pro **masterPrivateClusterIp**.  Tato IP adresa se přiřadí front-endu nástroje pro vyrovnávání zatížení hlavní.  IP adresa musí být v rámci CIDR pro hlavní podsíť a není používáno.  **masterClusterDnsType** musí být nastavena na "vlastní" a k hlavnímu serveru musí být zadaný název DNS pro **masterClusterDns**.  Název DNS, musí být namapovaný na statickou privátní IP adresu a se použije pro přístup ke konzole na hlavní uzly.

Pokud je vybrána směrovače privátního (**routerClusterType**= private), statické privátní IP adresa musí být zadáno pro **routerPrivateClusterIp**.  Tato IP adresa se přiřadí front-endu infra nástroj pro vyrovnávání zatížení.  IP adresa musí být v rámci CIDR pro infra podsítě a nepoužívá.  **routingSubDomainType** musí být nastavena na "vlastní" a zástupný název DNS pro směrování musí být zadaná pro **routingSubDomain**.  

Pokud se vybere privátní hlavních serverů a směrovač privátní, vlastní název domény i pro musí být zadáno **domainName**

Po úspěšném nasazení uzel Bastionu je jediný uzel s veřejnou IP adresu, která můžete ssh do.  I v případě, že řídicí uzly jsou nakonfigurované pro veřejný přístup, že se nezobrazí pro ssh přístup.

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
        "_artifactsLocation": {
            "value": "https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master"
        },
        "location": {
            "value": "eastus"
        },
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
            "value": "changeme"
        },
        "openshiftClusterPrefix": {
            "value": "changeme"
        },
        "minorVersion": {
            "value": "69"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 3
        },
        "nodeInstanceCount": {
            "value": 3
        },
        "cnsInstanceCount": {
            "value": 3
        },
        "osDiskSize": {
            "value": 64
        },
        "dataDiskSize": {
            "value": 64
        },
        "cnsGlusterDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "changeme"
        },
        "enableMetrics": {
            "value": "false"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "changeme"
        },
        "rhsmPoolId": {
            "value": "changeme"
        },
        "rhsmBrokerPoolId": {
            "value": "changeme"
        },
        "sshPublicKey": {
            "value": "GEN-SSH-PUB-KEY"
        },
        "keyVaultSubscriptionId": {
            "value": "255a325e-8276-4ada-af8f-33af5658eb34"
        },
        "keyVaultResourceGroup": {
            "value": "changeme"
        },
        "keyVaultName": {
            "value": "changeme"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "changeme"
        },
        "domainName": {
            "value": "contoso.com"
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
            "value": "apps.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "changeme"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "changeme"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "changeme"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "changeme"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/infrasubnet"
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
            "value": "10.2.0.200"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        }
    }
}
```

Nahraďte parametry konkrétní informace.

Dvě různé verze může mít různé parametry, proto ověřte potřebné parametry pro větev, kterou používáte.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy. Vysvětlení souboru parameters.JSON

| Vlastnost | Popis | Platné možnosti | Výchozí hodnota |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | Adresa URL pro artefakty (json, skripty atd.) |  |  https:\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | Oblasti Azure do nasazení prostředků do |  |  |
| `masterVmSize` | Velikost hlavního virtuálního počítače. Vyberte jednu z povolených velikostí virtuálních počítačů, které jsou uvedené v souboru azuredeploy.json |  | Standard_E2s_v3 |
| `infraVmSize` | Velikost infrastruktura virtuálního počítače. Vyberte jednu z povolených velikostí virtuálních počítačů, které jsou uvedené v souboru azuredeploy.json |  | Standard_D4s_v3 |
| `nodeVmSize` | Velikost uzlu aplikace virtuálního počítače. Vyberte jednu z povolených velikostí virtuálních počítačů, které jsou uvedené v souboru azuredeploy.json |  | Standard_D4s_v3 |
| `cnsVmSize` | Velikost uzlu kontejneru nativní úložiště (CNS) virtuálního počítače. Vyberte jednu z povolených velikostí virtuálních počítačů, které jsou uvedené v souboru azuredeploy.json |  | Standard_E4s_v3 |
| `osImageType` | Image RHEL, který se má použít. defaultgallery: On-Demand; Marketplace: třetích stran obrázku | defaultgallery <br> marketplace | defaultgallery |
| `marketplaceOsImage` | Pokud `osImageType` se webu marketplace, a pak zadejte odpovídající hodnoty pro "vydavatele", "nabízejí", "sku", verze nabídky marketplace. Tento parametr je typu objektu |  |  |
| `storageKind` | Typ úložiště, který se má použít  | Spravované<br> nespravované | Spravované |
| `openshiftClusterPrefix` | Předpona použitá ke konfiguraci názvy hostitelů pro všechny uzly clusteru.  Od 1 do 20 znaků |  | clusteru |
| `minoVersion` | Podverze OpenShift Container Platform 3.11 k nasazení |  | 69 |
| `masterInstanceCount` | Počet hlavních serverů uzlů pro nasazení | 1, 3, 5 | 3 |
| `infraInstanceCount` | Počet infra uzly k nasazení | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Počet uzlů na nasazení | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Číslo CNS uzly k nasazení | 3, 4 | 3 |
| `osDiskSize` | Velikost disku s operačním systémem pro virtuální počítač (v GB) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Velikost datového disku k připojení k uzlům pro Docker svazek (v GB) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Velikost datového disku k připojení k CNS uzly pro použití podle glusterfs (v GB | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Uživatelské jméno správce pro přihlášení operačního systému (VM) a počáteční OpenShift uživatele |  | ocpadmin |
| `enableMetrics` | Zapnutí metrik. Metriky vyžadují více prostředků, proto vyberte správné velikosti infrastruktura virtuálního počítače | true (pravda) <br> false (nepravda) | false (nepravda) |
| `enableLogging` | Povolení protokolování. elasticsearch pod vyžaduje 8 GB paměti RAM proto vyberte správné velikosti infrastruktura virtuálního počítače | true (pravda) <br> false (nepravda) | false (nepravda) |
| `enableCNS` | Povolit nativní kontejneru úložiště | true (pravda) <br> false (nepravda) | false (nepravda) |
| `rhsmUsernameOrOrgId` | Red Hat správce předplatného uživatelské jméno nebo organizace ID |  |  |
| `rhsmPoolId` | ID fondu Red Hat správce předplatného, obsahující své nároky OpenShift pro výpočetní uzly |  |  |
| `rhsmBrokerPoolId` | Red Hat správce předplatného fondu Identifikátor, který obsahuje uzly své nároky OpenShift pro vzory a infrastruktura. Pokud nemáte k dispozici jiný fond ID, zadejte stejné ID fondu jako "rhsmPoolId. |  |
| `sshPublicKey` | Zkopírujte váš veřejný klíč SSH zde |  |  |
| `keyVaultSubscriptionId` | ID předplatného předplatné obsahující služby Key Vault |  |  |
| `keyVaultResourceGroup` | Název skupiny prostředků obsahující trezor klíčů |  |  |
| `keyVaultName` | Název služby Key Vault, které jste vytvořili |  |  |
| `enableAzure` | Povolit poskytovatele cloudu Azure | true (pravda) <br> false (nepravda) | true (pravda) |
| `aadClientId` | Azure ID pro klienta Active Directory také označuje jako ID aplikace instančního objektu pro |  |  |
| `domainName` | Název vlastního názvu domény použít (pokud existuje). Nastavte na "none" není-li nasazení plně soukromý cluster |  | žádný |
| `masterClusterDnsType` | Typ domény pro webovou konzolu OpenShift. "default" infra použije název DNS hlavního uzlu veřejnou IP adresu. "vlastní" můžete zadat vlastní název | default <br> vlastní | default |
| `masterClusterDns` | Vlastní název DNS používat pro přístup k webové konzole OpenShift, pokud jste vybrali vlastní pro `masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Pokud nastavena na "nipio" `routingSubDomain` použije nip.io.  Použijte 'custom', pokud máte vlastní doménu, kterou chcete použít pro směrování | nipio <br> vlastní | nipio |
| `routingSubDomain` | Zástupný název DNS, který chcete použít pro směrování, pokud jste vybrali vlastní pro `routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Vyberte, jestli chcete použít existující virtuální síť nebo vytvořit novou virtuální síť | existující <br> novinka | novinka |
| `virtualNetworkResourceGroupName` | Název skupiny prostředků pro nové virtuální sítě, pokud jste vybrali pro "new. `virtualNetworkNewOrExisting` |  | resourceGroup().name |
| `virtualNetworkName` | Název nové virtuální sítě vytvořit, pokud jste vybrali pro "new. `virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | Předpony adres nové virtuální sítě |  | 10.0.0.0/14 |
| `masterSubnetName` | Název hlavní podsítě. |  | mastersubnet |
| `masterSubnetPrefix` | CIDR používané pro hlavní subnet - musí být podmnožinou addressPrefix |  | 10.1.0.0/16 |
| `infraSubnetName` | Název infra podsítě |  | infrasubnet |
| `infraSubnetPrefix` | CIDR používané pro subnet - infra musí být podmnožinou addressPrefix |  | 10.2.0.0/16 |
| `nodeSubnetName` | Název podsítě. Tento uzel |  | nodesubnet |
| `nodeSubnetPrefix` | CIDR používané pro podsíť uzlu – musí být podmnožinou addressPrefix |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Úplný odkaz na existující podsíť pro hlavní uzly. Není potřeba, pokud vytváříte novou virtuální síť / podsíť |  |  |
| `existingInfraSubnetReference` | Úplný odkaz na existující podsíť pro infra uzly. Není potřeba, pokud vytváříte novou virtuální síť / podsíť |  |  |
| `existingCnsSubnetReference` | Úplný odkaz na existující podsíť pro CNS uzly. Není potřeba, pokud vytváříte novou virtuální síť / podsíť |  |  |
| `existingNodeSubnetReference` | Úplný odkaz na existující podsíť pro výpočetní uzly. Není potřeba, pokud vytváříte novou virtuální síť / podsíť |  |  |
| `masterClusterType` | Zadejte, jestli cluster používá privátní nebo veřejné řídicí uzly. Pokud je zvolená privátní řídicí uzly nebude zveřejnit na Internetu prostřednictvím veřejné IP adresy. Místo toho použije privátní IP adresa zadaná v `masterPrivateClusterIp` | veřejná <br> privátní | veřejná |
| `masterPrivateClusterIp` | Vybrali privátní řídicí uzly potom privátní IP adresa musí být určeno pro použití interního nástroje pro hlavní uzly. Tato statická IP adresa musí být v rámci blok CIDR pro hlavní podsíť a již není používáno. Pokud se vybere veřejné hlavní uzly, tato hodnota se nepoužije, ale musí být zadán |  | 10.1.0.200 |
| `routerClusterType` | Zadejte, jestli cluster používá privátní nebo veřejné infra uzly. Pokud je zvolená privátní infra uzlů nesmí být zpřístupněné Internetu přes veřejnou IP adresu. Místo toho použije privátní IP adresa zadaná v `routerPrivateClusterIp` | veřejná <br> privátní | veřejná |
| `routerPrivateClusterIp` | Pokud privátní infra vybrané uzly a privátní IP adresa musí být zadán pro použití pomocí interního nástroje pro infra uzlů. Tato statická IP adresa musí být v rámci blok CIDR pro hlavní podsíť a již není používáno. Pokud veřejná infra vybrané uzly, tato hodnota se nepoužije, ale musí být zadán |  | 10.2.0.200 |
| `routingCertType` | Použít vlastní certifikát pro doménu směrování nebo certifikát podepsaný svým držitelem výchozí – postupujte podle pokynů v **vlastní certifikáty** oddílu | selfsigned <br> vlastní | selfsigned |
| `masterCertType` | Použít vlastní certifikát pro hlavní doménu nebo certifikát podepsaný svým držitelem výchozí – postupujte podle pokynů v **vlastní certifikáty** oddílu | selfsigned <br> vlastní | selfsigned |

<br>

### <a name="deploy-using-azure-cli"></a>Nasazení pomocí rozhraní příkazového řádku Azure

> [!NOTE] 
> Následující příkaz vyžaduje Azure CLI 2.0.8 nebo novější. Verzi rozhraní příkazového řádku pomocí můžete ověřit `az --version` příkazu. Pokud chcete aktualizovat verzi rozhraní příkazového řádku, naleznete v tématu [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

Následující příklad nasadí do skupiny prostředků s názvem openshiftrg, s názvem nasazení myOpenShiftCluster OpenShift cluster a všechny související prostředky. Šablona se odkazuje přímo z úložiště GitHub se vzorovými a místní parametrů, které se používá soubor s názvem souboru azuredeploy.parameters.json.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Nasazení trvá minimálně 60 minut na dokončení, na základě celkového počtu nasazených uzlů a možností, které nakonfigurujete. Plně kvalifikovaný název domény Bastionu DNS a adresu URL konzoly OpenShift vytiskne po dokončení nasazení do terminálu.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Pokud nechcete vytížit příkazový řádek čeká na dokončení, přidat nasazení `--no-wait` jako jednu z možností pro nasazení skupiny. Výstup z nasazení můžete získat z webu Azure portal v části nasazení pro skupinu prostředků.

## <a name="connect-to-the-openshift-cluster"></a>Připojte se ke clusteru Openshiftu

Až se nasazení dokončí, získání připojení z výstupní sekce nasazení. Připojení ke konzole OpenShift v prohlížeči pomocí **adresa URL konzoly OpenShift**. Můžete také SSH Bastion host. Tady je příklad, kde je uživatelské jméno správce clusteradmin a bastionu veřejné IP adresy DNS plně kvalifikovaný název domény je bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Použití [odstranění skupiny az](/cli/azure/group) příkazu k odebrání skupiny prostředků, clusteru OpenShift a všechny související prostředky, pokud jste už nepotřebujete.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Další postup

- [Úlohy po nasazení](./openshift-post-deployment.md)
- [Řešení potíží s nasazení OpenShift v Azure](./openshift-troubleshooting.md)
- [Začínáme s OpenShift Container Platform](https://docs.openshift.com/container-platform)
