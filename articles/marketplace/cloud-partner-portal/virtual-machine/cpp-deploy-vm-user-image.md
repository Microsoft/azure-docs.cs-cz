---
title: Nasazení virtuálního počítače Azure od uživatele virtuálního pevného disku | Dokumentace Microsoftu
description: Vysvětluje, jak nasadit uživatelské image virtuálního pevného disku pro vytvoření instance virtuálního počítače Azure.
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
ms.openlocfilehash: 9c163ddf7859246fcdaa28edfd4b598a24a32be2
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53196664"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>Nasazení virtuálního počítače Azure od uživatele virtuálního pevného disku

Tento článek vysvětluje, jak nasadit image generalizovaného virtuálního pevného disku vytvořit nový prostředek virtuálního počítače Azure, pomocí zadané šablony Azure Resource Manageru a Azure powershellu.


## <a name="vhd-deployment-template"></a>Šablona pro nasazení virtuálního pevného disku

Kopírování šablony Azure Resource Manageru pro [nasazení virtuálního pevného disku](cpp-deploy-json-template.md) do místního souboru s názvem `VHDtoImage.json`.  Tento soubor k poskytnutí hodnot pro tyto parametry upravte. 

|  **Parametr**             |   **Popis**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | Název existující skupiny prostředků Azure.  Obvykle používají stejné replikační skupině přidružené k trezoru klíčů  |
| TemplateFile               | Úplná cesta k souboru `VHDtoImage.json`                                    |
| userStorageAccountName     | Název účtu úložiště                                                    |
| sNameForPublicIP           | Název DNS pro veřejné IP adresy. Musí obsahovat malá písmena                                  |
| subscriptionId             | Identifikátor předplatného Azure                                                  |
| Umístění                   | Standardní Azure zeměpisné umístění skupiny prostředků                       |
| vmName                     | Název virtuálního počítače                                                    |
| VaultName                  | Název trezoru klíčů.                                                          |
| vaultResourceGroup         | Skupina prostředků trezoru klíčů.
| certificateUrl             | Adresa URL certifikátu, včetně verze uložené v trezoru klíčů, například:  https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7 |
| vhdUrl                     | Adresa URL virtuálního pevného disku                                                   |
| vmSize                     | Velikost instance virtuálního počítače                                           |
| publicIPAddressName        | Název veřejné IP adresy                                                  |
| virtualNetworkName         | Název virtuální sítě                                                    |
| nicName                    | Název síťové karty pro virtuální síť                     |
| adminUserName              | Uživatelské jméno účtu správce                                          |
| adminPassword              | Heslo správce                                                          |
|  |  |


## <a name="powershell-script"></a>Skript prostředí PowerShell

Zkopírujte a upravte následující skript a zadejte hodnoty pro `$storageaccount` a `$vhdUrl` proměnné.  Spusťte ji a vytvoří prostředek virtuálního počítače Azure z vaší existující generalizovaného virtuálního pevného disku.

```powershell
# storage account of existing generalized VHD 
$storageaccount = "testwinrm11815"

# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzureRMResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

#deploying VM with existing VHD
New-AzureRMResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd 

```

## <a name="next-steps"></a>Další postup

Po nasazení virtuálního počítače, jste připraveni [certifikaci vaší image virtuálního počítače](./cpp-certify-vm.md).
