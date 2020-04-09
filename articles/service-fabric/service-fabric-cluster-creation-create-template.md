---
title: Vytvoření šablony clusteru Azure Service Fabric
description: Přečtěte si, jak vytvořit šablonu Správce prostředků pro cluster Service Fabric. Konfigurace zabezpečení, Trezoru klíčů Azure a Azure Active Directory (Azure AD) pro ověřování klientů.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: 6cf0f9c3b8b54db7bd27ec8dd9c9d59d849c74cc
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985367"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Vytvoření šablony Správce prostředků clusteru Service Fabric

[Cluster Azure Service Fabric](service-fabric-deploy-anywhere.md) je síťová sada virtuálních počítačů, do kterých se vaše mikroslužby nasazují a spravují. Cluster Service Fabric spuštěný v Azure je prostředek Azure a nasadí se, spravuje a monitoruje pomocí Správce prostředků.  Tento článek popisuje, jak vytvořit šablonu Správce prostředků pro cluster Service Fabric spuštěný v Azure.  Po dokončení šablony můžete [nasadit cluster v Azure](service-fabric-cluster-creation-via-arm.md).

Zabezpečení clusteru je nakonfigurováno při prvním nastavení clusteru a nelze jej později změnit. Před nastavením clusteru si přečtěte [scénáře zabezpečení clusteru Service Fabric][service-fabric-cluster-security]. V Azure service fabric používá certifikát x509 k zabezpečení clusteru a jeho koncových bodů, ověřování klientů a šifrování dat. Azure Active Directory se také doporučuje zabezpečit přístup ke koncovým bodům správy. Klienti azure ad a uživatelé musí být vytvořeny před vytvořením clusteru.  Další informace načtete [načláneknutí azure ad k ověření klientů](service-fabric-cluster-creation-setup-aad.md).

Před nasazením produkčního clusteru pro spuštění produkčních úloh si nejprve přečtěte [kontrolní seznam připravenosti výroby](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-the-resource-manager-template"></a>Vytvoření šablony Resource Manageru
Ukázkové šablony Správce prostředků jsou dostupné ve [vzorcích Azure na GitHubu](https://github.com/Azure-Samples/service-fabric-cluster-templates). Tyto šablony lze použít jako výchozí bod pro šablonu clusteru.

Tento článek používá [pět uzlů zabezpečené clusteru][service-fabric-secure-cluster-5-node-1-nodetype] příklad šablony a parametry šablony. Stáhněte si *azuredeploy.json* a *azuredeploy.parameters.json* do počítače a otevřete oba soubory ve svém oblíbeném textovém editoru.

> [!NOTE]
> Pro národní cloudy (Azure Government, Azure China, Azure `fabricSettings` Germany) `AADLoginEndpoint`byste `AADTokenEndpointFormat` `AADCertEndpointFormat`měli do šablony přidat také následující: a .

## <a name="add-certificates"></a>Přidání certifikátů
Certifikáty přidáte do šablony Správce prostředků clusteru odkazem na trezor klíčů, který obsahuje klíče certifikátu. Přidejte tyto parametry a hodnoty trezoru klíčů do souboru parametrů šablony Správce prostředků (*azuredeploy.parameters.json*).

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Přidání všech certifikátů do škálovací sady virtuálních strojů osProfile
Každý certifikát nainstalovaný v clusteru musí být nakonfigurován v části **osProfile** prostředku škálovací sady (Microsoft.Compute/virtualMachineScaleSets). Tato akce instruuje poskytovatele prostředků k instalaci certifikátu na virtuálních počítačích. Tato instalace zahrnuje certifikát clusteru i všechny certifikáty zabezpečení aplikací, které chcete použít pro vaše aplikace:

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

### <a name="configure-the-service-fabric-cluster-certificate"></a>Konfigurace certifikátu clusteru Service Fabric

Certifikát ověřování clusteru musí být nakonfigurován v prostředku clusteru Service Fabric (Microsoft.ServiceFabric/clusters) a v rozšíření Service Fabric pro škálovací sady virtuálních strojů v prostředku škálovací sady virtuálních strojů. Toto uspořádání umožňuje poskytovateli prostředků Service Fabric nakonfigurovat pro použití pro ověřování clusteru a ověřování serveru pro koncové body správy.

#### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Přidání informací o certifikátu, které prostředek škálovací sady virtuálních strojů

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

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Přidání informací o certifikátu do prostředku clusteru Service Fabric

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

## <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Přidání konfigurace Azure AD pro použití Azure AD pro přístup ke klientům

Konfiguraci Azure AD přidáte do šablony Správce prostředků clusteru odkazem na trezor klíčů, který obsahuje klíče certifikátu. Přidejte tyto parametry a hodnoty Azure AD do souboru parametrů šablony Správce prostředků (*azuredeploy.parameters.json*). 

> [!NOTE]
> Na Linuxu musí být před vytvořením clusteru vytvořeni klienti a uživatelé Azure AD.  Další informace načtete [načláneknutí azure ad k ověření klientů](service-fabric-cluster-creation-setup-aad.md).

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

## <a name="populate-the-parameter-file-with-the-values"></a>Naplnění souboru parametrů hodnotami

Nakonec použijte výstupní hodnoty z trezoru klíčů a příkazů Azure AD PowerShell k naplnění souboru parametrů.

Pokud máte v plánu použít moduly Azure service fabric RM PowerShell, není nutné naplnit informace o certifikátu clusteru. Pokud chcete, aby systém vygeneroval certifikát podepsaný svým držitelem pro zabezpečení clusteru, ponechte jej jako null. 

> [!NOTE]
> Pro moduly RM vyzvednout a naplnit tyto prázdné hodnoty parametrů, názvy parametrů hodně odpovídají níže uvedené názvy

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

Pokud používáte certifikáty aplikace nebo používáte existující cluster, který jste nahráli do trezoru klíčů, je třeba tyto informace získat a naplnit.

Rm moduly nemají možnost generovat konfiguraci Azure AD pro vás, takže pokud máte v plánu použít Azure AD pro přístup ke klientovi, je třeba naplnit.

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
Pomocí následujícího příkazu PowerShell otestujte šablonu Správce prostředků pomocí souboru parametrů:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

V případě, že narazíte na problémy a dostanete tajemné zprávy, použijte jako možnost "-Ladění".

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Následující diagram znázorňuje, kde se trezor klíčů a konfigurace Azure AD vejdou do šablony Správce prostředků.

![Mapa závislostí Správce prostředků][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>Další kroky
Teď, když máte šablonu pro váš cluster, zjistěte, jak [nasadit cluster do Azure](service-fabric-cluster-creation-via-arm.md).  Pokud jste tak ještě neučinili, přečtěte si [kontrolní seznam připravenosti výroby](service-fabric-production-readiness-checklist.md) před nasazením produkčního clusteru.

Informace o syntaxi JSON a vlastnostech prostředků nasazených v tomto článku najdete v těchto tématech:

* [Microsoft.ServiceFabric/clustery](/azure/templates/microsoft.servicefabric/clusters)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAdresy](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)

<!-- Links -->
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-create-template/cluster-security-arm-dependency-map.png
