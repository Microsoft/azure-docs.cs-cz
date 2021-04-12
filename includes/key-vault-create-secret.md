---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6745d1f7d97a8f4d08078bf93b61762ab04aad46
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "106073065"
---
Pojďme vytvořit tajný kód s názvem **mySecret** s hodnotou **Success!**. Tajný klíč může být heslo, připojovací řetězec SQL nebo jakékoli jiné informace, které potřebujete pro zajištění zabezpečení i k dispozici pro vaši aplikaci. 

K přidání tajného klíče do nově vytvořeného trezoru klíčů použijte následující příkaz:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$secret = ConvertTo-SecureString -String 'Success!' -AsPlainText
Set-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret -SecretValue $secret
```
---