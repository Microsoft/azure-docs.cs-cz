---
title: Správa tvůrce map Microsoft Azure (Preview)
description: V tomto článku se dozvíte, jak spravovat tvůrce map Microsoft Azure (Preview).
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/16/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d26df4287032bc59cc58dd1d832d9d5a9c40afcd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100559150"
---
# <a name="manage-azure-maps-creator-preview"></a>Správa Azure Maps Creator (Preview) 

> [!IMPORTANT]
> Služby Azure Maps Creator jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Maps Creator vám umožňuje vytvořit soukromá data o mapě interiéru. Pomocí rozhraní Azure Maps API a modulu mapy vnitřních souborů můžete vyvíjet interaktivní a dynamické webové aplikace v rámci vnitřních map. V současné době je tvůrce k dispozici pouze v USA pomocí cenové úrovně S1.

Tento článek vás provede kroky k vytvoření a odstranění prostředku Creator v účtu Azure Maps.

## <a name="create-creator-preview-resource"></a>Vytvořit prostředek Creator (Preview)

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

2. Vyberte účet Azure Maps. Pokud nevidíte účet Azure Maps v části **Poslední prostředky**, přejděte do nabídky Azure Portal. Vyberte **Všechny prostředky**. Vyhledejte a vyberte svůj účet Azure Maps.

    ![Domovská stránka portálu Azure Maps](./media/how-to-manage-creator/select-maps-account.png)

3. Až budete na stránce Azure Maps účet, přejděte na možnost **Přehled** v části **Autor**. Vyberte  **vytvořit**  a vytvořte prostředek Azure Maps Creator.

    ![Stránka pro vytvoření Azure Mapsho Tvůrce](./media/how-to-manage-creator/creator-blade-settings.png)

4. Zadejte název a umístění prostředku autora. V současné době je tvůrce podporován pouze v USA. Vyberte **Zkontrolovat a vytvořit**.

   ![Stránka zadání informací o účtu Tvůrce](./media/how-to-manage-creator/creator-creation-dialog.png)

5. Zkontrolujte nastavení a vyberte **vytvořit**.

    ![Stránka pro potvrzení autora nastavení účtu](./media/how-to-manage-creator/creator-create-dialog.png)

6. Po dokončení nasazení se zobrazí stránka s úspěšným nebo neúspěšným hlášením.

   ![Stránka stavu nasazení prostředků](./media/how-to-manage-creator/creator-resource-created.png)

7. Vyberte **Přejít k prostředku**. Stránka pro zobrazení prostředků autora zobrazuje stav zdroje autora a zvolené demografické oblasti.

    ![Stránka stavu Tvůrce](./media/how-to-manage-creator/creator-resource-view.png)

   >[!NOTE]
   >Na stránce tvůrce prostředků můžete přejít zpět na účet Azure Maps, ke kterému patří, tak, že vyberete Azure Maps účet.

## <a name="delete-creator-preview-resource"></a>Odstranit prostředek Creator (Preview)

Pokud chcete odstranit prostředek tvůrce, přejděte na účet Azure Maps. V části **Tvůrce** vyberte **Přehled** . Vyberte tlačítko **Odstranit**.

>[!WARNING]
>Když odstraníte prostředek autora účtu Azure Maps, odstraní se také datové sady, tilesets a funkce statesets vytvořené pomocí služby Creator Services.

![Stránka Creator s tlačítkem odstranit](./media/how-to-manage-creator/creator-delete.png)

Kliknutím na tlačítko **Odstranit** a zadáním jména autora potvrďte odstranění. Po odstranění prostředku se zobrazí potvrzovací stránka, například na obrázku níže:

![Stránka Creator s potvrzením odstranění](./media/how-to-manage-creator/creator-confirm-delete.png)

## <a name="authentication"></a>Authentication

Creator (Preview) dědí nastavení Azure Maps Access Control (IAM). Všechna volání rozhraní API pro přístup k datům se musí odesílat pomocí ověřovacích a autorizačních pravidel.

Data o využití tvůrce jsou začleněná do vašich Azure Mapsch grafů využití a protokolu aktivit.  Další informace najdete v tématu [Správa ověřování v Azure Maps](./how-to-manage-authentication.md).

## <a name="access-to-creator-services"></a>Přístup ke službám Tvůrce

Služby Creator Services (Preview) a služby, které používají data hostovaná v tvůrci (například služba vykreslování), jsou přístupné na geografické adrese URL. Zeměpisná adresa URL je určena umístěním vybraným při vytváření. Například pokud je tvůrce vytvořen v USA geografické umístění, musí být všechna volání služby převodu odeslána na `us.atlas.microsoft.com/conversion/convert` .

Všechna data importovaná do Tvůrce se taky musí nahrát do stejného geografického umístění jako prostředek tvůrce. Pokud je například v českém seznamu zřízena možnost tvůrce, měla by být všechna nezpracovaná data odeslána prostřednictvím `us.atlas.microsoft.com/mapData/upload` .

## <a name="next-steps"></a>Další kroky

Seznámení se službou Creator Services (Preview) pro mapování vnitřních verzí:

> [!div class="nextstepaction"]
> [Nahrávání dat](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Převod dat](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Integrován](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Tileset](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Sada stavů funkcí](creator-indoor-maps.md#feature-statesets)

Naučte se používat služby Creator (Preview) k vykreslování vnitřních map v aplikaci:

> [!div class="nextstepaction"]
> [Kurz pro tvůrce Azure Maps](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Dynamické stylování mapy interiéru](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Použití modulu mapy Vnitřníchy](how-to-use-indoor-module.md)