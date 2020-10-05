---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: b2b3ca886359a0b4c906b89ed76f57486fc2c368
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "83638997"
---
## <a name="create-a-spatial-anchors-resource"></a>Vytvoření prostředku prostorových kotev

Přejděte na web <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

V levém navigačním podokně v Azure Portal vyberte **vytvořit prostředek**.

Pomocí vyhledávacího pole vyhledejte **prostorové kotvy**.

   ![Hledat prostorové kotvy](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Vyberte **prostorové kotvy**. V dialogovém okně vyberte **vytvořit**.

V dialogovém okně **účet prostorových kotev** :

- Zadejte jedinečný název prostředku s použitím běžných alfanumerických znaků.
- Vyberte předplatné, ke kterému chcete prostředek připojit.
- Vytvořte skupinu prostředků výběrem možnosti **vytvořit nový**. Pojmenujte ji **myResourceGroup** a vyberte **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Vyberte umístění (oblast), do kterého se má prostředek umístit.
- Pokud chcete začít vytvářet prostředek, vyberte **Nový** .

   ![Vytvoření prostředku](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Po vytvoření prostředku Azure Portal zobrazí, že vaše nasazení bylo dokončeno. Klikněte na **Přejít k prostředku**.

![Dokončené nasazení](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Pak můžete zobrazit vlastnosti prostředku. Zkopírujte hodnotu **ID účtu** prostředku do textového editoru, protože ho budete potřebovat později.

   ![Vlastnosti prostředku](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Zkopírujte také hodnotu **Doména účtu** prostředku do textového editoru, protože ji budete potřebovat později.

   ![Doména účtu](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

V části **Nastavení**vyberte **klíč**. Zkopírujte hodnotu **primárního klíče** do textového editoru. Tato hodnota je `Account Key` . Budete ho potřebovat později.

   ![Klíč účtu](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
