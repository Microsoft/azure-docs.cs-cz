---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 32f4545a45eda8acddd7c93cc4917dbadca9ad4d
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58633036"
---
## <a name="create-a-spatial-anchors-resource"></a>Vytvořit prostředek prostorových kotvy

Přejděte na <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

V levém navigačním podokně na webu Azure Portal, vyberte **vytvořit prostředek**.

Použijte vyhledávací pole pro hledání **prostorových kotvy**.

   ![Vyhledat prostorové kotvy](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Vyberte **prostorových kotvy**. V dialogovém okně vyberte **vytvořit**.

V **prostorových účet kotvy** dialogové okno:

- Zadejte název jedinečné prostředků, pomocí regulárních alfanumerické znaky.
- Vyberte předplatné, které chcete připojit prostředek, který chcete.
- Vytvořte skupinu prostředků tak, že vyberete **vytvořit nový**. Pojmenujte ji **myResourceGroup** a vyberte **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Vyberte umístění (oblast), ve kterém k umístění zdroje.
- Vyberte **nový** a vytvořte prostředek.

   ![Vytvoření prostředku](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Po vytvoření prostředku, webu Azure Portal se zobrazí, že vaše nasazení je kompletní. Klikněte na **Přejít k prostředku**.

![Nasazení dokončeno](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Pak můžete zobrazit vlastnosti prostředku. Zkopírujte resource **ID účtu** hodnoty do textového editoru, protože ho budete potřebovat později.

   ![Vlastnosti prostředku](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

V části **nastavení**vyberte **klíč**. Kopírovat **primární klíč** hodnoty do textového editoru. Tato hodnota je `Account Key`. Budete ho potřebovat později.

   ![Klíč účtu](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
