---
title: Nasazení virtuálního počítače z Azure Marketplace
description: Vysvětluje, jak nasadit virtuální počítač z předem nakonfigurovaného virtuálního počítače na Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 6e39f1c70cd94c14b12e54817941ea9106aacfdd
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273864"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Nasazení virtuálního počítače z Azure Marketplace

> [!IMPORTANT]
> dubna 2020 začneme přesouvat správu nabídek virtuálního počítače Azure do Centra partnerů. Po migraci vytvoříte a spravujete nabídky v Centru partnerů. Podle pokynů v [certifikaci image virtuálního počítače Azure](https://aks.ms/CertifyVMimage) spravujte migrované nabídky.

Tento článek vysvětluje, jak nasadit předem nakonfigurovaný virtuální počítač (VM) z Azure Marketplace pomocí zadaný skript Azure PowerShell.  Tento skript také zveřejňuje koncové body WinRM HTTP a HTTPS na virtuálním počítači.  Skript vyžaduje, abyste už měli certifikát nahraný do úložiště klíčů Azure Key Vault, jak je popsáno v [tématu Vytvořit certifikáty pro Azure Key Vault](./cpp-create-key-vault-cert.md). 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vm-deployment-template"></a>Šablona nasazení virtuálního počítače

Šablona nasazení virtuálního počítače Azure na rychlém startu je dostupná jako online soubor [azuredeploy.json](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json).  Obsahuje následující parametry:

|  **Parametr**        |   **Popis**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName    | Název účtu úložiště                       |
| adresa dnsNameForPublicIP    | Název DNS pro veřejnou IP adresu. Musí být malá písmena.    |
| adminUserName            | Uživatelské jméno správce                          |
| adminPassword            | Heslo správce                          |
| imagePublisher        | Vydavatel obrázků                                   |
| imageNabídka            | Nabídka obrázků                                       |
| imageSKU                | Skladová položka obrázku                                         |
| vmSize                | Velikost virtuálního počítače                                    |
| vmName                | Název virtuálního_                                    |
| název trezoru                | Název trezoru klíčů                             |
| vaultResourceGroup    | Skupina prostředků trezoru klíčů                   |
| certificateUrl        | Adresa URL certifikátu, včetně verze v KeyVault, například`https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
|  |  |


## <a name="deployment-script"></a>Skript nasazení

Upravte následující skript Azure PowerShellu a spusťte ho k nasazení zadaného virtuálního počítače Azure Marketplace.

```powershell

New-AzResourceGroupDeployment -Name "dplvm$postfix" -ResourceGroupName "$rgName" -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json" -newStorageAccountName "test$postfix" -dnsNameForPublicIP $vmName -adminUserName "isv" -adminPassword $pwd -vmSize "Standard_A2" -vmName $vmName -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id 

```


## <a name="next-steps"></a>Další kroky

Po nasazení předem nakonfigurovaného virtuálního počítače můžete nakonfigurovat a přistupovat k řešením a službám, které obsahuje, nebo jej použít pro další vývoj. 
