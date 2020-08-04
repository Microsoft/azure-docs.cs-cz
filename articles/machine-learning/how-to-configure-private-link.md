---
title: Konfigurace privátního koncového bodu (Preview)
titleSuffix: Azure Machine Learning
description: Pomocí privátního odkazu Azure získáte zabezpečený přístup k pracovnímu prostoru Azure Machine Learning z virtuální sítě.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/28/2020
ms.openlocfilehash: dcf99b2a2a762114db45a9371a489b952b57d430
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542174"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Konfigurace privátního odkazu Azure pro pracovní prostor Azure Machine Learning (Preview)

V tomto dokumentu se dozvíte, jak pomocí privátního propojení Azure s vaším pracovním prostorem Azure Machine Learning. 

> [!IMPORTANT]
> Používání privátního odkazu Azure s Azure Machine Learning pracovním prostorem je v současnosti ve verzi Public Preview. Tato funkce je k dispozici pouze v oblastech **USA – východ** a **USA – západ 2** . Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Privátní odkaz Azure umožňuje připojit se k pracovnímu prostoru pomocí privátního koncového bodu. Soukromý koncový bod je sada privátních IP adres v rámci vaší virtuální sítě. Přístup k pracovnímu prostoru pak můžete omezit tak, aby se nacházet jenom přes privátní IP adresy. Soukromý odkaz pomáhá snižovat riziko exfiltrace dat. Další informace o privátních koncových bodech najdete v článku věnovaném [privátním odkazům Azure](/azure/private-link/private-link-overview) .

> [!IMPORTANT]
> Privátní propojení Azure neovlivňuje plochu ovládacího prvku Azure (operace správy), jako je například odstranění pracovního prostoru nebo Správa výpočetních prostředků. Například vytvoření, aktualizace nebo odstranění cíle služby Compute. Tyto operace se provádějí na veřejném Internetu jako normální.
>
> V pracovním prostoru, kde je povolený privátní odkaz, se nepodporuje Azure Machine Learning výpočetních instancí Preview.
>
> Pokud používáte Mozilla Firefox, může dojít k potížím při pokusu o přístup k privátnímu koncovému bodu pro váš pracovní prostor. Tento problém může souviset s DNS přes HTTPS v Mozilla. Jako alternativní řešení doporučujeme používat Microsoft Edge Google Chrome.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Vytvoření pracovního prostoru, který používá privátní koncový bod

> [!IMPORTANT]
> V současné době podporujeme při vytváření nového pracovního prostoru Azure Machine Learning jenom povolení privátního koncového bodu.

[https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced)Dá se použít k vytvoření pracovního prostoru s privátním koncovým bodem.

Informace o použití této šablony, včetně privátních koncových bodů, najdete v tématu [použití šablony Azure Resource Manager k vytvoření pracovního prostoru pro Azure Machine Learning](how-to-create-workspace-template.md).

## <a name="using-a-workspace-over-a-private-endpoint"></a>Použití pracovního prostoru v rámci privátního koncového bodu

Vzhledem k tomu, že komunikace s pracovním prostorem je povolená jenom z virtuální sítě, musí být všechna vývojová prostředí, která používají pracovní prostory, členy virtuální sítě. Například virtuální počítač ve virtuální síti.

> [!IMPORTANT]
> Aby nedošlo k dočasnému přerušení připojení, společnost Microsoft doporučuje po povolení privátního odkazu vyprázdnit mezipaměť DNS na počítačích, které se připojují k pracovnímu prostoru. 

Informace o službě Azure Virtual Machines najdete v [dokumentaci k Virtual Machines](/azure/virtual-machines/).


## <a name="using-azure-storage"></a>S využitím Azure Storage

Pokud chcete zabezpečit účet Azure Storage používaný vaším pracovním prostorem, umístěte ho do virtuální sítě.

Informace o tom, jak vložit účet úložiště ve virtuální síti, najdete v tématu [použití účtu úložiště pro váš pracovní prostor](how-to-enable-virtual-network.md#use-a-storage-account-for-your-workspace).

> [!WARNING]
> Azure Machine Learning nepodporuje použití účtu Azure Storage s povoleným privátním odkazem.

## <a name="using-azure-key-vault"></a>Použití Azure Key Vault

Pokud chcete zabezpečit Azure Key Vault používané vaším pracovním prostorem, můžete ho buď umístit do virtuální sítě, nebo pro něj Povolit privátní propojení.

Informace o vložení trezoru klíčů ve virtuální síti najdete v tématu [použití instance trezoru klíčů v pracovním prostoru](how-to-enable-virtual-network.md#key-vault-instance).

Informace o povolení privátního odkazu pro Trezor klíčů najdete v tématu věnovaném [integraci Key Vault s privátním odkazem Azure](/azure/key-vault/private-link-service).

## <a name="using-azure-kubernetes-services"></a>Používání služeb Azure Kubernetes Services

Pokud chcete zabezpečit službu Azure Kubernetes, kterou používá váš pracovní prostor, umístěte ji do virtuální sítě. Další informace najdete v tématu [použití služeb Azure Kubernetes Services s vaším pracovním prostorem](how-to-enable-virtual-network.md#aksvnet).

Azure Machine Learning teď podporuje používání služby Azure Kubernetes s povoleným privátním odkazem.
Pokud chcete vytvořit privátní cluster AKS, postupujte podle dokumentů [zde](https://docs.microsoft.com/azure/aks/private-clusters) .

## <a name="azure-container-registry"></a>Azure Container Registry

Informace o zabezpečení Azure Container Registry v rámci virtuální sítě najdete v tématu [použití Azure Container Registry](how-to-enable-virtual-network.md#azure-container-registry).

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

## <a name="next-steps"></a>Další kroky

Další informace o zabezpečení Azure Machine Learning pracovního prostoru najdete v článku o [podnikovém zabezpečení](concept-enterprise-security.md) .
