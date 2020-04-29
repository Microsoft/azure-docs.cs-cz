---
title: Konfigurace privátního odkazu Azure
titleSuffix: Azure Machine Learning
description: Pomocí privátního odkazu Azure získáte zabezpečený přístup k pracovnímu prostoru Azure Machine Learning z virtuální sítě.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/13/2020
ms.openlocfilehash: 8140fc4286ac97260e0b23ea700a70303ec69e2e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81411202"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Konfigurace privátního odkazu Azure pro pracovní prostor Azure Machine Learning (Preview)

V tomto dokumentu se dozvíte, jak pomocí privátního propojení Azure s vaším pracovním prostorem Azure Machine Learning. Tato funkce je aktuálně ve verzi Preview a je k dispozici v USA – východ USA – západ 2 USA (střed) – jih oblastech. 

Privátní odkaz Azure umožňuje připojit se k pracovnímu prostoru pomocí privátního koncového bodu. Soukromý koncový bod je sada privátních IP adres v rámci vaší virtuální sítě. Přístup k pracovnímu prostoru pak můžete omezit tak, aby se nacházet jenom přes privátní IP adresy. Soukromý odkaz pomáhá snižovat riziko exfiltrace dat. Další informace o privátních koncových bodech najdete v článku věnovaném [privátním odkazům Azure](/azure/private-link/private-link-overview) .

> [!IMPORTANT]
> Privátní propojení Azure neovlivňuje plochu ovládacího prvku Azure (operace správy), jako je například odstranění pracovního prostoru nebo Správa výpočetních prostředků. Například vytvoření, aktualizace nebo odstranění cíle služby Compute. Tyto operace se provádějí na veřejném Internetu jako normální.
>
> V pracovním prostoru, kde je povolený privátní odkaz, se nepodporuje Azure Machine Learning výpočetních instancí Preview.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Vytvoření pracovního prostoru, který používá privátní koncový bod

V současné době podporujeme při vytváření nového pracovního prostoru Azure Machine Learning jenom povolení privátního koncového bodu. Následující šablony jsou k dispozici pro několik oblíbených konfigurací:

> [!TIP]
> Automatické schválení ovládá automatizovaný přístup k prostředku s povoleným privátním propojením. Další informace najdete v tématu [co je služba Azure Private Link](../private-link/private-link-service-overview.md).

* [Pracovní prostor s klíči spravovanými zákazníkem a automatickým schválením pro privátní propojení](#cmkaapl)
* [Pracovní prostor s klíči spravovanými zákazníkem a ručním schválením pro privátní propojení](#cmkmapl)
* [Pracovní prostor s klíči spravovanými Microsoftem a automatickým schválením pro privátní propojení](#mmkaapl)
* [Pracovní prostor s klíči spravovanými Microsoftem a ručním schválením pro privátní propojení](#mmkmapl)

Při nasazování šablony je nutné zadat následující informace:

* Název pracovního prostoru
* Oblast Azure, ve které se mají vytvářet prostředky
* Verze pracovního prostoru (Basic nebo Enterprise)
* Pokud má být povoleno nastavení vysoké důvěrnosti pro pracovní prostor
* Pokud má být povoleno šifrování pro pracovní prostor s klíčem spravovaným zákazníkem a související hodnoty pro klíč
* Virtual Network a název podsítě šablona vytvoří novou virtuální síť a podsíť.

Po odeslání a zřízení šablony bude skupina prostředků obsahující váš pracovní prostor obsahovat tři nové typy artefaktů, které se vztahují k soukromému propojení:

* Soukromý koncový bod
* Síťové rozhraní
* Zóna privátního DNS

Pracovní prostor obsahuje taky Virtual Network Azure, které mohou komunikovat s pracovním prostorem prostřednictvím privátního koncového bodu.

### <a name="deploy-the-template-using-the-azure-portal"></a>Nasazení šablony pomocí Azure Portal

1. Postupujte podle kroků v části [nasazení prostředků z vlastní šablony](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Až se dostanete na obrazovku __Upravit šablonu__ , vložte ji na konci tohoto dokumentu do jedné ze šablon.
1. Vyberte __Uložit__ a použijte šablonu. Zadejte následující informace a vyjádřete souhlas s uvedenými podmínkami a ujednáními:

   * Předplatné: vyberte předplatné Azure, které chcete použít pro tyto prostředky.
   * Skupina prostředků: vyberte nebo vytvořte skupinu prostředků, která bude obsahovat služby.
   * Název pracovního prostoru: název, který se má použít pro pracovní prostor Azure Machine Learning, který se vytvoří. Název pracovního prostoru musí být dlouhý 3 až 33 znaků. Může obsahovat pouze alfanumerické znaky a znak "-".
   * Umístění: vyberte umístění, kde se budou prostředky vytvářet.

Další informace najdete v tématu [nasazení prostředků z vlastní šablony](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

### <a name="deploy-the-template-using-azure-powershell"></a>Nasazení šablony pomocí Azure PowerShell

V tomto příkladu se předpokládá, že jste uložili jednu ze šablon z konce tohoto dokumentu do souboru s názvem `azuredeploy.json` v aktuálním adresáři:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

Další informace najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) a [nasazení privátní šablony Správce prostředků pomocí tokenu SAS a Azure PowerShell](../azure-resource-manager/templates/secure-template-with-sas-token.md).

### <a name="deploy-the-template-using-the-azure-cli"></a>Nasazení šablony pomocí Azure CLI

V tomto příkladu se předpokládá, že jste uložili jednu ze šablon z konce tohoto dokumentu do souboru s názvem `azuredeploy.json` v aktuálním adresáři:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

Další informace najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../azure-resource-manager/templates/deploy-cli.md) a [nasazení privátních správce prostředků šablony s tokenem SAS a](../azure-resource-manager/templates/secure-template-with-sas-token.md)rozhraním příkazového řádku Azure CLI.

## <a name="using-a-workspace-over-a-private-endpoint"></a>Použití pracovního prostoru v rámci privátního koncového bodu

Vzhledem k tomu, že komunikace s pracovním prostorem je povolená jenom z virtuální sítě, musí být všechna vývojová prostředí, která používají pracovní prostory, členy virtuální sítě. Například virtuální počítač ve virtuální síti nebo počítač připojený k virtuální síti pomocí brány VPN.

> [!IMPORTANT]
> Aby nedošlo k dočasnému přerušení připojení, společnost Microsoft doporučuje po povolení privátního odkazu vyprázdnit mezipaměť DNS na počítačích, které se připojují k pracovnímu prostoru. 

Informace o službě Azure Virtual Machines najdete v [dokumentaci k Virtual Machines](/azure/virtual-machines/).

Informace o branách VPN najdete v tématu [co je VPN Gateway](/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="using-azure-storage"></a>S využitím Azure Storage

Pokud chcete zabezpečit účet Azure Storage používaný vaším pracovním prostorem, umístěte ho do virtuální sítě.

Informace o tom, jak vložit účet úložiště ve virtuální síti, najdete v tématu [použití účtu úložiště pro váš pracovní prostor](how-to-enable-virtual-network.md#use-a-storage-account-for-your-workspace).

## <a name="using-azure-key-vault"></a>Použití Azure Key Vault

Pokud chcete zabezpečit Azure Key Vault používané vaším pracovním prostorem, můžete ho buď umístit do virtuální sítě, nebo pro něj Povolit privátní propojení.

Informace o vložení trezoru klíčů ve virtuální síti najdete v tématu [použití instance trezoru klíčů v pracovním prostoru](how-to-enable-virtual-network.md#use-a-key-vault-instance-with-your-workspace).

Informace o povolení privátního odkazu pro Trezor klíčů najdete v tématu věnovaném [integraci Key Vault s privátním odkazem Azure](/azure/key-vault/private-link-service).

## <a name="using-azure-kubernetes-services"></a>Používání služeb Azure Kubernetes Services

Pokud chcete zabezpečit službu Azure Kubernetes, kterou používá váš pracovní prostor, umístěte ji do virtuální sítě. Další informace najdete v tématu [použití služeb Azure Kubernetes Services s vaším pracovním prostorem](how-to-enable-virtual-network.md#aksvnet).

> [!WARNING]
> Azure Machine Learning nepodporuje použití služby Azure Kubernetes s povoleným privátním odkazem.

## <a name="azure-container-registry"></a>Azure Container Registry

Informace o zabezpečení Azure Container Registry v rámci virtuální sítě najdete v tématu [použití Azure Container Registry](how-to-enable-virtual-network.md#use-azure-container-registry).

> [!IMPORTANT]
> Pokud pro váš pracovní prostor Azure Machine Learning používáte privátní odkaz a Azure Container Registry pro svůj pracovní prostor ve virtuální síti, musíte použít také následující šablonu Azure Resource Manager. Tato šablona umožňuje vašemu pracovnímu prostoru komunikovat s ACR prostřednictvím privátního odkazu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "keyVaultArmId": {
      "type": "string"
      },
      "workspaceName": {
      "type": "string"
      },
      "containerRegistryArmId": {
      "type": "string"
      },
      "applicationInsightsArmId": {
      "type": "string"
      },
      "storageAccountArmId": {
      "type": "string"
      },
      "location": {
      "type": "string"
      }
  },
  "resources": [
      {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "identity": {
          "type": "SystemAssigned"
      },
      "sku": {
          "tier": "enterprise",
          "name": "enterprise"
      },
      "properties": {
          "sharedPrivateLinkResources":
  [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
          "keyVault": "[parameters('keyVaultArmId')]",
          "containerRegistry": "[parameters('containerRegistryArmId')]",
          "applicationInsights": "[parameters('applicationInsightsArmId')]",
          "storageAccount": "[parameters('storageAccountArmId')]"
      }
      }
  ]
}
```

## <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

<a id="cmkaapl"></a>
### <a name="workspace-with-customer-managed-keys-and-auto-approval-for-private-link"></a>Pracovní prostor s klíči spravovanými zákazníkem a automatickým schválením pro privátní propojení

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "eastus",
        "southcentralus",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku":{
      "type": "string",
      "defaultValue": "basic",
      "allowedValues": [
        "basic",
        "enterprise"
      ],
      "metadata": {
        "description": "Specifies the sku, also referred as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "hbi_workspace":{
      "type": "string",
      "defaultValue": "false",
      "allowedValues": [
        "false",
        "true"
      ],
      "metadata": {
        "description": "Specifies that the Azure Machine Learning workspace holds highly confidential data."
      }
    },
    "encryption_status":{
      "type": "string",
      "defaultValue": "Disabled",
      "allowedValues": [
        "Enabled",
        "Disabled"
      ],
      "metadata": {
        "description": "Specifies if the Azure Machine Learning workspace should be encrypted with customer managed key."
      }
    },
    "cmk_keyvault":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyVault id."
      }
    },
    "resource_cmk_uri":{
      "type": "string",
      "metadata": {
        "description": "Specifies if the customer managed keyvault key uri."
      }
    },
    "subnetName": {
        "type": "string"
      },
      "vnetName": {
        "type": "string"
      }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
    "privateDnsGuid": "[guid(resourceGroup().id, deployment().name)]"    
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2018-02-14",
      "name": "[variables('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "tenantId": "[variables('tenantId')]",
        "sku": {
          "name": "standard",
          "family": "A"
        },
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2018-05-01-preview",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2020-01-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
      ],
      "identity": {
        "type": "systemAssigned"
      },
      "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
      },
      "properties": {
        "friendlyName": "[parameters('workspaceName')]",
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]",
         "encryption": {
                "status": "[parameters('encryption_status')]",
                "keyVaultProperties": {
                    "keyVaultArmId": "[parameters('cmk_keyvault')]",
                    "keyIdentifier": "[parameters('resource_cmk_uri')]"
                  }
            },
        "hbi_workspace": "[parameters('hbi_workspace')]"
      }
    },
    {
        "type": "Microsoft.Network/virtualNetworks",
        "apiVersion": "2019-09-01",
        "name": "[parameters('vnetName')]",
        "location": "[parameters('location')]",
        "properties": {
            "addressSpace": {
                "addressPrefixes": [
                    "10.0.0.0/27"
                ]
            },
            "virtualNetworkPeerings": [],
            "enableDdosProtection": false,
            "enableVmProtection": false
        }
      },
    {
        "type": "Microsoft.Network/virtualNetworks/subnets",
        "apiVersion": "2019-09-01",
        "name": "[concat(parameters('vnetName'), '/', parameters('subnetName'))]",
        "dependsOn": [
            "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))]"
        ],
        "properties": {
            "addressPrefix": "10.0.0.0/27",
            "delegations": [],
            "privateEndpointNetworkPolicies": "Disabled",
            "privateLinkServiceNetworkPolicies": "Enabled"
        }
    },
    {
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
      "type": "Microsoft.Network/privateEndpoints",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
      ],
      "properties": {
        "privateLinkServiceConnections": [
          {
            "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
              "groupIds": [
                "amlworkspace"
              ]
            }
          }
        ],
        "manualPrivateLinkServiceConnections": [],
        "subnet": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "[concat('PrivateDns-', variables('privateDnsGuid'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateEndpoints', concat(parameters('workspaceName'), '-PrivateEndpoint'))]"
      ],
      "properties": {
          "mode": "Incremental",
          "template": {
              "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
              "contentVersion": "1.0.0.0",
              "resources": [
                  {
                      "type": "Microsoft.Network/privateDnsZones",
                      "apiVersion": "2018-09-01",
                      "name": "privatelink.api.azureml.ms",
                      "location": "global",
                      "tags": {},
                      "properties": {}
                  },
                  {
                      "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
                      "apiVersion": "2018-09-01",
                      "name": "[concat('privatelink.api.azureml.ms', '/', uniqueString(resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))))]",
                      "location": "global",
                      "dependsOn": [
                          "privatelink.api.azureml.ms"
                      ],
                      "properties": {
                          "virtualNetwork": {
                              "id": "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))]"
                          },
                          "registrationEnabled": false
                      }
                  },
                  {
                      "apiVersion": "2017-05-10",
                      "name": "[concat('EndpointDnsRecords-', variables('privateDnsGuid'))]",
                      "type": "Microsoft.Resources/deployments",
                      "dependsOn": [
                          "privatelink.api.azureml.ms"
                      ],
                      "properties": {
                          "mode": "Incremental",
                          "templatelink": {
                              "contentVersion": "1.0.0.0",
                              "uri": "https://network.hosting.portal.azure.net/network/Content/4.13.392.925/DeploymentTemplates/PrivateDnsForPrivateEndpoint.json"
                          },
                          "parameters": {
                              "privateDnsName": {
                                  "value": "privatelink.api.azureml.ms"
                              },
                              "privateEndpointNicResourceId": {
                                  "value": "[reference(resourceId('Microsoft.Network/privateEndpoints',concat(parameters('workspaceName'), '-PrivateEndpoint'))).networkInterfaces[0].id]"
                              },
                              "nicRecordsTemplateUri": {
                                  "value": "https://network.hosting.portal.azure.net/network/Content/4.13.392.925/DeploymentTemplates/PrivateDnsForPrivateEndpointNic.json"
                              },
                              "ipConfigRecordsTemplateUri": {
                                  "value": "https://network.hosting.portal.azure.net/network/Content/4.13.392.925/DeploymentTemplates/PrivateDnsForPrivateEndpointIpConfig.json"
                              },
                              "uniqueId": {
                                  "value": "[variables('privateDnsGuid')]"
                              },
                              "existingRecords": {
                                  "value": {}
                              }
                          }
                      }
                  }
              ]
          }
      },
      "resourceGroup": "[resourceGroup().name]"
  }
  ]
}
```

<a id="cmkmapl"></a>
### <a name="workspace-with-customer-managed-keys-and-manual-approval-for-private-link"></a>Pracovní prostor s klíči spravovanými zákazníkem a ručním schválením pro privátní propojení

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "eastus",
        "southcentralus",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku":{
      "type": "string",
      "defaultValue": "basic",
      "allowedValues": [
        "basic",
        "enterprise"
      ],
      "metadata": {
        "description": "Specifies the sku, also referred as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "hbi_workspace":{
      "type": "string",
      "defaultValue": "false",
      "allowedValues": [
        "false",
        "true"
      ],
      "metadata": {
        "description": "Specifies that the Azure Machine Learning workspace holds highly confidential data."
      }
    },
    "encryption_status":{
      "type": "string",
      "defaultValue": "Disabled",
      "allowedValues": [
        "Enabled",
        "Disabled"
      ],
      "metadata": {
        "description": "Specifies if the Azure Machine Learning workspace should be encrypted with customer managed key."
      }
    },
    "cmk_keyvault":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyVault id."
      }
    },
    "resource_cmk_uri":{
      "type": "string",
      "metadata": {
        "description": "Specifies if the customer managed keyvault key uri."
      }
    },
    "subnetName": {
        "type": "string"
      },
      "vnetName": {
        "type": "string"
      }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2018-02-14",
      "name": "[variables('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "tenantId": "[variables('tenantId')]",
        "sku": {
          "name": "standard",
          "family": "A"
        },
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2018-05-01-preview",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2020-01-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
      ],
      "identity": {
        "type": "systemAssigned"
      },
      "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
      },
      "properties": {
        "friendlyName": "[parameters('workspaceName')]",
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]",
         "encryption": {
                "status": "[parameters('encryption_status')]",
                "keyVaultProperties": {
                    "keyVaultArmId": "[parameters('cmk_keyvault')]",
                    "keyIdentifier": "[parameters('resource_cmk_uri')]"
                  }
            },
        "hbi_workspace": "[parameters('hbi_workspace')]"
      }
    },
    {
        "type": "Microsoft.Network/virtualNetworks",
        "apiVersion": "2019-09-01",
        "name": "[parameters('vnetName')]",
        "location": "[parameters('location')]",
        "properties": {
            "addressSpace": {
                "addressPrefixes": [
                    "10.0.0.0/27"
                ]
            },
            "virtualNetworkPeerings": [],
            "enableDdosProtection": false,
            "enableVmProtection": false
        }
      },
    {
        "type": "Microsoft.Network/virtualNetworks/subnets",
        "apiVersion": "2019-09-01",
        "name": "[concat(parameters('vnetName'), '/', parameters('subnetName'))]",
        "dependsOn": [
            "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))]"
        ],
        "properties": {
            "addressPrefix": "10.0.0.0/27",
            "delegations": [],
            "privateEndpointNetworkPolicies": "Disabled",
            "privateLinkServiceNetworkPolicies": "Enabled"
        }
    },
    {
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
      "type": "Microsoft.Network/privateEndpoints",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
      ],
      "properties": {
        "privateLinkServiceConnections": [],
        "manualPrivateLinkServiceConnections": [
          {
            "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
              "groupIds": [
                "amlworkspace"
              ]
            }
          }
        ],
        "subnet": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
        }
      }
    }
  ]
}
```

<a id="mmkaapl"></a>
### <a name="workspace-with-microsoft-managed-keys-and-auto-approval-for-private-link"></a>Pracovní prostor s klíči spravovanými Microsoftem a automatickým schválením pro privátní propojení

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "eastus",
        "southcentralus",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku": {
      "type": "string",
      "defaultValue": "basic",
      "allowedValues": [
        "basic",
        "enterprise"
      ],
      "metadata": {
        "description": "Specifies the sku, also referred as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "subnetName": {
      "type": "string"
    },
    "vnetName": {
      "type": "string"
    }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
    "privateDnsGuid": "[guid(resourceGroup().id, deployment().name)]"
    
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2018-02-14",
      "name": "[variables('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "tenantId": "[variables('tenantId')]",
        "sku": {
          "name": "standard",
          "family": "A"
        },
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2018-05-01-preview",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
      ],
      "identity": {
        "type": "systemAssigned"
      },
      "sku": {
        "tier": "[parameters('sku')]",
        "name": "[parameters('sku')]"
      },
      "properties": {
        "friendlyName": "[parameters('workspaceName')]",
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
      }
    },
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vnetName')]",
      "location": "[parameters('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "10.0.0.0/27"
          ]
        },
        "virtualNetworkPeerings": [],
        "enableDdosProtection": false,
        "enableVmProtection": false
      }
    },
    {
      "type": "Microsoft.Network/virtualNetworks/subnets",
      "apiVersion": "2019-09-01",
      "name": "[concat(parameters('vnetName'), '/', parameters('subnetName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))]"
      ],
      "properties": {
        "addressPrefix": "10.0.0.0/27",
        "delegations": [],
        "privateEndpointNetworkPolicies": "Disabled",
        "privateLinkServiceNetworkPolicies": "Enabled"
      }
    },
    {
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
      "type": "Microsoft.Network/privateEndpoints",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
      ],
      "properties": {
        "privateLinkServiceConnections": [
          {
            "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
              "groupIds": [
                "amlworkspace"
              ]
            }
          }
        ],
        "subnet": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "[concat('PrivateDns-', variables('privateDnsGuid'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateEndpoints', concat(parameters('workspaceName'), '-PrivateEndpoint'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Network/privateDnsZones",
              "apiVersion": "2018-09-01",
              "name": "privatelink.api.azureml.ms",
              "location": "global",
              "tags": {},
              "properties": {}
            },
            {
              "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
              "apiVersion": "2018-09-01",
              "name": "[concat('privatelink.api.azureml.ms', '/', uniqueString(resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))))]",
              "location": "global",
              "dependsOn": [
                "privatelink.api.azureml.ms"
              ],
              "properties": {
                "virtualNetwork": {
                  "id": "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))]"
                },
                "registrationEnabled": false
              }
            },
            {
              "apiVersion": "2017-05-10",
              "name": "[concat('EndpointDnsRecords-', variables('privateDnsGuid'))]",
              "type": "Microsoft.Resources/deployments",
              "dependsOn": [
                "privatelink.api.azureml.ms"
              ],
              "properties": {
                "mode": "Incremental",
                "templatelink": {
                  "contentVersion": "1.0.0.0",
                  "uri": "https://network.hosting.portal.azure.net/network/Content/4.13.392.925/DeploymentTemplates/PrivateDnsForPrivateEndpoint.json"
                },
                "parameters": {
                  "privateDnsName": {
                    "value": "privatelink.api.azureml.ms"
                  },
                  "privateEndpointNicResourceId": {
                    "value": "[reference(resourceId('Microsoft.Network/privateEndpoints',concat(parameters('workspaceName'), '-PrivateEndpoint'))).networkInterfaces[0].id]"
                  },
                  "nicRecordsTemplateUri": {
                    "value": "https://network.hosting.portal.azure.net/network/Content/4.13.392.925/DeploymentTemplates/PrivateDnsForPrivateEndpointNic.json"
                  },
                  "ipConfigRecordsTemplateUri": {
                    "value": "https://network.hosting.portal.azure.net/network/Content/4.13.392.925/DeploymentTemplates/PrivateDnsForPrivateEndpointIpConfig.json"
                  },
                  "uniqueId": {
                    "value": "[variables('privateDnsGuid')]"
                  },
                  "existingRecords": {
                    "value": {}
                  }
                }
              }
            }
          ]
        }
      },
      "resourceGroup": "[resourceGroup().name]"
    }
  ]
}
```

<a id="mmkmapl"></a>
### <a name="workspace-with-microsoft-managed-keys-and-manual-approval-for-private-link"></a>Pracovní prostor s klíči spravovanými Microsoftem a ručním schválením pro privátní propojení

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "eastus",
        "southcentralus",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku": {
      "type": "string",
      "defaultValue": "basic",
      "allowedValues": [
        "basic",
        "enterprise"
      ],
      "metadata": {
        "description": "Specifies the sku, also referred as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "subnetName": {
      "type": "string"
    },
    "vnetName": {
      "type": "string"
    }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2018-02-14",
      "name": "[variables('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "tenantId": "[variables('tenantId')]",
        "sku": {
          "name": "standard",
          "family": "A"
        },
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2018-05-01-preview",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
      ],
      "identity": {
        "type": "systemAssigned"
      },
      "sku": {
        "tier": "[parameters('sku')]",
        "name": "[parameters('sku')]"
      },
      "properties": {
        "friendlyName": "[parameters('workspaceName')]",
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
      }
    },
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vnetName')]",
      "location": "[parameters('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "10.0.0.0/27"
          ]
        },
        "virtualNetworkPeerings": [],
        "enableDdosProtection": false,
        "enableVmProtection": false
      }
    },
    {
      "type": "Microsoft.Network/virtualNetworks/subnets",
      "apiVersion": "2019-09-01",
      "name": "[concat(parameters('vnetName'), '/', parameters('subnetName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))]"
      ],
      "properties": {
        "addressPrefix": "10.0.0.0/27",
        "delegations": [],
        "privateEndpointNetworkPolicies": "Disabled",
        "privateLinkServiceNetworkPolicies": "Enabled"
      }
    },
    {
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
      "type": "Microsoft.Network/privateEndpoints",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
      ],
      "properties": {
        "privateLinkServiceConnections": [],
        "manualPrivateLinkServiceConnections": [
          {
            "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
              "groupIds": [
                "amlworkspace"
              ]
            }
          }
        ],
        "subnet": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Další kroky

Další informace o zabezpečení Azure Machine Learning pracovního prostoru najdete v článku o [podnikovém zabezpečení](concept-enterprise-security.md) .
