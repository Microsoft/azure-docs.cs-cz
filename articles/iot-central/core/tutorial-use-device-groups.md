---
title: Použití skupin zařízení v aplikaci Azure IoT Central | Dokumenty společnosti Microsoft
description: Jako operátor se dozvíte, jak pomocí skupin zařízení analyzovat telemetrii ze zařízení v aplikaci Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 758ac037fcd224d02f62239b3408b41b50390147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167224"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Kurz: Použití skupin zařízení k analýze telemetrie zařízení

Tento článek popisuje, jak jako operátor pomocí skupin zařízení k analýze telemetrie zařízení v aplikaci Azure IoT Central.

Skupina zařízení je seznam zařízení, která jsou seskupena, protože odpovídají některým zadaným kritériím. Skupiny zařízení vám pomohou spravovat, vizualizovat a analyzovat zařízení ve velkém měřítku seskupením zařízení do menších logických skupin. Můžete například vytvořit skupinu zařízení, která zobrazí seznam všech klimatizačních zařízení v Seattlu, aby technik mohl najít zařízení, za která jsou zodpovědní.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření skupiny zařízení
> * Analýza telemetrie zařízení pomocí skupiny zařízení

## <a name="prerequisites"></a>Požadavky

Než začnete, měli byste dokončit [vytvoření aplikace Azure IoT Central](./quick-deploy-iot-central.md) a přidat simulované zařízení do rychlého spuštění aplikace [IoT Central](./quick-create-pnp-device.md) k vytvoření šablony zařízení **MXChip IoT DevKit** pro práci.

## <a name="create-simulated-devices"></a>Vytvoření simulovaných zařízení

Před vytvořením skupiny zařízení přidejte alespoň pět simulovaných zařízení ze šablony zařízení **MXChip IoT DevKit,** která se použijí v tomto kurzu:

![Pět simulovaných senzorových zařízení](./media/tutorial-use-device-groups/simulated-devices.png)

U čtyř simulovaných senzorových zařízení můžete pomocí zobrazení **Spravovat zařízení** nastavit jméno zákazníka na *Contoso*:

![Nastavit jméno zákazníka na Contoso](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>Vytvoření skupiny zařízení

Vytvoření skupiny zařízení:

1. V levém podokně zvolte **Skupiny zařízení.**

1. Vyberte **+**:

    ![Nová skupina zařízení](media/tutorial-use-device-groups/image1.png)

1. Dejte skupině zařízení název *Zařízení Contoso*. Můžete také přidat popis. Skupina zařízení může obsahovat pouze zařízení z jedné šablony zařízení. Zvolte šablonu zařízení **MXChip IoT DevKit,** která se má použít pro tuto skupinu.

1. Chcete-li přizpůsobit skupinu zařízení tak, aby zahrnovala pouze zařízení patřící společnosti **Contoso**, vyberte **možnost + filtr**. Jako hodnotu vyberte vlastnost **Jméno zákazníka,** operátor **porovnání Rovná se** a **Contoso.** Můžete přidat více filtrů a zařízení, která splňují **všechna** kritéria filtru, jsou umístěna ve skupině zařízení. Skupina zařízení, kterou vytvoříte, je přístupná všem uživatelům, kteří mají přístup k aplikaci, takže skupinu zařízení může zobrazit, upravit nebo odstranit kdokoli:

    ![Dotaz skupiny zařízení](media/tutorial-use-device-groups/image2.png)

    > [!TIP]
    > Skupina zařízení je dynamický dotaz. Při každém zobrazení seznamu zařízení mohou být v seznamu různá zařízení. Seznam závisí na tom, která zařízení aktuálně splňují kritéria dotazu.

1. Zvolte **Uložit**.

> [!NOTE]
> Pro zařízení Azure IoT Edge vyberte šablony Azure IoT Edge a vytvořte skupinu zařízení.

## <a name="analytics"></a>Analýza

**Pomocí služby Analytics** se skupinou zařízení můžete analyzovat telemetrii ze zařízení ve skupině. Můžete například vykreslit průměrnou teplotu hlášenou všemi senzory prostředí Contoso.

Analýza telemetrie pro skupinu zařízení:

1. V levém podokně zvolte **Analytics.**

1. Vyberte skupinu **zařízení Contoso,** kterou jste vytvořili. Potom přidejte typy telemetrie **Teplota** i **Vlhkost:**

    ![Vytváření analýz](./media/tutorial-use-device-groups/create-analysis.png)

    Pomocí ikon ozubených kol vedle typů telemetrie vyberte typ agregace. Výchozí hodnota je **Průměr**. Pomocí **funkce Rozdělit podle** můžete změnit způsob zobrazení agregovaných dat. Pokud například rozdělíte podle ID zařízení, zobrazí se obrázek pro každé zařízení, když vyberete **Analyzovat**.

1. Výběrem **možnosti Analyzovat** zobrazíte průměrné hodnoty telemetrie:

    ![Zobrazit analýzu](./media/tutorial-use-device-groups/view-analysis.png)

    Zobrazení můžete přizpůsobit, změnit zobrazené časové období a exportovat data.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili používat skupiny zařízení v aplikaci Azure IoT Central, tady je navrhovaný další krok:

> [!div class="nextstepaction"]
> [Jak vytvořit pravidla telemetrie](tutorial-create-telemetry-rules.md)
