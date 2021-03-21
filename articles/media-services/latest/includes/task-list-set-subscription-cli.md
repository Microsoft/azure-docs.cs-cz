---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: aa76f7b85302651f6874747610c3355f0572a7ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94885070"
---
<!-- List and set subscriptions -->

1. Získejte seznam vašich předplatných pomocí příkazu [az account list](/cli/azure/account#az-account-list):

    ```
    az account list --output table
    ```

2. Použijte příkaz `az account set` s názvem nebo ID předplatného, na které chcete přepnout.

    ```
    az account set --subscription "My Demos"
    ```
