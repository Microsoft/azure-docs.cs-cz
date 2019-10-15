---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: ca576290ea511dc54b89ecebef72ca2a42e9169f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329544"
---
### <a name="query-the-storage-queue"></a>Dotazování fronty úložiště

Pomocí příkazu [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) můžete zobrazit fronty úložiště ve vašem účtu, jako v následujícím příkladu:

```azurecli-interactive
az storage queue list --output tsv
```

Výstup z tohoto příkazu zahrnuje frontu s názvem `outqueue`, což je fronta vytvořená při spuštění funkce.

Dále pomocí příkazu [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) Zobrazte zprávy v této frontě, jako v tomto příkladu:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

Vrácený řetězec by měl být stejný jako zpráva, kterou jste odeslali k otestování funkce.

> [!NOTE]  
> Předchozí příklad dekóduje vrácený řetězec z formátu base64. Důvodem je to, že vazby úložiště fronty zapisují a čtou z Azure Storage jako [řetězce Base64](../articles/azure-functions/functions-bindings-storage-queue.md#encoding).