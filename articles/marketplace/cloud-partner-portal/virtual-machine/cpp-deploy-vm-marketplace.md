---
title: Nasazení virtuálního počítače na webu Azure Marketplace | Dokumentace Microsoftu
description: Vysvětluje, jak nasadit virtuální počítač z předem nakonfigurovaného virtuálního počítače Azure Marketplace.
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
ms.openlocfilehash: d800d2a9c4eced2fa347658ecbb5b7a97031d997
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57838693"
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
