---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: cee446e54a37ce47fc82d72724e831b8270f3769
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57908480"
---
## <a name="create-a-spatial-anchors-resource"></a>Vytvořit prostředek prostorových kotvy

1. Přejděte na <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

2. V nabídce vlevo na webu Azure Portal vyberte **vytvořit prostředek**.

3. Vyhledejte "Prostorových ukotvení" na vyhledávacím panelu.

   ![Vyhledat prostorové kotvy](./media/spatial-anchors-get-started-create-resource/portal-search.png)

4. Vyberte **prostorových kotvy** otevřete dialogové okno a zvolte **vytvořit**.

5. V **prostorových účet kotvy** formuláře:

   1. Zadejte název jedinečné prostředku.
   2. Vyberte předplatné, prostředek, který chcete připojit.
   3. Vytvořte skupinu prostředků tak, že vyberete **vytvořit nový** a název skupiny prostředků **myResourceGroup** a vyberte **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
   4. Vyberte umístění (oblast), kde budou umístěné zdroje.
   5. Vyberte **nový** a vytvořte prostředek.

   ![Vytvoření prostředku](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

6. Po úspěšném dokončení vytvoření prostředku můžete zobrazit vlastnosti prostředku. Zkopírujte resource **ID účtu** hodnoty do textového editoru, jak se bude vyžadovat později.

   ![Zobrazení vlastností prostředku](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

7. V části **nastavení**vyberte **klíč**a zkopírujte do textového editoru **primární klíč** hodnotu. Tato hodnota je `Account Key` a bude použita později.

   ![Klíč účtu zobrazení](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
