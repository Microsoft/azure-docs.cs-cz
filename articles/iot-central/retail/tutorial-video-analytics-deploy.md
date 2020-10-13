---
title: Jak nasadit šablonu aplikace Azure IoT Central video Analytics – objekt a pohyb
description: Tato příručka shrnuje postup nasazení aplikace Azure IoT Central pomocí šablony aplikace pro video Analytics – objekt a detekce pohybu.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: how-to
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: decfa7020be7778e8ca64a9fb0cb4aac1657da27
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91873332"
---
# <a name="how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>Postup nasazení IoT Central aplikace pomocí šablony video Analytics – objekt a detekce pohybu

Přehled klíčových komponent aplikace *video Analytics – objekt a pohyb pohybu* najdete v tématu [Architektura aplikace analýzy objektů a pohybů](architecture-video-analytics.md).

Následující video poskytuje návod, jak pomocí _šablony aplikace pro detekci video Analytics – objekt a pohyb_ nasadit řešení IoT Central:

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

## <a name="deploy-the-application"></a>Nasazení aplikace

Provedením následujících kroků nasadíte IoT Central aplikaci pomocí šablony aplikace video Analytics:

1. Dokončete buď [aplikaci video Analytics v azure IoT Central (Yolo V3)](tutorial-video-analytics-create-app-yolo-v3.md) , nebo vytvořte kurz [analýzy videa v Azure IoT Central (OpenVINO &trade; )](tutorial-video-analytics-create-app-openvino.md) :
    - Vytvoření účtu Azure Media Services
    - Vytvořte aplikaci IoT Central z šablony video Analytics – objekt a detekce pohybu.
    - Nakonfigurujte zařízení brány v IoT Central aplikaci. Brána umožňuje zařízením kamery připojit se k aplikaci.

1. Dokončete buď [instanci IoT Edge pro video Analytics (virtuální počítač se systémem Linux)](tutorial-video-analytics-iot-edge-vm.md) , nebo [kurz: vytvoření instance IoT Edge pro kurz analýzy NUC (Intel)](tutorial-video-analytics-iot-edge-nuc.md) na:
    - Vytvořte virtuální počítač Azure s nainstalovaným modulem runtime Azure IoT Edge. – připraví instalaci IoT Edge na hostování modulu video Analytics.
    - Připojte zařízení IoT Edge k aplikaci IoT Central.

1. Dokončete kurz [monitorování a správy aplikace video Analytics](tutorial-video-analytics-manage.md) :
    - Přidejte kamery detekce objektů a pohybu do brány ve vaší aplikaci IoT Central.
    - Spusťte zpracování kamery.
    - Pokud chcete zobrazit zaznamenané video v AMS, nainstalujte místní přehrávač médií.
    - Zobrazí zachycené video, které zobrazuje zjištěné objekty.
    - Uklizený nahoru.

## <a name="next-steps"></a>Další kroky

Teď máte přehled o tom, jak nasadit a používat šablonu aplikace video Analytics, přečtěte si téma [Vytvoření aplikace video Analytics v azure IoT Central (Yolo V3)](tutorial-video-analytics-create-app-yolo-v3.md) nebo [vytvoření video analytics v Azure IoT Central (OpenVINO &trade; )](tutorial-video-analytics-create-app-openvino.md) , abyste mohli začít.
