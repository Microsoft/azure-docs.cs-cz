---
title: Nasazení virtuálního počítače Azure z virtuálního pevného disku uživatele | Azure Marketplace
description: Vysvětluje, jak nasadit image virtuálního pevného disku (VHD) uživatele pro vytvoření instance virtuálního počítače Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: e3bad2dc63f6a75f52c537aabfa6e85d1846ef15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147938"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>Nasazení virtuálního počítače Azure z virtuálního pevného disku uživatele

> [!IMPORTANT]
> Od 13. dubna 2020 začneme přesouvat správu nabídek virtuálních počítačů Azure do partnerského centra. Po dokončení migrace vytvoříte a budete spravovat své nabídky v partnerském centru. Pokud chcete spravovat migrované nabídky, postupujte podle pokynů v tématu [certifikace imagí virtuálních počítačů Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-image-certification) .

Tento článek vysvětluje, jak nasadit zobecněnou image VHD pro vytvoření nového prostředku virtuálního počítače Azure pomocí dodané Azure Resource Manager šablony a Azure PowerShell skriptu.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vhd-deployment-template"></a>Šablona nasazení VHD

Zkopírujte šablonu Azure Resource Manager pro [nasazení VHD](cpp-deploy-json-template.md) do místního souboru s názvem `VHDtoImage.json`.  Úpravou tohoto souboru zadejte hodnoty pro následující parametry. 

|  **Ukazatele**             |   **Popis**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | Název existující skupiny prostředků Azure  Typicky používejte stejný RG přidružený k vašemu trezoru klíčů.  |
| TemplateFile               | Úplná cesta k souboru`VHDtoImage.json`                                    |
| userStorageAccountName     | Název účtu úložiště                                                    |
| sNameForPublicIP           | Název DNS pro veřejnou IP adresu. Musí být malá                                  |
| subscriptionId             | Identifikátor předplatného Azure                                                  |
| Umístění                   | Standardní zeměpisná poloha služby Azure pro skupinu prostředků                       |
| vmName                     | Název virtuálního počítače                                                    |
| vaultName                  | Název trezoru klíčů                                                          |
| vaultResourceGroup         | Skupina prostředků trezoru klíčů
| certificateUrl             | Adresa URL certifikátu, včetně verze uložené v trezoru klíčů, například:`https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
| vhdUrl                     | Adresa URL virtuálního pevného disku                                                   |
| vmSize                     | Velikost instance virtuálního počítače                                           |
| publicIPAddressName        | Název veřejné IP adresy                                                  |
| virtualNetworkName         | Název virtuální sítě                                                    |
| nicName                    | Název karty síťového rozhraní pro virtuální síť                     |
| adminUserName              | Uživatelské jméno účtu správce                                          |
| adminPassword              | Heslo správce                                                          |
|  |  |


## <a name="powershell-script"></a>Powershellový skript

Zkopírujte a upravte následující skript a zadejte hodnoty pro proměnné `$storageaccount` a. `$vhdUrl`  Spusťte ho k vytvoření prostředku virtuálního počítače Azure ze stávajícího zobecněného virtuálního pevného disku.

```powershell
# storage account of existing generalized VHD 
$storageaccount = "testwinrm11815"

# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

#deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd 

```

## <a name="next-steps"></a>Další kroky

Po nasazení virtuálního počítače budete připraveni k [certifikaci vaší image virtuálního počítače](./cpp-certify-vm.md).
