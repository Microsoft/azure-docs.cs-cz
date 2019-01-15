---
title: Nasazení virtuálního počítače na webu Azure Marketplace | Dokumentace Microsoftu
description: Vysvětluje, jak nasadit virtuální počítač z Azure Marketplace předem nakonfigurovaného virtuálního počítače.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/29/2018
ms.author: pbutlerm
ms.openlocfilehash: 3ec4e0b047e94dc7481c51390c6e4370fdea5efa
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258794"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Nasazení virtuálního počítače z Azure Marketplace

Tento článek vysvětluje, jak nasadit předem nakonfigurovaného virtuálního počítače (VM) Azure Marketplace, pomocí dodávaného skriptu Azure Powershellu.  Tento skript také poskytuje koncové body služby WinRM HTTP a HTTPS na virtuálním počítači.  Skript vyžaduje, abyste už měli certifikát odeslán do služby Azure Key Vault, jak je popsáno v [vytváření certifikátů pro Azure Key Vault](./cpp-create-key-vault-cert.md). 


## <a name="vm-deployment-template"></a>Nasazení šablony virtuálního počítače

Šablona nasazení virtuálního počítače Azure quickstart, je k dispozici jako soubor online [azuredeploy.json](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json).  Obsahuje následující parametry:

|  **Parametr**        |   **Popis**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName | Název účtu úložiště                       |
| dnsNameForPublicIP    | Název DNS pro veřejné IP adresy. Musí obsahovat malá písmena.    |
| adminUserName         | Uživatelské jméno správce                          |
| adminPassword         | Heslo správce.                          |
| imagePublisher        | Vydavatel Image                                   |
| imageOffer            | Nabídka Image                                       |
| imageSKU              | Image SKU                                         |
| vmSize                | Velikost virtuálního počítače                                    |
| vmName                | Název virtuálního počítače                                    |
| VaultName             | Název trezoru klíčů.                             |
| vaultResourceGroup    | Skupina prostředků trezoru klíčů.                   |
| certificateUrl        | Adresa URL pro certifikát, včetně verze v trezoru klíčů, například  `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
|  |  |


## <a name="deployment-script"></a>Skript nasazení

Upravte následující skript prostředí Azure PowerShell a spusťte ho k nasazení zadaného virtuálního počítače Azure Marketplace.

```powershell

New-AzureRmResourceGroupDeployment -Name "dplvm$postfix" -ResourceGroupName "$rgName" -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json" -newStorageAccountName "test$postfix" -dnsNameForPublicIP $vmName -adminUserName "isv" -adminPassword $pwd -vmSize "Standard_A2" -vmName $vmName -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id 

```


## <a name="next-steps"></a>Další postup

Po nasazení předem nakonfigurovaných virtuálních počítačů můžete nakonfigurovat a přístup k řešení a služeb, které obsahuje nebo ho použít pro další vývoj. 
