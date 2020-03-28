---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 32f4545a45eda8acddd7c93cc4917dbadca9ad4d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "67135037"
---
## <a name="create-a-spatial-anchors-resource"></a>Vytvoření prostředku prostorových kotev

Přejděte na <a href="https://portal.azure.com" target="_blank">portál Azure</a>.

V levém navigačním podokně na webu Azure Portal vyberte **Vytvořit prostředek**.

Vyhledávací pole slouží k **vyhledání prostorových kotev**.

   ![Hledání prostorových kotev](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Vyberte **možnost Prostorové kotvy**. V dialogovém okně vyberte **Vytvořit**.

V dialogovém okně **Účet prostorových kotev:**

- Zadejte jedinečný název zdroje pomocí běžných alfanumerických znaků.
- Vyberte předplatné, ke kterému chcete prostředek připojit.
- Vytvořte skupinu prostředků výběrem **možnosti Vytvořit nový**. Pojmenujte ji **myResourceGroup** a vyberte **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Vyberte umístění (oblast), do kterého chcete zdroj umístit.
- Chcete-li začít vytvářet zdroj, vyberte **možnost Nový.**

   ![Vytvoření prostředku](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Po vytvoření prostředku Azure Portal zobrazí, že vaše nasazení je kompletní. Klikněte na **Přejít k prostředku**.

![Dokončené nasazení](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Potom můžete zobrazit vlastnosti prostředku. Zkopírujte hodnotu **ID účtu** prostředku do textového editoru, protože ji budete později potřebovat.

   ![Vlastnosti prostředků](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

V části **Nastavení**vyberte **Klávesa**. Zkopírujte hodnotu **primárního klíče** do textového editoru. Tato hodnota `Account Key`je . Budete ho potřebovat později.

   ![Klíč účtu](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
