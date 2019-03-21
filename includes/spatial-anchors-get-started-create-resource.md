---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: dc8d3c2d400204f53b05bb5536af95679541f3f6
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305095"
---
## <a name="create-a-spatial-anchors-resource"></a>Vytvořit prostředek prostorových kotvy

Přejděte na <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

V levém navigačním podokně na webu Azure Portal, vyberte **vytvořit prostředek**.

Použijte vyhledávací pole pro hledání **prostorových kotvy**.

   ![Vyhledat prostorové kotvy](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Vyberte **prostorových kotvy**. V dialogovém okně vyberte **vytvořit**.

V **prostorových účet kotvy** dialogové okno:

- Zadejte název jedinečné prostředků.
- Vyberte předplatné, které chcete připojit prostředek, který chcete.
- Vytvořte skupinu prostředků tak, že vyberete **vytvořit nový**. Pojmenujte ji **myResourceGroup** a vyberte **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Vyberte umístění (oblast), ve kterém k umístění zdroje.
- Vyberte **nový** a vytvořte prostředek.

   ![Vytvoření prostředku](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Po vytvoření prostředku můžete zobrazit vlastnosti prostředku. Zkopírujte resource **ID účtu** hodnoty do textového editoru, protože ho budete potřebovat později.

   ![Vlastnosti prostředku](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

V části **nastavení**vyberte **klíč**. Kopírovat **primární klíč** hodnoty do textového editoru. Tato hodnota je `Account Key`. Budete ho potřebovat později.

   ![Klíč účtu](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
