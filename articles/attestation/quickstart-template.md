---
title: Vytvoření certifikátu Azure Attestation pomocí šablony Azure Resource Manager
description: Naučte se vytvořit certifikát Azure Attestation pomocí šablony Azure Resource Manager.
services: azure-resource-manager
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mbaldwin
ms.date: 08/31/2020
ms.openlocfilehash: a73294e41f27a15d70ab95b3aa7b100cab5320b4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89237241"
---
# <a name="quickstart-create-an-azure-attestation-provider-with-an-arm-template"></a>Rychlý Start: vytvoření poskytovatele ověření identity Azure pomocí šablony ARM

[Ověření Microsoft Azure](overview.md) je řešení pro ověřování prostředí důvěryhodných spuštění (TEEs). Tento rychlý Start se zaměřuje na proces nasazení šablony Azure Resource Manager (šablona ARM) pro vytvoření Microsoft Azurech zásad ověřování identity.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)


## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-attestation-provider-create).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "name": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "tags": {
      "type": "Object"
    },
    "policySigningCertificates": {
      "type": "string"
    }
  },
  "variables": {
    "PolicySigningCertificates": {
      "PolicySigningCertificates": {
        "keys": [
          {
            "kty": "RSA",
            "use": "sig",
            "x5c": [ "[parameters('policySigningCertificates')]" ]
          }
        ]
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "[parameters('name')]",
      "location": "[parameters('location')]",
      "type": "Microsoft.Attestation/attestationProviders",
      "tags": "[parameters('tags')]",
      "Properties": "[if(empty(parameters('policySigningCertificates')), json('{}'), variables('PolicySigningCertificates'))]"
    }
  ]
}
```

Prostředky Azure definované v šabloně:
- Microsoft. Attestation/attestationProviders

## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující obrázek pro přihlášení do Azure a otevřete šablonu.

    [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

2. Vyberte nebo zadejte následující hodnoty.

    Pokud není zadaný, použijte k vytvoření poskytovatele ověření výchozí hodnotu.

    * **název**: vyberte název poskytovatele ověření identity Azure.
    * **umístění**: vyberte umístění. Například **USA – střed**.
    * **značky**: vyberte umístění. Například **USA – střed**.

3. Vyberte **Koupit**. Po úspěšném nasazení prostředku ověření se zobrazí oznámení:

K nasazení šablony se použije Azure Portal. Kromě Azure Portal můžete použít také Azure PowerShell, Azure CLI a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

K ověření prostředku ověření identity můžete použít Azure Portal.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další buildy Azure Attestation na tomto rychlém startu. Pokud chcete pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat.

Pokud už je nepotřebujete, odstraňte skupinu prostředků, která odstraní prostředek ověření identity. Odstranění skupiny prostředků pomocí rozhraní příkazového řádku Azure nebo Azure PowerShell:

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili prostředek ověření identity pomocí šablony ARM a nasazování ověřili. Další informace o ověření identity Azure najdete v tématu [Přehled ověřování Azure](overview.md).
