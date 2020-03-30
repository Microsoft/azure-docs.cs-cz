---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4fe159660421113e0f0ac0586ae7e4a22d5bcda7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77474150"
---
### <a name="query-the-storage-queue"></a>Dotaz na frontu úložiště

Pomocí příkazu [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) můžete zobrazit fronty úložiště ve vašem účtu, jako v následujícím příkladu:

```azurecli-interactive
az storage queue list --output tsv
```

Výstup z tohoto příkazu `outqueue`obsahuje frontu s názvem , což je fronta, která byla vytvořena při spuštění funkce.

Dále použijte [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) příkaz k zobrazení zpráv v této frontě, jako v tomto příkladu:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

Vrácený řetězec by měl být stejný jako zpráva, kterou jste odeslali k testování funkce.

> [!NOTE]  
> Předchozí příklad dekóduje vrácený řetězec z base64. Důvodem je, že vazby úložiště fronty zapisovat a číst z Azure Storage jako [base64 řetězce](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding).