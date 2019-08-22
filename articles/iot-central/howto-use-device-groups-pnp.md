---
title: Použití skupin zařízení v aplikaci Azure IoT Central | Microsoft Docs
description: Jako operátor použijte skupiny zařízení v aplikaci Azure IoT Central.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 07f1df101442e8ae43b26cebc60c8452d475d0f3
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879744"
---
# <a name="use-device-groups-in-your-azure-iot-central-application-preview-features"></a>Použití skupin zařízení ve vaší aplikaci Azure IoT Central (funkce ve verzi Preview)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Tento článek popisuje, jak jako operátor používat skupiny zařízení v aplikaci Azure IoT Central.

Skupina zařízení je seznam zařízení, která jsou seskupená, protože odpovídají některým zadaným kritériím. Skupiny zařízení vám pomůžou spravovat, vizualizovat a analyzovat zařízení ve velkém měřítku tím, že se zařízení seskupí do menších logických skupin. Můžete například vytvořit skupinu zařízení, která zobrazí seznam všech zařízení klimatizačního zařízení v Praze, aby mohl pracovník vyhledat zařízení, pro která jsou zodpovědná. V tomto článku se dozvíte, jak vytvořit a nakonfigurovat skupiny zařízení.

## <a name="create-a-device-group"></a>Vytvoření skupiny zařízení

Vytvoření skupiny zařízení:

1. V navigační nabídce vlevo vyberte **skupiny zařízení** .

1. Vyberte **+ Nový**.

    ![Nová skupina zařízení](media/howto-use-device-groups-pnp/image1.png)

1. Udělte skupině zařízení název, který je jedinečný v celé aplikaci. Můžete také přidat popis. Skupina zařízení může obsahovat jenom zařízení z jedné šablony zařízení. Vyberte šablonu zařízení, kterou chcete použít pro tuto skupinu.

1. Vytvořte dotaz pro identifikaci zařízení pro skupinu zařízení tak, že vyberete vlastnost, operátor porovnání a hodnotu. Můžete přidat několik dotazů a zařízení, která splňují **všechna** kritéria, která jsou umístěna ve skupině zařízení. Vytvořená skupina zařízení je přístupná všem uživatelům, kteří mají přístup k aplikaci, takže kdokoli může zobrazit, upravit nebo odstranit skupinu zařízení.

    ![Dotaz na skupinu zařízení](media/howto-use-device-groups-pnp/image2.png)

    > [!NOTE]
    > Skupina zařízení je dynamický dotaz. Pokaždé, když si zobrazíte seznam zařízení, v seznamu můžou být různá zařízení. Seznam závisí na tom, která zařízení aktuálně splňují kritéria dotazu.

1. Zvolte **Uložit**.

## <a name="analytics"></a>Analýzy

Analýza ve skupinách zařízení je stejná jako hlavní karta Analytics v levé navigační nabídce. Další informace o analýzách najdete v článku o [tom, jak vytvářet analýzy](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s používáním skupin zařízení v aplikaci Azure IoT Central, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Jak vytvořit pravidla telemetrie](howto-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
