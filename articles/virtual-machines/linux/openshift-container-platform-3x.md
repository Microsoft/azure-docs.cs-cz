---
title: Nasazení kontejnerové platformy OpenShift 3.11 v Azure
description: Nasazení platformy OpenShift Container Platform 3.11 v Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 04/05/2020
ms.author: haroldw
ms.openlocfilehash: 7d6cd4c6ce7991ae83f6f4a1dd6d8b86fe7eedbc
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757896"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>Nasazení kontejnerové platformy OpenShift 3.11 v Azure

K nasazení platformy OpenShift Container Platform 3.11 v Azure můžete použít jednu z několika metod:

- Můžete ručně nasadit potřebné součásti infrastruktury Azure a potom postupujte podle [dokumentace platformy OpenShift Container Platform](https://docs.openshift.com/container-platform).
- Můžete také použít existující [šablonu Správce prostředků,](https://github.com/Microsoft/openshift-container-platform/) která zjednodušuje nasazení clusteru platformy Kontejnerové platformy OpenShift.
- Další možností je použít [nabídku Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy).

Pro všechny možnosti je vyžadováno předplatné Red Hat. Během nasazení je instance Red Hat Enterprise Linux registrována k předplatnému Red Hat a připojena k ID fondu, které obsahuje nároky pro platformu OpenShift Container.
Ujistěte se, že máte platné uživatelské jméno, heslo a ID fondu Správce předplatného (RHSM). Můžete použít aktivační klíč, ID organizace a ID fondu. Tyto informace můžete ověřit přihlášením k programu https://access.redhat.com.


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>Nasazení pomocí šablony OpenShift Container Platform Resource Manager 3.11

### <a name="private-clusters"></a>Privátní clustery

Nasazení privátních clusterů OpenShift vyžaduje více než jen to, že není veřejná IP přidružená k hlavnímu systému vyrovnávání zatížení (webová konzola) nebo k infravodě pro vyrovnávání zatížení (směrovač).  Privátní cluster obvykle používá vlastní server DNS (nikoli výchozí Azure DNS), vlastní název domény (například contoso.com) a předdefinované virtuální sítě.  U privátních clusterů je třeba předem nakonfigurovat virtuální síť se všemi příslušnými podsítěmi a nastavením serveru DNS.  Potom použijte **existující MasterSubnetReference**, **existujícíInfraSubnetReference**, **existingCnsSubnetReference**a **existingNodeSubnetReference** k určení existující podsítě pro použití v clusteru.

Pokud je vybrán soukromý master **(masterClusterType**=private), musí být zadána statická privátní IP adresa pro **masterPrivateClusterIp**.  Tato adresa IP bude přiřazena k přednímu konci hlavního vytažovače zatížení.  Ip musí být v rámci CIDR pro hlavní podsíť a nesmí být používán.  **masterClusterDnsType** musí být nastavena na "vlastní" a hlavní název DNS musí být poskytnut pro **masterClusterDns**.  Název DNS musí být mapován na statickou privátní IP adresu a bude použit pro přístup ke konzole v hlavních uzlech.

Pokud je vybrán soukromý směrovač **(routerClusterType**=private), je třeba zadat statickou privátní IP adresu pro **routerPrivateClusterIp**.  Tato IP adresa bude přiřazena k přednímu konci infraváhy.  Ip musí být v CIDR pro podsíť infra a nesmí být používán.  **směr SubDomainType** musí být nastaven na "vlastní" a pro směrování **subdomény**musí být zadán zástupný název DNS pro směrování.  

Pokud jsou vybrány soukromé předlohy a soukromý směrovač, musí být pro domainName zadán také vlastní název **domény.**

Po úspěšném nasazení je uzel Bastion jediným uzlem s veřejnou IP adresou, do které můžete ssh.  I v případě, že hlavní uzly jsou nakonfigurovány pro veřejný přístup, nejsou vystaveny pro přístup ssh.

Chcete-li nasadit pomocí šablony Správce prostředků, použijte soubor parametrů k zadání vstupních parametrů. Chcete-li dále přizpůsobit nasazení, rozviněte úložiště GitHub a změňte příslušné položky.

Některé běžné možnosti vlastního nastavení zahrnují, ale nejsou omezeny na:

- Velikost virtuálního počítače bašty (proměnná v azuredeploy.json)
- Konvence pojmenování (proměnné v azuredeploy.json)
- Specifika clusteru OpenShift, upravená prostřednictvím souboru hosts (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Konfigurace souboru parametrů

[Šablona OpenShift Container Platform](https://github.com/Microsoft/openshift-container-platform) má k dispozici více větví pro různé verze platformy OpenShift Container Platform.  Na základě vašich potřeb můžete nasadit přímo z repo nebo můžete rozvrstvit repo a provést vlastní změny šablon nebo skriptů před nasazením.

Použijte `appId` hodnotu z instančního `aadClientId` objektu, který jste pro parametr vytvořili dříve.

Následující příklad ukazuje soubor parametrů s názvem azuredeploy.parameters.json se všemi požadovanými vstupy.

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

Různé verze mohou mít různé parametry, takže ověřte potřebné parametry pro větev, kterou používáte.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy. Soubor Parameters.json je vysvětlen

| Vlastnost | Popis | Platné možnosti | Výchozí hodnota |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | URL artefaktů (json, skripty atd.) |  |  https:\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | Oblast Azure k nasazení prostředků |  |  |
| `masterVmSize` | Velikost hlavního virtuálního počítače. Výběr z jedné z povolených velikostí virtuálních počítačů uvedených v souboru azuredeploy.json |  | Standard_E2s_v3 |
| `infraVmSize` | Velikost virtuálního počítače Infra. Výběr z jedné z povolených velikostí virtuálních počítačů uvedených v souboru azuredeploy.json |  | Standard_D4s_v3 |
| `nodeVmSize` | Velikost virtuálního počítače uzlu aplikace. Výběr z jedné z povolených velikostí virtuálních počítačů uvedených v souboru azuredeploy.json |  | Standard_D4s_v3 |
| `cnsVmSize` | Velikost virtuálního počítače uzlu nativníúložiště kontejneru (CNS). Výběr z jedné z povolených velikostí virtuálních počítačů uvedených v souboru azuredeploy.json |  | Standard_E4s_v3 |
| `osImageType` | Obrázek RHEL, který chcete použít. defaultgallery: On-Demand; tržiště: image třetí strany | výchozí galerie <br> marketplace | výchozí galerie |
| `marketplaceOsImage` | Pokud `osImageType` je marketplace, zadejte příslušné hodnoty pro 'vydavatel', 'nabídka', 'sku', 'verze' nabídky tržiště. Tento parametr je typ objektu. |  |  |
| `storageKind` | Typ úložiště, které má být použito  | Spravované<br> Nespravované | Spravované |
| `openshiftClusterPrefix` | Předpona clusteru slouží ke konfiguraci názvů hostitelů pro všechny uzly.  Mezi 1 a 20 znaky |  | mycluster |
| `minoVersion` | Dílčí verze OpenShift Container Platform 3.11 pro nasazení |  | 69 |
| `masterInstanceCount` | Počet předlohových uzlů k nasazení | 1, 3, 5 | 3 |
| `infraInstanceCount` | Počet infrauzlů, které mají být nasazeny | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Počet uzlů k nasazení | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Počet uzlů CNS, které mají být nasazeny | 3, 4 | 3 |
| `osDiskSize` | Velikost disku operačního systému pro virtuální počítače (v GB) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Velikost datového disku, který se má připojit k uzlům pro svazek Dockeru (v GB) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Velikost datového disku pro připojení k uzlům CNS pro použití glusterfy (v GB | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Admin uživatelské jméno pro přihlášení os (VM) a počáteční OpenShift uživatele |  | ocpadmin |
| `enableMetrics` | Povolte metriky. Metriky vyžadují více prostředků, takže vyberte správnou velikost pro virtuální počítače Infra | true <br> false (nepravda) | false (nepravda) |
| `enableLogging` | Povolit protokolování. elasticsearch pod vyžaduje 8 GB RAM, takže vyberte správnou velikost pro Infra VM | true <br> false (nepravda) | false (nepravda) |
| `enableCNS` | Povolení nativního úložiště kontejneru | true <br> false (nepravda) | false (nepravda) |
| `rhsmUsernameOrOrgId` | Uživatelské jméno nebo ID organizace správce předplatného Red Hat |  |  |
| `rhsmPoolId` | ID fondu správce předplatného Red Hat, které obsahuje vaše nároky OpenShift pro výpočetní uzly |  |  |
| `rhsmBrokerPoolId` | ID fondu správce předplatného Red Hat, které obsahuje vaše nároky OpenShift pro hlavní a infračervené uzly. Pokud nemáte různá ID fondu, zadejte stejné ID fondu jako "rhsmPoolId" |  |
| `sshPublicKey` | Zkopírujte svůj veřejný klíč SSH zde |  |  |
| `keyVaultSubscriptionId` | ID předplatného, které obsahuje trezor klíčů |  |  |
| `keyVaultResourceGroup` | Název skupiny prostředků obsahující trezor klíčů |  |  |
| `keyVaultName` | Název trezoru klíčů, který jste vytvořili |  |  |
| `enableAzure` | Povolení azure cloudového zprostředkovatele | true <br> false (nepravda) | true |
| `aadClientId` | ID klienta služby Azure Active Directory označované také jako ID aplikace pro instanční objekt |  |  |
| `domainName` | Název vlastního názvu domény, který má být používán (je-li k dispozici). Pokud nenasazujete plně privátní cluster, nastavte na žádné. |  | Žádná |
| `masterClusterDnsType` | Typ domény pro webovou konzolu OpenShift. 'default' použije DNS štítek hlavní ho infra veřejné IP adresy. 'vlastní' umožňuje definovat své vlastní jméno | default <br> custom | default |
| `masterClusterDns` | Vlastní název DNS, který se má použít pro přístup k webové konzoli OpenShift, pokud jste vybrali`masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Pokud je nastavena na `routingSubDomain` 'nipio', bude používat nip.io.  Použijte 'vlastní', pokud máte vlastní doménu, kterou chcete použít pro směrování | nipio <br> custom | nipio |
| `routingSubDomain` | Název DNS se zástupnými symboly, který chcete použít pro směrování, pokud jste pro`routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Vyberte, zda chcete použít existující virtuální síť nebo vytvořit novou virtuální síť. | Existující <br> new | new |
| `virtualNetworkResourceGroupName` | Název skupiny prostředků pro novou virtuální síť, pokud jste pro novou možnost vybrali "nový"`virtualNetworkNewOrExisting` |  | název_skupiny_).název |
| `virtualNetworkName` | Název nové virtuální sítě, kterou chcete vytvořit, pokud jste vybrali "nový" pro`virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | Adresa předpona nové virtuální sítě |  | 10.0.0.0/14 |
| `masterSubnetName` | Název hlavní podsítě |  | masterpodnet |
| `masterSubnetPrefix` | CIDR pro hlavní podsíť - musí být podmnožinou adresyPrefix |  | 10.1.0.0/16 |
| `infraSubnetName` | Název podsítě infra |  | infrasubnet |
| `infraSubnetPrefix` | CIDR pro podsíť infra - musí být podmnožinou adresyPrefix |  | 10.2.0.0/16 |
| `nodeSubnetName` | Název podsítě uzlu |  | nodesubnet |
| `nodeSubnetPrefix` | CIDR pro podsíť uzlu - musí být podmnožinou adresyPrefix |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Úplný odkaz na existující podsíť pro hlavní uzly. Není potřeba při vytváření nové virtuální sítě / podsítě |  |  |
| `existingInfraSubnetReference` | Úplný odkaz na existující podsíť pro infranozuly. Není potřeba při vytváření nové virtuální sítě / podsítě |  |  |
| `existingCnsSubnetReference` | Úplný odkaz na existující podsíť pro uzly CNS. Není potřeba při vytváření nové virtuální sítě / podsítě |  |  |
| `existingNodeSubnetReference` | Úplný odkaz na existující podsíť pro výpočetní uzly. Není potřeba při vytváření nové virtuální sítě / podsítě |  |  |
| `masterClusterType` | Určete, zda cluster používá privátní nebo veřejné hlavní uzly. Pokud je vybrána možnost Private, hlavní uzly nebudou vystaveny Internetu prostřednictvím veřejné IP adresy. Místo toho použije soukromou IP adresu uvedenou`masterPrivateClusterIp` | public <br> private | public |
| `masterPrivateClusterIp` | Pokud jsou vybrány soukromé hlavní uzly, musí být zadána privátní ADRESA IP pro použití interním vyvykladačem zatížení pro hlavní uzly. Tato statická adresa IP musí být v bloku CIDR pro hlavní podsíť a již není používána. Pokud jsou vybrány veřejné hlavní uzly, tato hodnota nebude použita, ale musí být stále zadána. |  | 10.1.0.200 |
| `routerClusterType` | Určete, zda cluster používá privátní nebo veřejné infraly. Pokud je vybrána možnost Soukromé, infranové uzly nebudou vystaveny internetu prostřednictvím veřejné IP adresy. Místo toho použije soukromou IP adresu uvedenou`routerPrivateClusterIp` | public <br> private | public |
| `routerPrivateClusterIp` | Pokud jsou vybrány soukromé infranové uzly, musí být zadána privátní IP adresa pro použití interním vynakladačem zatížení pro infranozuly. Tato statická IP adresa musí být v bloku CIDR pro podsíť infra a již není používána. Pokud jsou vybrány veřejné infrauly, tato hodnota nebude použita, ale musí být stále zadána. |  | 10.2.0.200 |
| `routingCertType` | Použití vlastního certifikátu pro směrovací doménu nebo výchozího certifikátu podepsaného svým držitelem – postupujte podle pokynů v části **Vlastní certifikáty** | vlastní podpis <br> custom | vlastní podpis |
| `masterCertType` | Použití vlastního certifikátu pro hlavní doménu nebo výchozího certifikátu podepsaného svým držitelem – postupujte podle pokynů v části **Vlastní certifikáty** | vlastní podpis <br> custom | vlastní podpis |

<br>

### <a name="deploy-using-azure-cli"></a>Nasazení s využitím rozhraní příkazového řádku Azure

> [!NOTE] 
> Následující příkaz vyžaduje Azure CLI 2.0.8 nebo novější. Můžete ověřit verzi rozhraní příkazového příkazu pomocí příkazu. `az --version` Pokud chcete aktualizovat verzi příkazového příkazového příkazu, [přečtěte si informace o instalaci příkazového příkazového příkazu k webu Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

Následující příklad nasazuje cluster OpenShift a všechny související prostředky do skupiny prostředků s názvem openshiftrg s názvem deployment name myOpenShiftCluster. Šablona je odkazována přímo z úložiště GitHub a používá se soubor místních parametrů s názvem azuredeploy.parameters.json.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Nasazení trvá nejméně 60 minut na základě celkového počtu nasazených uzlů a nakonfigurovaných možností. Po dokončení nasazení se do terminálu vytiskne hlavní název domény bastiondns a adresa URL konzoly OpenShift.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Pokud nechcete svázat příkazový řádek čekání na dokončení nasazení, přidejte `--no-wait` jako jednu z možností pro nasazení skupiny. Výstup z nasazení lze načíst z portálu Azure v části nasazení pro skupinu prostředků.

## <a name="connect-to-the-openshift-cluster"></a>Připojení ke clusteru OpenShift

Po dokončení nasazení načtěte připojení z výstupní části nasazení. Připojte se ke konzoli OpenShift s prohlížečem pomocí **adresy URL konzoly OpenShift**. Můžete také SSH na hostitele Bastion. Následuje příklad, kdy je uživatelské jméno správce clusteradmin a je bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com veřejný název domény dns DNS bastion:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pomocí příkazu [delete skupiny az](/cli/azure/group) odeberte skupinu prostředků, cluster OpenShift a všechny související prostředky, pokud už nejsou potřeba.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Další kroky

- [Úlohy po nasazení](./openshift-container-platform-3x-post-deployment.md)
- [Poradce při potížích s nasazením OpenShift v Azure](./openshift-container-platform-3x-troubleshooting.md)
- [Začínáme s platformou OpenShift Container Platform](https://docs.openshift.com)
