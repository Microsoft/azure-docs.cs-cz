---
title: Kroky importu aplikace
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/30/2020
ms.author: diberry
ms.openlocfilehash: b7b8befa0f5871b65b9b5621bfb99c659bf07235
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422763"
---
1. Na [portálu LUIS ve verzi Preview](https://preview.luis.ai)vyberte na stránce Moje **aplikace** možnost + Nová aplikace **pro konverzaci**a **potom importovat jako JSON**. Najděte uložený soubor JSON z předchozího kroku. Název aplikace není nutné měnit. Vybrat **Hotovo**

1. V části **Spravovat** vyberte na kartě `0.1` Verze verzi, pak vyberte **Klonovat,** chcete-li naklonovat `ml-entity`verzi, a přiřazujte jí nový desetimístný název **položky** a pak vyberte **Hotovo,** chcete-li dokončit proces klonování. Název verze je součástí cesty URL, a proto smí obsahovat jenom znaky, které jsou platné v adresách URL.

    > [!TIP]
    > Klonování do nové verze je osvědčeným postupem před úpravou aplikace. Po dokončení změny verze exportujte verzi (jako soubor JSON nebo .lu) a soubor zkontrolujte do systému správy zdrojového kódu.

1. Vyberte **sestavení** **pak záměry** zobrazíte záměry, hlavní stavební bloky aplikace LUIS.

    ![Změna ze stránky Verze na stránku Záměry](../media/tutorial-machine-learned-entity/new-version-imported-app.png)