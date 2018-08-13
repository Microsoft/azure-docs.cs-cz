---
title: zahrnout soubor
description: zahrnout soubor
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/26/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 6502ea1733e37e06172833c944c58101b3c049f2
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043716"
---
Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít.

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům. 

> [!IMPORTANT]
> Odstranění prostředků Azure a skupin prostředků je nevratná akce. V případě odstranění se skupina prostředků i všechny prostředky, které obsahuje, trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste službu IoT Hub vytvořili uvnitř existující skupiny prostředků obsahující prostředky, které chcete zachovat, odstraňte místo skupiny prostředků pouze samotný prostředek služby IoT Hub.
>

Pokud chcete odstranit jenom centrum IoT, spusťte následující příkaz. Místo \<YourIoTHub> zadejte název vašeho centra IoT a místo \<TestResources> název vaší skupiny prostředků:

```azurecli-interactive
az iot hub delete --name <YourIoTHub> --resource-group <TestResources>
```


Odstranění celé skupiny prostředků podle názvu:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a potom vyberte **Skupiny prostředků**.

2. Do textového pole **Filtrovat podle názvu** zadejte název skupiny prostředků obsahující vaši službu IoT Hub. 

3. Vpravo vedle skupiny prostředků ve výsledcích hledání vyberte tři tečky (**...**) a pak vyberte **Odstranit skupinu prostředků**.

    ![Odstranění skupiny prostředků](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)

4. Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Potvrďte odstranění opětovným zadáním názvu vaší skupiny prostředků a vyberte **Odstranit**. Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.






