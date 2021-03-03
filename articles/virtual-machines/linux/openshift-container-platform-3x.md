---
title: Nasazení OpenShift kontejneru platformy 3,11 v Azure
description: Nasaďte v Azure kontejnerovou platformu OpenShift 3,11.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 04/05/2020
ms.author: haroldw
ms.openlocfilehash: 054fb2ffc65b44d5436282eab5327f0facf39c06
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671193"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>Nasazení OpenShift kontejneru platformy 3,11 v Azure

K nasazení OpenShift kontejneru Platform 3,11 v Azure můžete použít některou z několika metod:

- Je možné ručně nasadit nezbytné součásti infrastruktury Azure a potom postupovat podle [dokumentace k platformě OpenShift Container](https://docs.openshift.com/container-platform).
- Můžete také použít stávající [šablonu správce prostředků](https://github.com/Microsoft/openshift-container-platform/) , která zjednodušuje nasazení clusteru OpenShift Container Platform.
- Další možností je použití [nabídky Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy).

Pro všechny možnosti se vyžaduje předplatné Red Hat. Během nasazování je instance Red Hat Enterprise Linux zaregistrovaná v rámci předplatného Red Hat a připojená k ID fondu, které obsahuje nároky na OpenShift kontejnerové platformy.
Ujistěte se, že máte platné uživatelské jméno, heslo a ID fondu pro Red Hat Subscription Manager (RHSM). Můžete použít aktivační klíč, ID organizace a ID fondu. Tyto informace můžete ověřit tak, že se přihlásíte k https://access.redhat.com .


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>Nasazení pomocí šablony OpenShift Container Platform Správce prostředků 3,11

### <a name="private-clusters"></a>Soukromé clustery

Nasazení privátních clusterů OpenShift vyžaduje více než jen veřejnou IP adresu přidruženou k hlavnímu nástroji pro vyrovnávání zatížení (webová konzola) nebo k nástroji pro vyrovnávání zatížení (směrovač).  Privátní cluster obecně používá vlastní server DNS (nikoli výchozí Azure DNS), vlastní název domény (například contoso.com) a předem definované virtuální sítě (y).  U privátních clusterů je potřeba nakonfigurovat virtuální síť se všemi příslušnými podsítěmi a nastaveními DNS serveru předem.  Pak pomocí **existingMasterSubnetReference**, **existingInfraSubnetReference**, **existingCnsSubnetReference** a **existingNodeSubnetReference** určete existující podsíť pro použití clusterem.

Je-li vybrána privátní hlavní server (**masterClusterType**= Private), musí být pro **masterPrivateClusterIp** zadána statická privátní IP adresa.  Tato IP adresa se přiřadí front-endu hlavního nástroje pro vyrovnávání zatížení.  IP adresa musí být v rámci CIDR pro hlavní podsíť a nepoužívá se.  **masterClusterDnsType** musí být nastavená na Custom a musí se zadat hlavní název DNS pro **masterClusterDns**.  Název DNS se musí namapovat na statickou privátní IP adresu a bude se používat pro přístup ke konzole na hlavních uzlech.

Pokud je vybrán privátní směrovač (**routerClusterType**= Private), musí být pro **routerPrivateClusterIp** zadána statická privátní IP adresa.  Tato IP adresa se přiřadí front-endu nástroje pro vyrovnávání zatížení.  IP adresa musí být v rámci CIDR pro podsíť, která se nepoužívá.  **routingSubDomainType** musí být nastavená na Custom a pro **routingSubDomain** se musí zadat název DNS zástupného znaku pro směrování.  

Pokud jsou vybrány privátní hlavní servery a privátní směrovač, je nutné zadat vlastní název domény také pro název **domény** .

Po úspěšném nasazení je uzel bastionu jediným uzlem s veřejnou IP adresou, na kterou můžete protokol SSH.  I když jsou hlavní uzly nakonfigurované pro veřejný přístup, nezveřejňují se pro přístup přes SSH.

Chcete-li nasadit pomocí šablony Správce prostředků, použijte soubor parametrů k zadání vstupních parametrů. Pro další přizpůsobení nasazení, rozvětvete úložiště GitHub a změňte příslušné položky.

Mezi běžné možnosti přizpůsobení patří, ale nejsou omezené na:

- Velikost virtuálního počítače bastionu (proměnná v azuredeploy.jszapnutá)
- Zásady vytváření názvů (proměnné v azuredeploy.jszapnuté)
- OpenShift konkrétní clustery, upravené prostřednictvím souboru Hosts (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Konfigurace souboru parametrů

[Šablona platformy kontejnerů OpenShift](https://github.com/Microsoft/openshift-container-platform) má k dispozici více větví pro různé verze kontejnerové platformy OpenShift.  Na základě vašich potřeb můžete nasadit přímo z úložiště nebo můžete rozvětvit úložiště a před nasazením vytvořit vlastní změny v šablonách nebo skriptech.

`appId`Pro parametr použijte hodnotu z instančního objektu, který jste vytvořili dříve `aadClientId` .

Následující příklad ukazuje soubor parametrů s názvem azuredeploy.parameters.jspro všechny požadované vstupy.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

Nahraďte parametry konkrétními informacemi.

Různé verze mohou mít různé parametry, takže ověřují parametry potřebné pro větev, kterou používáte.

### <a name="azuredeployparametersjson-file-explained"></a>Vysvětlení azuredeploy.Parameters.jssouboru

| Vlastnost | Popis | Platné možnosti | Výchozí hodnota |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | Adresa URL artefaktů (JSON, skripty atd.) |  |  https: \/ /raw.githubusercontent.com/Microsoft/OpenShift-Container-Platform/Master  |
| `location` | Oblast Azure, do které se nasazují prostředky |  |  |
| `masterVmSize` | Velikost hlavního virtuálního počítače. Vyberte jednu z povolených velikostí virtuálních počítačů uvedených v azuredeploy.jssouboru. |  | Standard_E2s_v3 |
| `infraVmSize` | Velikost infračerveného virtuálního počítače. Vyberte jednu z povolených velikostí virtuálních počítačů uvedených v azuredeploy.jssouboru. |  | Standard_D4s_v3 |
| `nodeVmSize` | Velikost virtuálního počítače uzlu aplikace Vyberte jednu z povolených velikostí virtuálních počítačů uvedených v azuredeploy.jssouboru. |  | Standard_D4s_v3 |
| `cnsVmSize` | Velikost virtuálního počítače uzlu pro nativní úložiště kontejnerů (CNS). Vyberte jednu z povolených velikostí virtuálních počítačů uvedených v azuredeploy.jssouboru. |  | Standard_E4s_v3 |
| `osImageType` | Obrázek RHEL, který se má použít defaultgallery: na vyžádání; Marketplace: obrázek třetí strany | defaultgallery <br> marketplace | defaultgallery |
| `marketplaceOsImage` | Pokud `osImageType` je Marketplace, zadejte odpovídající hodnoty pro ' Publish ', ' nabídka ', ' SKU ', ' verze ' nabídky Marketplace. Tento parametr je typ objektu. |  |  |
| `storageKind` | Typ úložiště, který se má použít  | starosti<br> spravovateln | starosti |
| `openshiftClusterPrefix` | Předpona clusteru používaná ke konfiguraci názvů hostitelů pro všechny uzly.  Mezi 1 a 20 znaky |  | mycluster |
| `minoVersion` | Podverze OpenShift kontejneru platformy 3,11 k nasazení |  | 69 |
| `masterInstanceCount` | Počet hlavních uzlů k nasazení | 1, 3, 5 | 3 |
| `infraInstanceCount` | Počet uzlů pro nasazení | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Počet uzlů k nasazení | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Počet uzlů propojené sítě, které se mají nasadit | 3, 4 | 3 |
| `osDiskSize` | Velikost disku s operačním systémem pro virtuální počítač (v GB) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Velikost datového disku, který se má připojit k uzlům pro svazek Docker (v GB) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Velikost datového disku, který se má připojit k uzlům CNS pro použití v GlusterFS (v GB | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Uživatelské jméno správce pro přihlášení k operačnímu systému (VM) a počátečního uživatele OpenShift |  | ocpadmin |
| `enableMetrics` | Povolte metriky. Metriky vyžadují více prostředků, takže vyberte vhodnou velikost pro virtuální počítač s infračerveným odkazem. | true <br> false (nepravda) | false (nepravda) |
| `enableLogging` | Povolit protokolování. Elasticsearch pod vyžaduje 8 GB paměti RAM, takže vyberte vhodnou velikost pro virtuální počítač pro infračervené zařízení. | true <br> false (nepravda) | false (nepravda) |
| `enableCNS` | Povolit nativní úložiště kontejneru | true <br> false (nepravda) | false (nepravda) |
| `rhsmUsernameOrOrgId` | Uživatelské jméno nebo ID organizace pro správce předplatného Red Hat |  |  |
| `rhsmPoolId` | ID fondu správce předplatného Red Hat, které obsahuje vaše nároky na OpenShift pro výpočetní uzly |  |  |
| `rhsmBrokerPoolId` | ID fondu správce předplatného Red Hat, které obsahuje vaše nároky na OpenShift pro hlavní a infračervené uzly. Pokud nemáte jiná ID fondu, zadejte jako rhsmPoolId stejné ID fondu. |  |
| `sshPublicKey` | Sem zkopírujte veřejný klíč SSH. |  |  |
| `keyVaultSubscriptionId` | ID předplatného pro předplatné obsahující Key Vault |  |  |
| `keyVaultResourceGroup` | Název skupiny prostředků, která obsahuje Key Vault |  |  |
| `keyVaultName` | Název Key Vault, který jste vytvořili |  |  |
| `enableAzure` | Povolit Azure Cloud Provider | true <br> false (nepravda) | true |
| `aadClientId` | ID klienta Azure Active Directory také označované jako ID aplikace pro instanční objekt |  |  |
| `domainName` | Název vlastního názvu domény, který se má použít (Pokud je k dispozici) Pokud není nasazen plně soukromý cluster, nastavte na hodnotu None. |  | žádné |
| `masterClusterDnsType` | Typ domény pro webovou konzolu OpenShift klíčové slovo Default bude používat označení DNS pro veřejnou IP adresu hlavního infračerveného provozu. možnost vlastní umožňuje definovat vlastní název. | default <br> vlastní | default |
| `masterClusterDns` | Vlastní název DNS, který se použije pro přístup k webové konzoli OpenShift, pokud jste vybrali možnost Custom (vlastní) pro `masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Pokud je nastavená na ' nipio ', použije `routingSubDomain` NIP.IO.  Pokud máte vlastní doménu, kterou chcete použít pro směrování, použijte možnost vlastní. | nipio <br> vlastní | nipio |
| `routingSubDomain` | Název DNS se zástupnými znaky, který chcete použít pro směrování, pokud jste vybrali možnost vlastní pro `routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Vyberte, zda chcete použít existující Virtual Network nebo vytvořit nový Virtual Network | stávající <br> new | new |
| `virtualNetworkResourceGroupName` | Název skupiny prostředků pro nový Virtual Network, pokud jste vybrali možnost Nový pro `virtualNetworkNewOrExisting` |  | resourceName (). Name |
| `virtualNetworkName` | Název nového Virtual Network, který se má vytvořit, pokud jste vybrali možnost Nový pro `virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | Předpona adresy nové virtuální sítě |  | 10.0.0.0/14 |
| `masterSubnetName` | Název hlavní podsítě |  | mastersubnet |
| `masterSubnetPrefix` | CIDR použitý pro hlavní podsíť – musí být podmnožinou addressPrefix. |  | 10.1.0.0/16 |
| `infraSubnetName` | Název infračervené podsítě |  | infrasubnet |
| `infraSubnetPrefix` | CIDR, který se používá pro podsíť v síti, musí být podmnožinou addressPrefix. |  | 10.2.0.0/16 |
| `nodeSubnetName` | Název podsítě uzlu |  | nodesubnet |
| `nodeSubnetPrefix` | CIDR použitý pro podsíť uzlu – musí být podmnožinou addressPrefix. |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Úplný odkaz na existující podsíť pro hlavní uzly. Není nutné při vytváření nové virtuální sítě nebo podsítě. |  |  |
| `existingInfraSubnetReference` | Úplný odkaz na existující podsíť pro infračervené uzly Není nutné při vytváření nové virtuální sítě nebo podsítě. |  |  |
| `existingCnsSubnetReference` | Úplný odkaz na existující podsíť pro uzly CNS Není nutné při vytváření nové virtuální sítě nebo podsítě. |  |  |
| `existingNodeSubnetReference` | Úplný odkaz na existující podsíť pro výpočetní uzly. Není nutné při vytváření nové virtuální sítě nebo podsítě. |  |  |
| `masterClusterType` | Určete, jestli cluster používá soukromé nebo veřejné hlavní uzly. Pokud je zvolená možnost privátní, hlavní uzly nebudou zpřístupněny Internetu prostřednictvím veřejné IP adresy. Místo toho použije privátní IP adresu uvedenou v `masterPrivateClusterIp` | public <br> private | public |
| `masterPrivateClusterIp` | Pokud jsou vybrány privátní hlavní uzly, musí být zadána privátní IP adresa pro použití interním nástrojem pro vyrovnávání zatížení pro hlavní uzly. Tato statická IP adresa musí být v bloku CIDR pro hlavní podsíť a ještě se nepoužívá. Pokud jsou vybrané veřejné hlavní uzly, tato hodnota se nepoužije, ale musí se zadat i dál. |  | 10.1.0.200 |
| `routerClusterType` | Určete, jestli cluster používá soukromé nebo veřejné infračervené uzly. Pokud je zvolená možnost privátní, nebudou se uzly v síti Internet zveřejnit prostřednictvím veřejné IP adresy. Místo toho použije privátní IP adresu uvedenou v `routerPrivateClusterIp` | public <br> private | public |
| `routerPrivateClusterIp` | Pokud jsou vybrány privátní infračervené uzly, musí být zadána privátní IP adresa pro použití interním nástrojem pro vyrovnávání zatížení pro infračervené uzly. Tato statická IP adresa musí být v rámci bloku CIDR pro podsíť, která se už nepoužívá. Pokud je vybraná možnost veřejné infračervené uzly, tato hodnota se nebude používat, ale musí se dál zadat. |  | 10.2.0.200 |
| `routingCertType` | Použijte vlastní certifikát pro doménu směrování nebo výchozí certifikát podepsaný svým držitelem – postupujte podle pokynů v části **vlastní certifikáty** . | selfsigned <br> vlastní | selfsigned |
| `masterCertType` | Použijte vlastní certifikát pro hlavní doménu nebo výchozí certifikát podepsaný svým držitelem – postupujte podle pokynů v části **vlastní certifikáty** . | selfsigned <br> vlastní | selfsigned |

<br>

### <a name="deploy-using-azure-cli"></a>Nasazení s využitím rozhraní příkazového řádku Azure

> [!NOTE] 
> Následující příkaz vyžaduje rozhraní příkazového řádku Azure CLI 2.0.8 nebo novější. Verzi rozhraní příkazového řádku můžete ověřit pomocí `az --version` příkazu. Pokud chcete aktualizovat verzi rozhraní příkazového řádku, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latesti).

Následující příklad nasadí cluster OpenShift a všechny související prostředky do skupiny prostředků s názvem openshiftrg s názvem nasazení myOpenShiftCluster. Na šablonu se odkazuje přímo z úložiště GitHubu a používá se soubor místních parametrů s názvem azuredeploy.parameters.jsv souboru.

```azurecli 
az deployment group create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Dokončení nasazení trvá minimálně 60 minut, a to na základě celkového počtu nasazených uzlů a nakonfigurovaných možností. Plně kvalifikovaný název domény DNS bastionu a adresa URL konzoly OpenShift se po dokončení nasazení vytisknou do terminálu.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Pokud nechcete vytvořit vazbu na příkazový řádek, který čeká na dokončení nasazení, přidejte `--no-wait` jako jednu z možností pro nasazení skupiny. Výstup z nasazení lze načíst z Azure Portal v části nasazení pro skupinu prostředků.

## <a name="connect-to-the-openshift-cluster"></a>Připojení ke clusteru OpenShift

Až se nasazení dokončí, načtěte připojení z části výstup nasazení. Pomocí **adresy URL konzoly OpenShift** se připojte ke konzole OpenShift pomocí prohlížeče. K hostiteli bastionu můžete také přissh. Tady je příklad, kde uživatelské jméno správce je clusteradmin a plně kvalifikovaný název domény DNS bastionu pro veřejnou IP adresu je bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pomocí příkazu [AZ Group Delete](/cli/azure/group) odeberte skupinu prostředků, cluster OpenShift a všechny související prostředky, pokud už je nepotřebujete.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Další kroky

- [Úkoly po nasazení](./openshift-container-platform-3x-post-deployment.md)
- [Řešení potíží s nasazením OpenShift v Azure](./openshift-container-platform-3x-troubleshooting.md)
- [Začínáme s kontejnerovou platformou OpenShift](https://docs.openshift.com)
