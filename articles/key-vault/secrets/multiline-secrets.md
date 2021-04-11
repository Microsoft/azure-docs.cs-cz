---
title: Uložení víceřádkového tajného kódu do Azure Key Vault
description: Kurz ukazující způsob nastavení víceřádkových tajných klíčů z Azure Key Vault pomocí rozhraní příkazového řádku Azure a PowerShellu
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 03/17/2021
ms.author: mbaldwin
ms.openlocfilehash: e320795d5e8623dea9b97ac6371a0ffc6e6117f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104610280"
---
# <a name="store-a-multi-line-secret-in-azure-key-vault"></a>Uložení víceřádkového tajného kódu do Azure Key Vault

Rychlé zprovoznění a [Azure PowerShell](quick-create-powershell.md) v [Azure CLI](quick-create-cli.md) ukazují, jak uložit jednořádkový tajný klíč.   Key Vault můžete použít také k uložení víceřádkového tajného kódu, například souboru JSON nebo privátního klíče RSA.

Víceřádkové tajné kódy nelze předat do Azure CLI AZ klíčů [trezoru set](/cli/azure/keyvault/secret#az_keyvault_secret_set) nebo rutinu Azure PowerShell [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) pomocí příkazového řádku. Místo toho je nutné nejprve uložit víceřádkový tajný klíč jako textový soubor. 

Můžete například vytvořit textový soubor s názvem "secretfile.txt", který obsahuje následující řádky:

```bash
This is my
multi-line
secret
```

Pak můžete tento soubor předat do Azure CLI [AZ klíčů trezor Secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) pomocí `--file` parametru.

```azurecli-interactive
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "MultilineSecret" --file "secretfile.txt"
```

V Azure PowerShell musíte nejdřív přečíst soubor pomocí rutiny [Get-Content](/powershell/module/microsoft.powershell.management/get-content) a pak ho převést na zabezpečený řetězec pomocí [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring). 

```azurepowershell-interactive
$RawSecret =  Get-Content "secretfile.txt" -Raw
$SecureSecret = ConvertTo-SecureString -String $RawSecret -AsPlainText -Force
```

Nakonec tajný klíč uložíte pomocí rutiny [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) .

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "MultilineSecret" -SecretValue $SecureSecret
```

V obou případech můžete uložený tajný klíč zobrazit pomocí příkazu Azure CLI [AZ klíčů trezor tajné zobrazení](/cli/azure/keyvault/secret#az_keyvault_secret_show) nebo rutiny Azure PowerShell [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) .

```azurecli-interactive
az keyvault secret show --name "MultilineSecret" --vault-name "<your-unique-keyvault-name>" --query "value"
```

Tajný kód se vrátí s newlines Embedded:

```bash
"This is\nmy multi-line\nsecret"
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si [přehled Azure Key Vault](../general/overview.md)
- Další informace najdete v tématu [rychlý Start Azure CLI](quick-create-cli.md) .
- Další informace najdete v tématu [Azure CLI AZ klíčů trezor](/cli/azure/keyvault) .
- Další informace najdete v tématu [rychlý start Azure PowerShell](quick-create-powershell.md) .
- Přečtěte si [Azure PowerShell rutin AZ. klíčů trezor.](/powershell/module/az.keyvault#key-vault)
