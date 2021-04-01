---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: d1a67a131a94bc017eaf2199546ef08f0291cd54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244497"
---
Pojďme vytvořit tajný kód s názvem **mySecret** s hodnotou **Success!**. Tajný klíč může být heslo, připojovací řetězec SQL nebo jakékoli jiné informace, které potřebujete pro zajištění zabezpečení i k dispozici pro vaši aplikaci. 

K přidání tajného klíče do nově vytvořeného trezoru klíčů použijte příkaz Azure CLI [AZ Key trezor Secret set](/cli/azure/keyvault/secret#az-keyvault-secret-set) :

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```