---
title: zahrnout soubor
description: zahrnout soubor
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 60a62733a17d1a3dcc4ba80ed7ceb1c37c8ac5d6
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877174"
---
## <a name="create-the-azure-iot-edge-gateway-device"></a>Vytvoření zařízení Azure IoT Edge brány

Aplikace video Analytics – objekt a detekce pohybu obsahují šablonu zařízení **lva Edge** a zařízení **detekce pohybů lva Edge** . V této části vytvoříte šablonu zařízení brány pomocí manifestu nasazení a přidáte zařízení brány do vaší aplikace IoT Central.

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>Vytvoření šablony zařízení pro bránu LVA Edge

Import manifestu nasazení a vytvoření šablony zařízení **lva Edge Gateway** :

1. V aplikaci IoT Central přejděte na **šablony zařízení**a vyberte **+ Nový**.

1. Na stránce **Vybrat typ šablony** vyberte dlaždici **Azure IoT Edge** . Pak vyberte **Další: přizpůsobit**.

1. Na stránce **nahrát manifest nasazení Azure IoT Edge** jako název šablony zadejte *lva Edge Gateway* a v **zařízení brány se zařízením pro příjem dat**.

    Zatím nevybírejte manifest nasazení. Pokud tak učiníte, Průvodce nasazením očekává rozhraní pro každý modul, ale je nutné vystavit rozhraní pro **LvaEdgeGatewayModule**. Manifest nahrajete v pozdějším kroku.

    :::image type="content" source="./media/iot-central-video-analytics-part3/upload-deployment-manifest.png" alt-text="Nenahrávat manifest nasazení":::

    Vyberte **Další: Kontrola**.

1. Na stránce **Kontrola** vyberte **vytvořit**.

### <a name="import-the-device-capability-model"></a>Import modelu schopností zařízení

Šablona zařízení musí zahrnovat model schopností zařízení. Na stránce **Brána lva Edge** vyberte dlaždici **importovat model schopností** . Přejděte do složky *lva-Configuration* , kterou jste předtím vytvořili, a vyberte *LvaEdgeGatewayDcm.jsv* souboru.

Šablona zařízení **brány lva Edge** teď obsahuje **modul brány lva Edge** spolu se třemi rozhraními: **informace o zařízení**, **nastavení brány lva Edge**a **rozhraní lva Edge Gateway**.
