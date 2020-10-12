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
ms.openlocfilehash: d661df57e4409c1d7fe196c7f136965191421bd4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88719654"
---
# <a name="how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>Postup nasazení IoT Central aplikace pomocí šablony video Analytics – objekt a detekce pohybu

Přehled klíčových komponent aplikace *video Analytics – objekt a pohyb pohybu* najdete v tématu [Architektura aplikace analýzy objektů a pohybů](architecture-video-analytics.md).

## <a name="deploy-the-application"></a>Nasazení aplikace

Provedením následujících kroků nasadíte IoT Central aplikaci pomocí šablony aplikace video Analytics:

1. Dokončete kurz [Vytvoření aplikace video Analytics v Azure IoT Central](tutorial-video-analytics-create-app.md) kurzu:
    - Vytvoření účtu Azure Media Services
    - Vytvořte aplikaci IoT Central z šablony video Analytics – objekt a detekce pohybu.
    - Nakonfigurujte zařízení brány v IoT Central aplikaci. Brána umožňuje zařízením kamery připojit se k aplikaci.

1. Dokončete kurz [vytvoření instance IoT Edge pro video Analytics (virtuální počítač se systémem Linux)](tutorial-video-analytics-iot-edge-vm.md) a proveďte tyto kroky:
    - Vytvořte virtuální počítač Azure s nainstalovaným modulem runtime Azure IoT Edge. – připraví instalaci IoT Edge na hostování modulu video Analytics.
    - Připojte zařízení IoT Edge k aplikaci IoT Central.

1. Dokončete kurz [monitorování a správy aplikace video Analytics](tutorial-video-analytics-manage.md) :
    - Přidejte kamery detekce objektů a pohybu do brány ve vaší aplikaci IoT Central.
    - Spusťte zpracování kamery.
    - Pokud chcete zobrazit zaznamenané video v AMS, nainstalujte místní přehrávač médií.
    - Zobrazí zachycené video, které zobrazuje zjištěné objekty.
    - Uklizený nahoru.

## <a name="next-steps"></a>Další kroky

Teď máte přehled o tom, jak nasadit a používat šablonu aplikace video Analytics, v tématu [Vytvoření aplikace video Analytics v Azure IoT Central](tutorial-video-analytics-create-app.md) , abyste mohli začít.
