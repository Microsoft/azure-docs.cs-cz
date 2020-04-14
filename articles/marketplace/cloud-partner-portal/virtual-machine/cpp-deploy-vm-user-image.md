---
title: Nasazení virtuálního počítače Azure z uživatelského virtuálního pevného disku | Azure Marketplace
description: Vysvětluje, jak nasadit image virtuálního pevného disku uživatele k vytvoření instance virtuálního počítače Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 79754b4ce7c3dfe2a5c549f4a39ef3160be423d8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273881"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>Nasazení virtuálního počítače Azure z uživatelského virtuálního pevného disku

> [!IMPORTANT]
> dubna 2020 začneme přesouvat správu nabídek virtuálního počítače Azure do Centra partnerů. Po migraci vytvoříte a spravujete nabídky v Centru partnerů. Podle pokynů v [certifikaci image virtuálního počítače Azure](https://aks.ms/CertifyVMimage) spravujte migrované nabídky.

Tento článek vysvětluje, jak nasadit zobecněnou image virtuálního pevného disku k vytvoření nového prostředku virtuálního počítače Azure pomocí dodané šablony Azure Resource Manager a skriptu Azure PowerShell.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vhd-deployment-template"></a>Šablona nasazení VHD

Zkopírujte šablonu Azure Resource Manager pro nasazení `VHDtoImage.json` [virtuálního pevného disku](cpp-deploy-json-template.md) do místního souboru s názvem .  Upravte tento soubor a zadejte hodnoty pro následující parametry. 

|  **Parametr**             |   **Popis**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | Název existující skupiny prostředků Azure.  Obvykle používejte stejný RG přidružený k trezoru klíčů  |
| Soubor šablony               | Úplný název cesty k souboru`VHDtoImage.json`                                    |
| userStorageAccountName     | Název účtu úložiště                                                    |
| sNameForPublicIP           | Název DNS pro veřejnou IP adresu. Musí být malá písmena                                  |
| subscriptionId             | Identifikátor předplatného Azure                                                  |
| Umístění                   | Standardní geografické umístění Azure skupiny prostředků                       |
| vmName                     | Název virtuálního počítače                                                    |
| název trezoru                  | Název trezoru klíčů                                                          |
| vaultResourceGroup         | Skupina prostředků trezoru klíčů
| certificateUrl             | Url certifikátu, včetně verze uložené v trezoru klíčů, například:`https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
| vhdUrl                     | Adresa URL virtuálního pevného disku                                                   |
| vmSize                     | Velikost instance virtuálního počítače                                           |
| publicIPAddressName        | Název veřejné IP adresy                                                  |
| virtualNetworkName         | Název virtuální sítě                                                    |
| nicNázev                    | Název karty síťového rozhraní pro virtuální síť                     |
| adminUserName              | Uživatelské jméno účtu správce                                          |
| adminPassword              | Heslo správce                                                          |
|  |  |


## <a name="powershell-script"></a>Powershellový skript

Zkopírujte a upravte následující skript `$storageaccount` `$vhdUrl` pro zadání hodnot pro proměnné a.  Spusťte jej k vytvoření prostředku virtuálního počítače Azure z existujícího generalizovaného virtuálního pevného disku.

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

Po nasazení virtuálního počítače jste připraveni [certifikovat image virtuálního počítače](./cpp-certify-vm.md).
