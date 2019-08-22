---
title: Použití sad zařízení ve vaší aplikaci Azure IoT Central | Microsoft Docs
description: Jako operátor, jak používat sady zařízení ve vaší aplikaci Azure IoT Central.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 9576711c33979cef7e043c18ac3b56251dd8a806
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877342"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Použití sad zařízení v aplikaci Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Tento článek popisuje, jak jako operátor použít sady zařízení v aplikaci Azure IoT Central.

Sada zařízení je seznam zařízení, která jsou seskupená, protože odpovídají některým zadaným kritériím. Sady zařízení vám pomůžou spravovat, vizualizovat a analyzovat zařízení ve velkém měřítku tím, že se zařízení seskupí do menších logických skupin. Můžete například vytvořit zařízení s nastavením pro výpis všech zařízení klimatizačního zařízení v Praze, aby mohl pracovník najít zařízení, pro která jsou zodpovědná. V tomto článku se dozvíte, jak vytvářet a konfigurovat sady zařízení.

## <a name="create-a-device-set"></a>Vytvoření sady zařízení

Vytvoření sady zařízení:

1. V navigační nabídce vlevo vyberte **sady zařízení** .

1. Vyberte **+ Nový**.

    ![Nová sada zařízení](media/howto-use-device-sets/image1.png)

1. Dejte zařízení název, který je jedinečný v celé aplikaci. Můžete také přidat popis. Sada zařízení může obsahovat jenom zařízení z jedné šablony zařízení. Vyberte šablonu zařízení, kterou chcete použít pro tuto sadu.

1. Vytvořte dotaz pro identifikaci zařízení pro sadu zařízení tak, že vyberete vlastnost, operátor porovnání a hodnotu. Do sady zařízení můžete přidat několik dotazů a zařízení, která splňují **všechna** kritéria. Vytvořená sada zařízení je přístupná všem uživatelům, kteří mají přístup k aplikaci, takže kdokoli může zobrazit, upravit nebo odstranit sadu zařízení.

    ![Dotaz na sadu zařízení](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > Sada zařízení je dynamický dotaz. Pokaždé, když si zobrazíte seznam zařízení, v seznamu můžou být různá zařízení. Seznam závisí na tom, která zařízení aktuálně splňují kritéria dotazu.

1. Zvolte **Uložit**.

## <a name="configure-the-dashboard-for-your-device-set"></a>Konfigurace řídicího panelu pro sadu zařízení

Po vytvoření sady zařízení můžete nakonfigurovat její **řídicí panel**. **Řídicí panel** je Domovská stránka, kam umístíte obrázky a odkazy. Můžete také přidat mřížky, které uvádějí zařízení v sadě zařízení.

1. V navigační nabídce vlevo vyberte **sady zařízení** .

1. Vyberte sadu zařízení.

1. Vyberte kartu **Řídicí panel** .

1. Vyberte **Upravit**.

    ![Režim návrhu na](media/howto-use-device-sets/image3.png)

1. Informace o přidání obrázku najdete v tématu [Příprava a nahrání imagí do aplikace Azure IoT Central](howto-prepare-images.md).

1. Přidat dlaždici propojení:
    1. V pravém podokně vyberte **odkaz** .
    1. Zadejte **název**odkazu.
    1. Vyberte adresu URL, která se má otevřít po výběru odkazu.
    1. Zadejte svůj odkaz na popis, který se zobrazí pod **názvem**.
    1. Zvolte **Uložit**.

        ![Uložit odkaz](media/howto-use-device-sets/image7.png)

    1. Dlaždici odkazů můžete na **řídicím panelu**přesunout a změnit její velikost.

1. Přidejte mřížku. Mřížka je tabulka zařízení v sadě zařízení s vámi zvolenými sloupci.
    1. V pravém podokně vyberte **Mřížka** .
    1. Dejte své mřížce **Nadpis**.
    1. Vyberte sloupce, které chcete zobrazit, výběrem možnosti **Přidat nebo odebrat**. Na panelu, který se zobrazí, vyberte požadovaný sloupec a zvolte šipku vpravo a vyberte ji.
    1. Zvolte **OK**.
    1. Zvolte **Uložit**.

        ![Uložit mřížku](media/howto-use-device-sets/image9.png)

    1. Přetažením mřížky umístěte na **řídicí panel**.

        > [!NOTE]
        > Můžete přidat více obrázků, odkazů a mřížek.
  
    1. Vyberte **Done** (Hotovo).

Další informace o tom, jak používat dlaždice v Azure IoT Central, najdete v tématu [použití dlaždic řídicího panelu](howto-use-tiles.md).

### <a name="configure-a-location-map-in-your-device-sets-dashboard"></a>Konfigurace mapy umístění na řídicím panelu sady zařízení

Můžete přidat mapu, která vizualizuje umístění zařízení v sadě zařízení.

Pokud chcete přidat mapu do řídicího panelu sady zařízení, musíte mít v šabloně zařízení nakonfigurovanou vlastnost umístění nebo umístění umístění. Další informace najdete v tématu [Vytvoření měření umístění](howto-set-up-template.md) nebo [vytvoření vlastnosti umístění](howto-set-up-template.md).

1. Na **řídicím panelu**sady zařízení vyberte **Mapa** z knihovny.
2. Přidejte název a vyberte měření umístění nebo vlastnost, které jste nakonfigurovali dříve.
3. Vyberte **Uložit** a na dlaždici mapa se zobrazí poslední známá umístění zařízení v sadě zařízení.
4. Když operátor zobrazí řídicí panel pro nastavení zařízení, operátor uvidí všechny dlaždice, které jste nakonfigurovali, včetně mapy umístění.

Na řídicím panelu můžete změnit velikost dlaždice mapa. Výběrem kódu PIN na mapě se zobrazí informace o zařízení, název a umístění. Výběrem automaticky otevíraného okna přejdete na stránku vlastností zařízení.

## <a name="configure-the-list-for-your-device-set"></a>Konfigurace seznamu pro sadu zařízení

Po vytvoření sady zařízení můžete nakonfigurovat **seznam**. **Seznam** obsahuje všechna zařízení v sadě zařízení v tabulce se zvolenými sloupci.

1. V navigační nabídce vlevo vyberte **sady zařízení** .

1. Vyberte kartu **seznam** .

1. Vyberte **Možnosti sloupců**.

    ![Možnosti sloupců](media/howto-use-device-sets/image11.png)

1. Vyberte sloupce, které chcete zobrazit, a vyberte sloupec, který chcete zobrazit, a výběrem šipky vpravo ho vyberte.

    ![Vybrat sloupec](media/howto-use-device-sets/image12.png)

1. Zvolte **OK**.

## <a name="analytics"></a>Analýzy

Analýza v sadách zařízení je stejná jako hlavní karta Analytics v levé navigační nabídce. Další informace o analýzách najdete v článku o [tom, jak vytvářet analýzy](howto-use-device-sets.md).

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili používat sady zařízení v aplikaci Azure IoT Central, je zde doporučený další krok:

> [!div class="nextstepaction"]
> [Jak vytvořit pravidla telemetrie](howto-create-telemetry-rules.md)
