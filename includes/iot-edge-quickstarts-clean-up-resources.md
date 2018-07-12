---
title: zahrnout soubor
description: zahrnout soubor
services: iot-edge
author: wesmc7777
ms.service: iot-edge
ms.topic: include
ms.date: 06/26/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: c0b9f9e9808de90df84edf2d3c409a921629baee
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38728301"
---
Pokud budete pokračovat k dalšímu doporučenému článku, můžete už vytvořené prostředky a konfigurace zachovat a znovu je použít.

Pokud nebudete pokračovat, můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům. 

> [!IMPORTANT]
> Odstranění prostředků Azure a skupiny prostředků je nevratná akce. V případě odstranění se skupina prostředků i všechny prostředky, které obsahuje, trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste službu IoT Hub vytvořili uvnitř existující skupiny prostředků obsahující prostředky, které chcete zachovat, odstraňte místo skupiny prostředků pouze samotný prostředek služby IoT.
>

Pokud chcete odstranit jenom IoT Hub, nahraďte v následujícím příkazu `<YourIoTHub>` názvem centra a `<TestResources>` názvem skupiny prostředků a potom ho spusťte:

```azurecli-interactive
az iot hub delete --name <YourIoTHub> --resource-group <TestResources>
```


Odstranění celé skupiny prostředků podle názvu:

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com) a klikněte na **Skupiny prostředků**.

2. Do textového pole **Filtrovat podle názvu...** zadejte název skupiny prostředků obsahující vaši službu IoT Hub. 

3. V seznamu výsledků klikněte na **...** napravo od vaší skupiny prostředků a pak na **Odstranit skupinu prostředků**.

    ![Odstranění](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)

4. Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Potvrďte odstranění tím, že znovu zadáte název vaší skupiny prostředků, a pak klikněte na **Odstranit**. Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.






