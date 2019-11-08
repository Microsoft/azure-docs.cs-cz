---
title: Nasazení virtuálního počítače z Azure Marketplace
description: Vysvětluje, jak nasadit virtuální počítač z Azure Marketplace předem nakonfigurovaného virtuálního počítače.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 2888d7643fd4f624634dc2ec520bec6e753382f1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816827"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Nasazení virtuálního počítače z Azure Marketplace

Tento článek vysvětluje, jak nasadit předem nakonfigurovaný virtuální počítač z Azure Marketplace pomocí poskytnutého Azure PowerShell skriptu.  Tento skript také zpřístupňuje koncové body HTTP a HTTPS služby WinRM na virtuálním počítači.  Tento skript vyžaduje, abyste už certifikát nahráli do Azure Key Vault, jak je popsáno v tématu [Vytvoření certifikátů pro Azure Key Vault](./cpp-create-key-vault-cert.md). 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vm-deployment-template"></a>Šablona nasazení virtuálních počítačů

Šablona nasazení virtuálního počítače Azure v rychlém startu je dostupná jako online soubor [azuredeploy. JSON](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json).  Obsahuje následující parametry:

|  **Ukazatele**        |   **Popis**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName | Název účtu úložiště                       |
| dnsNameForPublicIP    | Název DNS pro veřejnou IP adresu. Musí být malými písmeny.    |
| adminUserName         | Uživatelské jméno správce                          |
| adminPassword         | Heslo správce                          |
| imagePublisher        | Vydavatel obrázku                                   |
| imageOffer            | Nabídka Image                                       |
| imageSKU              | SKU image                                         |
| vmSize                | Velikost virtuálního počítače                                    |
| vmName                | Název virtuálního počítače                                    |
| vaultName             | Název trezoru klíčů                             |
| vaultResourceGroup    | Skupina prostředků trezoru klíčů                   |
| certificateUrl        | Adresa URL certifikátu, včetně verze v trezoru klíčů, například `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
|  |  |


## <a name="deployment-script"></a>Skript nasazení

Upravte následující skript Azure PowerShell a spusťte ho pro nasazení zadaného Azure Marketplace virtuálního počítače.

```powershell

New-AzResourceGroupDeployment -Name "dplvm$postfix" -ResourceGroupName "$rgName" -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json" -newStorageAccountName "test$postfix" -dnsNameForPublicIP $vmName -adminUserName "isv" -adminPassword $pwd -vmSize "Standard_A2" -vmName $vmName -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id 

```


## <a name="next-steps"></a>Další kroky

Po nasazení předem nakonfigurovaného virtuálního počítače můžete nakonfigurovat a získat přístup k řešením a službám, které obsahuje, nebo je použít k dalšímu vývoji. 
