---
title: Importovat kroky aplikace
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/30/2020
ms.author: diberry
ms.openlocfilehash: b7b8befa0f5871b65b9b5621bfb99c659bf07235
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422763"
---
1. Na [portálu Luis Preview](https://preview.luis.ai)na stránce **Moje aplikace** vyberte **+ Nová aplikace pro konverzaci**a pak **importovat jako JSON**. Vyhledejte uložený soubor JSON z předchozího kroku. Nemusíte měnit název aplikace. Vyberte **Hotovo** .

1. V části **Správa** na kartě **verze** vyberte `0.1` verzi, pak vyberte **klonovat** , aby se naklonoval verze, a zadejte nový název `ml-entity`o 10 znaků a potom vyberte **Hotovo** pro dokončení procesu klonování. Název verze je součástí cesty URL, a proto smí obsahovat jenom znaky, které jsou platné v adresách URL.

    > [!TIP]
    > Naklonování do nové verze je osvědčeným postupem před úpravou aplikace. Až dokončíte změnu verze, exportujte verzi (jako soubor. JSON nebo. Lu) a Prohlédněte si soubor do systému správy zdrojového kódu.

1. Vyberte **Build** a pak **záměry** , abyste viděli záměry, hlavní stavební kameny aplikace Luis.

    ![Změňte ze stránky verze na stránku záměry.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)