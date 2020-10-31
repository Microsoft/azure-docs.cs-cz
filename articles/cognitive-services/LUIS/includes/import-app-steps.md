---
title: Importovat kroky aplikace
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 10/16/2020
ms.openlocfilehash: b70a22e502de5441b5c91a8f33357375834f54e1
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130720"
---
1. Na stránce **Moje aplikace** na [portálu Luis](https://www.luis.ai)vyberte **+ Nová aplikace pro konverzaci** a pak **importovat jako JSON** . Vyhledejte uložený soubor JSON z předchozího kroku. Nemusíte měnit název aplikace. Vyberte **Hotovo** .

1. V části **Správa** na kartě **verze** vyberte `0.1` verzi, pak vyberte **klonovat** , aby se naklonoval verze, a zadejte nový název a `ml-entity` potom vyberte **Hotovo** pro dokončení procesu klonování. Název verze je součástí cesty URL, a proto smí obsahovat jenom znaky, které jsou platné v adresách URL.

    > [!TIP]
    > Naklonování do nové verze je osvědčeným postupem před úpravou aplikace. Až dokončíte změnu verze, exportujte verzi (jako soubor. JSON nebo. Lu) a Prohlédněte si soubor do systému správy zdrojového kódu.

1. V horní části obrazovky vyberte **sestavení** a potom v navigační nabídce vlevo klikněte na **záměry** . Zobrazí se záměry aplikace, což jsou hlavní stavební kameny aplikace LUIS.
