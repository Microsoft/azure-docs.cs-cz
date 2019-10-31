---
title: Použití skupin zařízení v aplikaci Azure IoT Central | Microsoft Docs
description: Jako operátor se naučíte používat skupiny zařízení k analýze telemetrie ze zařízení v aplikaci Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 0f222cf9907dbf768a5f93842ff3df3fddf224a4
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180865"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry-preview-features"></a>Kurz: použití skupin zařízení k analýze telemetrie zařízení (funkce ve verzi Preview)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Tento článek popisuje, jak jako operátor k používání skupin zařízení k analýze telemetrie zařízení v aplikaci Azure IoT Central.

Skupina zařízení je seznam zařízení, která jsou seskupená, protože odpovídají některým zadaným kritériím. Skupiny zařízení vám pomůžou spravovat, vizualizovat a analyzovat zařízení ve velkém měřítku tím, že se zařízení seskupí do menších logických skupin. Můžete například vytvořit skupinu zařízení, která zobrazí seznam všech zařízení klimatizačního zařízení v Praze, aby mohl pracovník vyhledat zařízení, pro která jsou zodpovědná.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření skupiny zařízení
> * Použití skupiny zařízení k analýze telemetrie zařízení

## <a name="prerequisites"></a>Předpoklady

Než začnete, měli byste dokončit [Vytvoření aplikace IoT Central v Azure](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) a [Přidat simulované zařízení do](./quick-create-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) rychlých startů pro aplikace IoT Central a vytvořit šablonu zařízení **snímače prostředí** , se kterou chcete pracovat.

## <a name="create-simulated-devices"></a>Vytváření simulovaných zařízení

Než vytvoříte skupinu zařízení, přidejte aspoň pět simulovaných zařízení ze šablony zařízení **snímače prostředí** pro použití v tomto kurzu:

![Pět simulovaných zařízení snímače životního prostředí](./media/tutorial-use-device-groups-pnp/simulated-devices.png)

Pro čtyři ze zařízení snímače životního prostředí použijte zobrazení **vlastnosti snímače životního prostředí** a nastavte název zákazníka na **Contoso**:

![Nastavit název zákazníka na contoso](./media/tutorial-use-device-groups-pnp/customer-name.png)

## <a name="create-a-device-group"></a>Vytvoření skupiny zařízení

Vytvoření skupiny zařízení:

1. V levém podokně vyberte **skupiny zařízení** .

1. Vyberte **+ Nový**.

    ![Nová skupina zařízení](media/tutorial-use-device-groups-pnp/image1.png)

1. Zadejte název skupiny zařízení, například **zařízení contoso**. Můžete také přidat popis. Skupina zařízení může obsahovat jenom zařízení z jedné šablony zařízení. Vyberte šablonu zařízení **snímače životního prostředí** , kterou chcete použít pro tuto skupinu.

1. Vytvořte dotaz pro identifikaci zařízení patřících **společnosti Contoso** pro skupinu zařízení tak, že vyberete vlastnost **název zákazníka** , porovnávací operátor **rovnosti** a **Contoso** jako hodnotu. Můžete přidat několik dotazů a zařízení, která splňují **všechna** kritéria, která jsou umístěna ve skupině zařízení. Vytvořená skupina zařízení je přístupná všem uživatelům, kteří mají přístup k aplikaci, takže kdokoli může zobrazit, upravit nebo odstranit skupinu zařízení.

    ![Dotaz na skupinu zařízení](media/tutorial-use-device-groups-pnp/image2.png)

    > [!NOTE]
    > Skupina zařízení je dynamický dotaz. Pokaždé, když si zobrazíte seznam zařízení, v seznamu můžou být různá zařízení. Seznam závisí na tom, která zařízení aktuálně splňují kritéria dotazu.

1. Zvolte **Uložit**.

> [!NOTE]
> U Azure IoT Edge zařízení vyberte Azure IoT Edge šablony a vytvořte skupinu zařízení.

## <a name="analytics"></a>Analýzy

Pomocí **analýzy** se skupinou zařízení můžete analyzovat telemetrii ze zařízení ve skupině. Můžete například vykreslovat průměrnou teplotu oznámenou všemi senzory prostředí contoso.

Postup analýzy telemetrie pro skupinu zařízení:

1. V levém podokně vyberte **Analýza** .

1. Vyberte skupinu zařízení **Contoso** , kterou jste vytvořili. Pak přidejte i typ telemetrie **teploty** i **vlhkosti** :

    ![Vytváření analýz](./media/tutorial-use-device-groups-pnp/create-analysis.png)

    Pomocí ikon ozubeného kolečka vedle typů telemetrie vyberte typ agregace. Výchozí hodnota je **Average**. Použijte funkci **rozdělit podle** a změňte způsob zobrazení agregovaných dat. Pokud například rozdělíte podle ID zařízení, zobrazí se vykreslení pro každé zařízení, když vyberete **analyzovat**.

1. Výběrem položky **analyzovat** zobrazíte průměrné hodnoty telemetrie:

    ![Zobrazit analýzu](./media/tutorial-use-device-groups-pnp/view-analysis.png)

    Můžete upravit zobrazení, změnit zobrazené časové období a exportovat data.

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s používáním skupin zařízení v aplikaci Azure IoT Central, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Jak vytvořit pravidla telemetrie](tutorial-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
