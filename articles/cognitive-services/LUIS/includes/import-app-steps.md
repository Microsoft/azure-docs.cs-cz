---
title: Importovat kroky aplikace
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/22/2020
ms.author: diberry
ms.openlocfilehash: 37f1b85b4ce8510d5e288df985a55dba659f0c9b
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2020
ms.locfileid: "86035491"
---
1. Na stránce **Moje aplikace** na [portálu Luis](https://www.luis.ai)vyberte **+ Nová aplikace pro konverzaci**a pak **importovat jako JSON**. Vyhledejte uložený soubor JSON z předchozího kroku. Nemusíte měnit název aplikace. Vyberte **Hotovo** .

1. V části **Správa** na kartě **verze** vyberte `0.1` verzi, pak vyberte **klonovat** , aby se naklonoval verze, a zadejte nový název a `ml-entity` potom vyberte **Hotovo** pro dokončení procesu klonování. Název verze je součástí cesty URL, a proto smí obsahovat jenom znaky, které jsou platné v adresách URL.

    > [!TIP]
    > Naklonování do nové verze je osvědčeným postupem před úpravou aplikace. Až dokončíte změnu verze, exportujte verzi (jako soubor. JSON nebo. Lu) a Prohlédněte si soubor do systému správy zdrojového kódu.

1. Vyberte **Build** a pak **záměry** , abyste viděli záměry, hlavní stavební kameny aplikace Luis.

    ![Změňte ze stránky verze na stránku záměry.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)
