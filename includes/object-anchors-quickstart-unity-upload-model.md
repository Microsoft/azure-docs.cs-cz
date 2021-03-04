---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 03/02/2021
ms.author: crtreasu
ms.openlocfilehash: 8b7ab183dfb7b6721beae7835acd75bc7a05e72c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044625"
---
### <a name="upload-your-model"></a>Nahrání modelu

Pokud ještě nemáte model ukotvení objektu, postupujte podle pokynů v tématu [Vytvoření modelu](/object-anchors/quickstarts/get-started-model-conversion.md) a vytvořte ho. Pak se vraťte sem.

Po připojení HoloLens k portálu zařízení se systémem Windows pomocí následujících kroků nahrajte model, který má aplikace používat:

1. Na portálu zařízení se systémem Windows klikněte na **systém > Průzkumník souborů > localappdata**. V seznamu aplikací by se měla zobrazit vaše aplikace.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localappdata.png" alt-text="Průzkumník souborů":::

2. Otevřete aplikaci a klikněte na `LocalState` složku.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localstate.png" alt-text="Otevřete složku LocalState":::

3. Nahrajte soubor modelu do `LocalState` složky.

    :::image type="content" source="./media/object-anchors-quickstarts/portal-upload-model.png" alt-text="nahrání modelu na portálu":::

    Znovu spusťte aplikaci z HoloLens. Nyní můžete detekovat objekty, které odpovídají modelu.
