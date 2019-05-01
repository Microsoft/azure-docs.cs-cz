---
title: Nasazení virtuálního počítače Azure od uživatele virtuálního pevného disku | Azure Marketplace
description: Vysvětluje, jak nasadit uživatelské image virtuálního pevného disku pro vytvoření instance virtuálního počítače Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: e4da523fa54a513fe77fda037aea0a5fd530250b
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938248"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>Nasazení virtuálního počítače Azure od uživatele virtuálního pevného disku

Tento článek vysvětluje, jak nasadit image generalizovaného virtuálního pevného disku vytvořit nový prostředek virtuálního počítače Azure, pomocí zadané šablony Azure Resource Manageru a Azure powershellu.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vhd-deployment-template"></a>Šablona pro nasazení virtuálního pevného disku

Kopírování šablony Azure Resource Manageru pro [nasazení virtuálního pevného disku](cpp-deploy-json-template.md) do místního souboru s názvem `VHDtoImage.json`.  Tento soubor k poskytnutí hodnot pro tyto parametry upravte. 

|  **Parametr**             |   **Popis**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | Název existující skupiny prostředků Azure.  Obvykle používají stejné replikační skupině přidružené k trezoru klíčů  |
| TemplateFile               | Úplná cesta k souboru `VHDtoImage.json`                                    |
| userStorageAccountName     | Název účtu úložiště                                                    |
| sNameForPublicIP           | Název DNS pro veřejné IP adresy. Musí obsahovat malá písmena                                  |
| subscriptionId             | Identifikátor předplatného Azure                                                  |
| Location                   | Standardní Azure zeměpisné umístění skupiny prostředků                       |
| vmName                     | Název virtuálního počítače                                                    |
| VaultName                  | Název trezoru klíčů.                                                          |
| vaultResourceGroup         | Skupina prostředků trezoru klíčů.
| certificateUrl             | Adresa URL certifikátu, včetně verze uložené v trezoru klíčů, například:  `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
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

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

#deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd 

```

## <a name="next-steps"></a>Další postup

Po nasazení virtuálního počítače, jste připraveni [certifikaci vaší image virtuálního počítače](./cpp-certify-vm.md).
