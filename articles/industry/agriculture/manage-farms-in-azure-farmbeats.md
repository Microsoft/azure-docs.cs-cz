---
title: Spravovat farmy
description: Popisuje, jak spravovat farmy.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: dc06f7c75e27d831994b97b97f3d20bb34c3a6c5
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2020
ms.locfileid: "92167929"
---
# <a name="manage-farms"></a>Správa farem

Své farmy můžete spravovat ve službě Azure FarmBeats. Tento článek poskytuje informace o tom, jak vytvářet farmy, instalovat zařízení, senzory a DRONY zachraňují životy, které vám pomůžou spravovat vaše farmy.

## <a name="create-farms"></a>Vytváření farem

Použijte k tomu následující postup:

1. Přihlaste se k akcelerátoru farmy a zobrazí se stránka **farmy** .
    Stránka **farmy** zobrazuje seznam farem v případě, že již byly vytvořeny v předplatném.

    Tady je ukázka obrázku:

    ![Snímek obrazovky zobrazující stránku farmy](./media/create-farms-in-azure-farmbeats/create-farm-main-page-1.png)


2. Vyberte **vytvořit farmu** a zadejte **název**, **plodiny** a **adresu**.
3. V části **definovat hranici farmy**(povinné pole) vyberte buď možnost **označit na mapě** , nebo **vložte kód v oblasti injson**.

Existují dva způsoby, jak definovat hranici farmy:

1. **Označit na mapě**: pomocí nástroje mapového ovládacího prvku nakreslete a označte hranici farmy. Chcete-li označit hranice,  ![ snímek obrazovky, který zobrazuje ikonu tužky pro kreslení hranic na mapě ](./media/create-farms-in-azure-farmbeats/pencil-icon-1.png) , a označte přesné hranice.

    ![Snímek obrazovky zobrazující hranice vykreslování na mapě](./media/create-farms-in-azure-farmbeats/create-farm-mark-on-map-1.png)

2. **Vložení geografického kódu JSON**: geografická JSON je formát pro kódování geografických datových struktur pomocí JavaScript Object Notation (JSON). Tato možnost zobrazí textové pole, kde lze zadat řetězec typu "text", který označuje hranice farmy. Z GeoJSON.io můžete také vytvořit kód pro injson.
Informace můžete zadat pomocí popisů.

    ![Snímek obrazovky, který zvýrazní možnost vložení invariantního kódu JSON na obrazovce vytvořit farmu.](./media/create-farms-in-azure-farmbeats/create-new-farm-1.png)

3.  Vyberte **Odeslat** a vytvořte farmu. Vytvoří se nová farma, která se zobrazí na stránce **farmy** .

## <a name="view-farm"></a>Zobrazit farmu

Na stránce seznam farmy se zobrazí seznam vytvořených farem. Vyberte farmu, pro kterou chcete zobrazit seznam:

 - **Počet zařízení** – zobrazí počet a stav zařízení nasazených v rámci farmy.
 - **Map** – mapování farmy pomocí zařízení nasazených ve farmě
 - **Telemetrie** – zobrazí telemetrii ze senzorů nasazených ve farmě.
 - **Nejnovější mapy přesnosti** – zobrazí nejnovější mapu satelitních indexů (EVI, NDWI), vlhkosti v půdě a mapu umístění senzorů.

## <a name="edit-farm"></a>Upravit farmu

Stránka **farmy** zobrazuje seznam vytvořených farem.

1.  Vyberte farmu, pro kterou chcete farmu zobrazit a upravit.
2.  Vyberte **Upravit farmu** a upravte informace o farmě. V okně **Podrobnosti o farmě** můžete upravit pole **název**, **plodiny**, **adresa**a definovat **hranice farmy** .

    ![Beats farmy projektu](./media/create-farms-in-azure-farmbeats/edit-farm-1.png)

3. Kliknutím na tlačítko **Odeslat** uložte upravované podrobnosti.

## <a name="delete-farm"></a>Odstranit farmu

Na stránce **farmy** se zobrazí seznam vytvořených Farm. Chcete-li odstranit farmu, použijte následující postup:

1.  Vyberte farmu ze seznamu pro odstranění podrobností o farmě.
2.  Vyberte **Odstranit farmu** , aby se farma odstranila.

    ![Snímek obrazovky zobrazující obrazovku odstranit farmu a zvýrazní tlačítko Odstranit](./media/create-farms-in-azure-farmbeats/delete-farm-1.png)

    > [!NOTE]
    > Když odstraníte farmu, zařízení a mapy přidružené k farmě se neodstraňují. Žádné informace o farmě spojené se zařízením a mapami nebudou relevantní. Můžete dál zobrazovat zařízení, telemetrie a mapy ze služby FarmBeats.


## <a name="next-steps"></a>Další kroky

Teď, když jste vytvořili farmu, zjistěte, jak [získat data ze senzorů](get-sensor-data-from-sensor-partner.md) do farmy.
