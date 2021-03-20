---
title: Vytvoření šablony clusteru Azure Service Fabric
description: Naučte se, jak vytvořit šablonu Správce prostředků pro cluster Service Fabric. Nakonfigurujte zabezpečení, Azure Key Vault a Azure Active Directory (Azure AD) pro ověřování klientů.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: b028b5c1f32733146c071e50b24202e5bad945c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260495"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Vytvoření Správce prostředků šablony Service Fabric clusteru

[Cluster Azure Service Fabric](service-fabric-deploy-anywhere.md) je sada virtuálních počítačů připojených k síti, do kterých se nasazují a spravují vaše mikroslužby. Service Fabric cluster se systémem v Azure je prostředek Azure, který se nasazuje, spravuje a monitoruje pomocí Správce prostředků.  Tento článek popisuje, jak vytvořit šablonu Správce prostředků pro cluster Service Fabric běžící v Azure.  Po dokončení šablony můžete [cluster nasadit v Azure](service-fabric-cluster-creation-via-arm.md).

Zabezpečení clusteru je nakonfigurované při prvním nastavení clusteru a nedá se později změnit. Před nastavením clusteru si přečtěte [Service Fabric scénáře zabezpečení clusteru][service-fabric-cluster-security]. V Azure Service Fabric používá certifikát x509 k zabezpečení clusteru a jeho koncových bodů, ověřování klientů a šifrování dat. Azure Active Directory také doporučujeme zabezpečit přístup ke koncovým bodům správy. Před vytvořením clusteru je nutné vytvořit klienty a uživatele služby Azure AD.  Pokud potřebujete další informace, přečtěte si téma [Nastavení Azure AD pro ověřování klientů](service-fabric-cluster-creation-setup-aad.md).

Před nasazením produkčního clusteru pro spouštění produkčních úloh nezapomeňte nejdřív přečíst [Kontrolní seznam připravenosti na produkci](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-the-resource-manager-template"></a>Vytvoření šablony Resource Manageru
Ukázkové šablony Správce prostředků jsou k dispozici v [ukázkách Azure na GitHubu](https://github.com/Azure-Samples/service-fabric-cluster-templates). Tyto šablony lze použít jako výchozí bod pro šablonu clusteru.

Tento článek používá příklad šablony a parametrů šablony [zabezpečeného clusteru s pěti uzly][service-fabric-secure-cluster-5-node-1-nodetype] . Stáhněte *azuredeploy.js* a *azuredeploy.parameters.jsna* svůj počítač a otevřete oba soubory v oblíbeném textovém editoru.

> [!NOTE]
> Pro národní cloudy (Azure Government, Azure Čína, Azure Německo) byste měli taky `fabricSettings` do šablony přidat následující: `AADLoginEndpoint` `AADTokenEndpointFormat` a `AADCertEndpointFormat` .

## <a name="add-certificates"></a>Přidat certifikáty
Certifikáty se přidávají do šablony Správce prostředků clusteru odkazem na Trezor klíčů, který obsahuje klíče certifikátu. Přidejte tyto parametry a hodnoty trezoru klíčů do souboru parametrů šablony Správce prostředků (*azuredeploy.parameters.json*).

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Přidat všechny certifikáty do sady škálování virtuálních počítačů osProfile
Každý certifikát, který je nainstalovaný v clusteru, musí být nakonfigurovaný v oddílu **osProfile** prostředku sady škálování (Microsoft. COMPUTE/virtualMachineScaleSets). Tato akce vydá pokyn poskytovateli prostředků k instalaci certifikátu na virtuální počítače. Tato instalace zahrnuje certifikát clusteru i všechny certifikáty zabezpečení aplikací, které plánujete použít pro vaše aplikace:

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

### <a name="configure-the-service-fabric-cluster-certificate"></a>Konfigurace Service Fabricho certifikátu clusteru

Certifikát pro ověřování clusteru musí být nakonfigurovaný jak v prostředku Service Fabric clusteru (Microsoft. ServiceFabric/clustery), tak v rozšíření Service Fabric pro sady škálování virtuálních počítačů v prostředku sady škálování virtuálního počítače. Toto uspořádání umožňuje poskytovateli prostředků Service Fabric nakonfigurovat, aby se použil pro ověřování clusteru a ověřování serveru pro koncové body správy.

#### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Přidejte informace o certifikátu prostředek sady škálování virtuálního počítače.

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt_',variables('vmNodeType0Name'))]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Přidat informace o certifikátu do prostředku Service Fabric clusteru

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
  }
}
```

## <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Přidání konfigurace Azure AD pro použití Azure AD pro klientský přístup

Konfiguraci služby Azure AD přidáte do šablony Správce prostředků clusteru odkazem na Trezor klíčů, který obsahuje klíče certifikátu. Do souboru parametrů šablony Správce prostředků přidejte tyto parametry a hodnoty služby Azure AD (*azuredeploy.parameters.json*). 

> [!NOTE]
> V systému Linux je nutné před vytvořením clusteru vytvořit klienty a uživatele služby Azure AD.  Pokud potřebujete další informace, přečtěte si téma [Nastavení Azure AD pro ověřování klientů](service-fabric-cluster-creation-setup-aad.md).

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

## <a name="populate-the-parameter-file-with-the-values"></a>Naplnit soubor parametrů hodnotami

Nakonec použijte výstupní hodnoty z trezoru klíčů a příkazů Azure AD PowerShellu k naplnění souboru parametrů.

Pokud máte v úmyslu použít moduly PowerShellu pro Azure Service Fabric RM, nemusíte naplnit informace o certifikátu clusteru. Pokud chcete, aby systém vygeneroval certifikát podepsaný svým vlastníkem pro zabezpečení clusteru, zachovejte ho jenom jako null. 

> [!NOTE]
> Aby se moduly RM vybraly a naplnily tyto prázdné hodnoty parametrů, názvy parametrů velmi odpovídají názvům uvedeným níže.

```json
"clusterCertificateThumbprint": {
    "value": ""
},
"certificateCommonName": {
    "value": ""
},
"clusterCertificateUrlValue": {
    "value": ""
},
"sourceVaultvalue": {
    "value": ""
},
```

Pokud používáte certifikáty aplikací nebo používáte existující cluster, který jste nahráli do trezoru klíčů, musíte tyto informace získat a naplnit je.

Moduly RM nemají možnost Generovat konfiguraci služby Azure AD, takže pokud plánujete používat Azure AD pro klientský přístup, musíte ho naplnit.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

## <a name="test-your-template"></a>Testování šablony
Pomocí následujícího příkazu PowerShellu otestujte šablonu Správce prostředků se souborem parametrů:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

V případě, že dojde k potížím a získání nešifrovaných zpráv, použijte jako možnost "-debug".

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Následující diagram znázorňuje, kde se váš Trezor klíčů a konfigurace Azure AD vejdou do šablony Správce prostředků.

![Mapa závislostí Správce prostředků][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>Další kroky
Teď, když máte šablonu pro váš cluster, zjistěte, jak [nasadit cluster do Azure](service-fabric-cluster-creation-via-arm.md).  Pokud jste to ještě neudělali, přečtěte si [Kontrolní seznam připravenosti výroby](service-fabric-production-readiness-checklist.md) ještě před nasazením produkčního clusteru.

Další informace o syntaxi a vlastnostech JSON pro prostředky nasazené v tomto článku najdete v těchto tématech:

* [Microsoft. ServiceFabric/clustery](/azure/templates/microsoft.servicefabric/clusters)
* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft. Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft. Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)

<!-- Links -->
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: ../azure-resource-manager/templates/deploy-powershell.md

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-create-template/cluster-security-arm-dependency-map.png
