---
author: msmbaldwin
ms.service: key-vault
ms.subservice: B2C
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 826d5c2183ecc70908192695927890dcf06137b1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87013090"
---
Vytvořte zásady přístupu pro váš Trezor klíčů, který uděluje oprávnění k instančnímu objektu předáním `clientId` příkazu [AZ Key trezor set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) . Udělte instančnímu objektu získání, seznam a nastavení oprávnění pro klíče a tajné kódy.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```
