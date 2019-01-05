---
title: Vytvořit šablonu clusteru Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit šablonu Resource Manageru pro cluster Service Fabric. Konfigurace zabezpečení, Azure Key Vault a Azure Active Directory (Azure AD) pro ověřování klientů.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/16/2018
ms.author: aljo
ms.openlocfilehash: cd4871e3e4613973a50c8ee0994fe0440150eddf
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54044021"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Vytvoření šablony Resource Manageru clusteru Service Fabric

[Clusteru Azure Service Fabric](service-fabric-deploy-anywhere.md) je síťově propojená sada virtuálních počítačů, do které se nasazují a spravují mikroslužby. Cluster Service Fabric běžící v Azure je prostředek Azure a nasadíte, spravované a sledované pomocí Resource Manageru.  Tento článek popisuje, jak vytvořit šablonu Resource Manageru pro cluster Service Fabric běžící v Azure.  Po dokončení šablonu můžete [nasazení clusteru v Azure](service-fabric-cluster-creation-via-arm.md).

Konfigurace zabezpečení clusteru při jeho nejprve nastavte není možné později změnit. Před nastavením clusteru, přečtěte si [scénáře zabezpečení clusteru Service Fabric][service-fabric-cluster-security]. V Azure Service Fabric používá x509 certifikátů k zabezpečení vašeho clusteru a její koncové body, ověřování klientů a šifrování dat. Azure Active Directory je také vhodné k zabezpečení přístupu ke koncovým bodům správy. Azure AD klienti a uživatelé musí být vytvořen před vytvořením clusteru.  Další informace najdete v článku [nastavení Azure AD pro ověřování klientů](service-fabric-cluster-creation-setup-aad.md).

Než nasadíte cluster pro produkční prostředí ke spuštění úlohy v produkčním prostředí, ujistěte se, abyste si nejdřív přečetli [připravenosti produkční kontrolní seznam](service-fabric-production-readiness-checklist.md).

## <a name="create-the-resource-manager-template"></a>Vytvoření šablony Resource Manageru
Šablony Resource Manageru ukázky jsou k dispozici v [ukázky na Githubu v Azure](https://github.com/Azure-Samples/service-fabric-cluster-templates). Tyto šablony lze použít jako výchozí bod pro šablony clusteru.

Tento článek používá [zabezpečeného clusteru s pěti uzly] [ service-fabric-secure-cluster-5-node-1-nodetype] příklad šablony a parametrů šablony. Stáhněte si *azuredeploy.json* a *azuredeploy.parameters.json* do vašeho počítače a otevřete oba soubory ve svém oblíbeném textovém editoru.

> [!NOTE]
> Pro národní cloudy (Azure Government, Azure China, Azure Germany), měli byste také přidat následující `fabricSettings` do šablony: `AADLoginEndpoint`, `AADTokenEndpointFormat` a `AADCertEndpointFormat`.

## <a name="add-certificates"></a>Přidat certifikáty
Přidejte certifikáty do šablony Resource Manageru clusteru odkazem, který obsahuje klíče certifikátů trezor klíčů. Přidejte tyto služby key vault parametry a hodnoty v souboru parametrů šablony Resource Manageru (*azuredeploy.parameters.json*).

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Přidat všechny certifikáty do osProfile sady škálování virtuálního počítače
Každý certifikát, který je nainstalován v clusteru se musí nakonfigurovat na **osProfile** části stupnice nastavení prostředku (Microsoft.Compute/virtualMachineScaleSets). Tato akce nastaví poskytovatele prostředků k instalaci certifikátu na virtuálních počítačích. Tato instalace zahrnuje certifikát clusteru a všechny certifikáty zabezpečení aplikací, které chcete použít pro vaše aplikace:

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

Ověřovací certifikát clusteru musí být nakonfigurovány v obou Service Fabric prostředku clusteru (Microsoft.ServiceFabric/clusters) a rozšíření Service Fabric pro virtuální počítač škálovací sady v prostředku virtuálního počítače škálovací sady. Toto uspořádání umožňuje poskytovateli prostředků Service Fabric ji nakonfigurovat pro použití pro ověření clusteru a ověření serveru pro koncové body správy.

#### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Přidání informací o certifikátu virtuálního počítače škálovací sady prostředků

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
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
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

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Přidání informací o certifikátu pro prostředek clusteru Service Fabric

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

## <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Přidat konfiguraci Azure AD, které používají Azure AD pro klientský přístup

Konfigurace služby Azure AD přidejte do šablony Resource Manageru clusteru odkazem, který obsahuje klíče certifikátů trezor klíčů. Přidejte tyto služby Azure AD parametry a hodnoty v souboru parametrů šablony Resource Manageru (*azuredeploy.parameters.json*). 

> [!NOTE]
> Azure AD klienti a uživatelé musí být vytvořen před vytvořením clusteru.  Další informace najdete v článku [nastavení Azure AD pro ověřování klientů](service-fabric-cluster-creation-setup-aad.md).

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

## <a name="populate-the-parameter-file-with-the-values"></a>Naplnění soubor parametrů s hodnotami

Nakonec použijte výstupní hodnoty ze služby key vault a příkazy Azure AD Powershellu k naplnění souboru parametrů.

Pokud máte v plánu používat moduly Powershellu služby Azure service fabric RM, není potřeba naplnění informací o certifikátu clusteru. Pokud má systém generování podepsané svým certifikátů pro zabezpečení clusteru, ponechat pouze jako hodnota null. 

> [!NOTE]
> Pro moduly RM sbírání a naplňte jimi tyto hodnoty prázdných parametrů názvy parametrů mnohem odpovídají názvům níže

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

Pokud používáte certifikáty aplikace nebo používáte existující cluster, který jste odeslali do služby key vault, musíte získat tyto informace a přidejte do ní.

Moduly RM nemají možnost vygenerovat konfiguraci Azure AD pro vás, tak pokud plánujete používat Azure AD pro klientský přístup, je potřeba ho naplnit.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

## <a name="test-your-template"></a>Test šablony
Pro testování soubor parametrů šablony Resource Manageru pomocí následujícího příkazu Powershellu:

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

V případě narazíte na problémy a získat zprávy jako nesrozumitelné, pak použijte "-Debug" jako možnost.

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Následující diagram znázorňuje, které je váš trezor klíčů a konfigurace služby Azure AD umístit do šablony Resource Manageru.

![Mapa závislostí Resource Manageru][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>Další postup
Teď, když máte vytvořenou šablonu pro váš cluster, zjistěte, jak [nasazení clusteru Azure](service-fabric-cluster-creation-via-arm.md).  Pokud jste to ještě neudělali, přečtěte si [připravenosti produkční kontrolní seznam](service-fabric-production-readiness-checklist.md) před nasazením clusteru pro produkční prostředí.

Další informace o syntaxi JSON a vlastnosti pro prostředky nasazené v tomto článku najdete v tématu:

* [Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters)
* [Microsoft.Storage/storageAccounts.](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)

<!-- Links -->
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-create-template/cluster-security-arm-dependency-map.png
