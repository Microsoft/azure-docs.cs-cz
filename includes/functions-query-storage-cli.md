---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4fe159660421113e0f0ac0586ae7e4a22d5bcda7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "77474150"
---
### <a name="query-the-storage-queue"></a>Dotazování fronty úložiště

Pomocí [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) příkazu můžete zobrazit fronty úložiště ve vašem účtu, jako v následujícím příkladu:

```azurecli-interactive
az storage queue list --output tsv
```

Výstup z tohoto příkazu zahrnuje frontu s názvem `outqueue` , což je fronta, která se vytvořila při spuštění funkce.

Dále pomocí [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) příkazu Zobrazte zprávy v této frontě, jako v tomto příkladu:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

Vrácený řetězec by měl být stejný jako zpráva, kterou jste odeslali k otestování funkce.

> [!NOTE]  
> Předchozí příklad dekóduje vrácený řetězec z formátu base64. Důvodem je to, že vazby úložiště fronty zapisují a čtou z Azure Storage jako [řetězce Base64](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding).