---
title: Kurz – používání skupin zařízení v aplikaci Azure IoT Central | Microsoft Docs
description: Kurz – jako operátor a Naučte se používat skupiny zařízení k analýze telemetrie ze zařízení v aplikaci Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 3192a9f121d4380a3e681747596fc91997662bf0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90967941"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Kurz: použití skupin zařízení k analýze telemetrie zařízení

Tento článek popisuje, jak jako operátor k používání skupin zařízení k analýze telemetrie zařízení v aplikaci Azure IoT Central.

Skupina zařízení je seznam zařízení, která jsou seskupená, protože odpovídají některým zadaným kritériím. Skupiny zařízení vám pomůžou spravovat, vizualizovat a analyzovat zařízení ve velkém měřítku tím, že se zařízení seskupí do menších logických skupin. Můžete například vytvořit skupinu zařízení, která zobrazí seznam všech zařízení klimatizačního zařízení v Praze, aby mohl pracovník vyhledat zařízení, pro která jsou zodpovědná.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření skupiny zařízení
> * Použití skupiny zařízení k analýze telemetrie zařízení

## <a name="prerequisites"></a>Požadavky

Než začnete, měli byste dokončit [Vytvoření aplikace IoT Central Azure](./quick-deploy-iot-central.md) a [Přidat simulované zařízení do](./quick-create-simulated-device.md) rychlých startů pro aplikace IoT Central a vytvořit tak šablonu zařízení **MXChip IoT DevKit** , se kterou chcete pracovat.

## <a name="create-simulated-devices"></a>Vytváření simulovaných zařízení

Než vytvoříte skupinu zařízení, přidejte aspoň pět simulovaných zařízení ze šablony zařízení **MXChip IoT DevKit** pro použití v tomto kurzu:

![Pět zařízení se simulovaným senzorem](./media/tutorial-use-device-groups/simulated-devices.png)

Pro čtyři ze zařízení simulovaného senzoru použijte zobrazení **spravovat zařízení** a nastavte název zákazníka na *Contoso*:

![Nastavit název zákazníka na contoso](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>Vytvoření skupiny zařízení

Vytvoření skupiny zařízení:

1. V levém podokně vyberte **skupiny zařízení** .

1. Vyberte **+** :

    ![Nová skupina zařízení](media/tutorial-use-device-groups/image1.png)

1. Udělte skupině zařízení název *zařízení contoso*. Můžete také přidat popis. Skupina zařízení může obsahovat jenom zařízení z jedné šablony zařízení. Vyberte šablonu zařízení **MXChip IoT DevKit** , kterou chcete pro tuto skupinu použít.

1. Pokud chcete skupinu zařízení přizpůsobit tak, aby zahrnovala jenom zařízení patřící **společnosti Contoso**, vyberte **+ Filtr**. Jako hodnotu vyberte vlastnost **název zákazníka** , porovnávací operátor **rovnosti** a **Contoso** . Do skupiny zařízení můžete přidat víc filtrů a zařízení, která splňují **všechna** kritéria filtru. Vytvořená skupina zařízení je přístupná všem uživatelům, kteří mají přístup k aplikaci, takže kdokoli může zobrazit, upravit nebo odstranit skupinu zařízení:

    ![Dotaz na skupinu zařízení](media/tutorial-use-device-groups/image2.png)

    > [!TIP]
    > Skupina zařízení je dynamický dotaz. Pokaždé, když si zobrazíte seznam zařízení, v seznamu můžou být různá zařízení. Seznam závisí na tom, která zařízení aktuálně splňují kritéria dotazu.

1. Klikněte na tlačítko **Uložit**.

> [!NOTE]
> U Azure IoT Edge zařízení vyberte Azure IoT Edge šablony a vytvořte skupinu zařízení.

## <a name="analytics"></a>Analýzy

Pomocí **analýzy** se skupinou zařízení můžete analyzovat telemetrii ze zařízení ve skupině. Můžete například vykreslovat průměrnou teplotu oznámenou všemi senzory prostředí contoso.

Postup analýzy telemetrie pro skupinu zařízení:

1. V levém podokně vyberte **Analýza** .

1. Vyberte skupinu zařízení **Contoso** , kterou jste vytvořili. Pak přidejte i typ telemetrie **teploty** i **vlhkosti** :

    ![Vytváření analýz](./media/tutorial-use-device-groups/create-analysis.png)

    Pomocí ikon ozubeného kolečka vedle typů telemetrie vyberte typ agregace. Výchozí hodnota je **Average**. Použijte funkci **rozdělit podle** a změňte způsob zobrazení agregovaných dat. Pokud například rozdělíte podle ID zařízení, zobrazí se vykreslení pro každé zařízení, když vyberete **analyzovat**.

1. Výběrem položky **analyzovat** zobrazíte průměrné hodnoty telemetrie:

    ![Zobrazení analýzy](./media/tutorial-use-device-groups/view-analysis.png)

    Můžete upravit zobrazení, změnit zobrazené časové období a exportovat data.

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s používáním skupin zařízení v aplikaci Azure IoT Central, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Jak vytvořit pravidla telemetrie](tutorial-create-telemetry-rules.md)
