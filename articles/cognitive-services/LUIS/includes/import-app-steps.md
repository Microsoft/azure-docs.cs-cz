---
title: Importovat kroky aplikace
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 12/03/2019
ms.author: diberry
ms.openlocfilehash: c0253360c66ef6fd995f65e8a83ba5adcdf19543
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806405"
---
1. Na [portálu Luis Preview](https://preview.luis.ai)na stránce **Moje aplikace** vyberte **importovat**a pak **importovat jako JSON**. Vyhledejte uložený soubor JSON z předchozího kroku. Nemusíte měnit název aplikace. Vyberte **Hotovo** .

1. V části **Správa** na kartě **verze** vyberte verzi, pak vyberte **klonovat** , aby se naklonoval verze, a zadejte nový název o 10 znaků a potom vyberte **Hotovo** a dokončete proces klonování. Vzhledem k tomu, že název verze je součástí cesty URL, smí obsahovat jen znaky platné v adresách URL.

    > [!TIP]
    > Klonování verze do nové verze je osvědčeným postupem před úpravou aplikace. Po dokončení verze exportujte verzi (jako soubor. JSON nebo. Lu) a Prohlédněte si soubor do systému správy zdrojového kódu.

1. Vyberte **Build** a pak **záměry** , abyste viděli záměry, hlavní stavební kameny aplikace Luis.

    ![Změňte ze stránky verze na stránku záměry.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)