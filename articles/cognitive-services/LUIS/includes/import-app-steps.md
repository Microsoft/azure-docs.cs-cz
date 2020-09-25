---
title: Importovat kroky aplikace
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/22/2020
ms.author: diberry
ms.openlocfilehash: d1eafde1cfb02234b2a52ccf4909ab47930177b9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334717"
---
1. Na stránce **Moje aplikace** na [portálu Luis](https://www.luis.ai)vyberte **+ Nová aplikace pro konverzaci**a pak **importovat jako JSON**. Vyhledejte uložený soubor JSON z předchozího kroku. Nemusíte měnit název aplikace. Vyberte **Hotovo** .

1. V části **Správa** na kartě **verze** vyberte `0.1` verzi, pak vyberte **klonovat** , aby se naklonoval verze, a zadejte nový název a `ml-entity` potom vyberte **Hotovo** pro dokončení procesu klonování. Název verze je součástí cesty URL, a proto smí obsahovat jenom znaky, které jsou platné v adresách URL.

    > [!TIP]
    > Naklonování do nové verze je osvědčeným postupem před úpravou aplikace. Až dokončíte změnu verze, exportujte verzi (jako soubor. JSON nebo. Lu) a Prohlédněte si soubor do systému správy zdrojového kódu.

1. Vyberte **Build** a pak **záměry** , abyste viděli záměry, hlavní stavební kameny aplikace Luis.

    ![Změňte ze stránky verze na stránku záměry.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)
