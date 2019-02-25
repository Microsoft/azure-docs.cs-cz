---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 9e232c5a8f1c2e38c04ea2236691554952f443e8
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752222"
---
## <a name="create-a-spatial-anchors-resource"></a>Vytvořit prostředek prostorových kotvy

1. Přejděte na <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

2. V nabídce vlevo na webu Azure Portal vyberte **vytvořit prostředek**.

3. Vyhledejte "Prostorových ukotvení" na vyhledávacím panelu.

   ![Vyhledat prostorové kotvy](./media/spatial-anchors-get-started-create-resource/portal-search.png)

4. Vyberte **prostorových ukotvení (preview)** otevřete dialogové okno a zvolte **vytvořit**.

5. V **prostorových účet kotvy** formuláře:

   1. Zadejte název jedinečné prostředku.
   2. Vyberte předplatné, prostředek, který chcete připojit.
   3. Vytvořte skupinu prostředků tak, že vyberete **vytvořit nový** a název skupiny prostředků **myResourceGroup** a vyberte **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
   4. Vyberte umístění (oblast), kde budou umístěné zdroje.
   5. Vyberte **nový** a vytvořte prostředek.

   ![Vytvoření prostředku](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

6. Po úspěšném dokončení vytvoření prostředku můžete zobrazit vlastnosti prostředku. Poznamenejte si prostředku **ID účtu** hodnota se bude vyžadovat později.

   ![Zobrazení vlastností prostředku](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

7. V části **nastavení**vyberte **klíč** a poznamenejte si **primární klíč** hodnotu. Tato hodnota je `Account Key` a bude použita později.

   ![Klíč účtu zobrazení](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
