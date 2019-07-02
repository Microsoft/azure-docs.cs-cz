---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 1ea0b01997d3d5cecff73f951c51de5898c2f07a
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503466"
---
Následující příkazy Azure CLI se zřídit prostředek detekce anomálií v bezplatné cenové úrovně. Klikněte na **vyzkoušet** tlačítko, vložte kód a stiskněte klávesu enter pro spuštění ve službě Azure cloud shell. Vytiskne se vaše klíče pro ověřování vaší aplikace. Po dokončení [vytvořit proměnnou prostředí](../articles/cognitive-services/cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) pro vaše buď ze svých klíčů s názvem `ANOMALY_DETECTOR_KEY`.

> [!NOTE]
> * Volitelně můžete:
>    * vytvoření prostředků pomocí [webu Azure portal](../articles/cognitive-services/cognitive-services-apis-create-account.md), nebo [rozhraní příkazového řádku Azure](../articles/cognitive-services/cognitive-services-apis-create-account.md) na místním počítači.
>    * získání [zkušební klíč](https://azure.microsoft.com/try/cognitive-services/#decision) platný po dobu 7 dní zdarma. Po registraci se budou k dispozici na [web Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).
>    * Zobrazit tento prostředek na [webu Azure portal](https://portal.azure.com/). 

Služby cognitive Services jsou reprezentovány prostředky Azure, které zřizujete. Každý účet služeb Cognitive Services (a jeho přidružený prostředek Azure), musí patřit do skupiny prostředků Azure.

1. Vytvořte skupinu prostředků Azure

    ```azurecli-interactive
    az group create \
        --name example-anomaly-detector-resource-group \
        --location westus2
    ```

2. Vytvořte prostředek detekce anomálií na úrovni free

    ```azurecli-interactive
    az cognitiveservices account create \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group \
        --kind AnomalyDetector \
        --sku F0 \
        --location westus2 \
        --yes
    ```

3. Výpis klíčů pro prostředek

    ```azurecli-interactive
    az cognitiveservices account keys list \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group
    ```