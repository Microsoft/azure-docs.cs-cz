---
title: 'Kurz: nasazení šablony aplikace video Analytics – objekt a pohyb v Azure IoT Central'
description: Kurz – Tento průvodce shrnuje postup nasazení aplikace Azure IoT Central pomocí šablony aplikace pro video Analytics – objekt a detekce pohybu.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: c578da7e83a39f84e72b550038bd87dde3c61d28
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727460"
---
# <a name="tutorial-how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>Kurz: jak nasadit aplikaci IoT Central pomocí šablony aplikace pro video Analytics – objekt a detekce pohybu

Přehled klíčových komponent aplikace *video Analytics – objekt a pohyb pohybu* najdete v tématu [Architektura aplikace analýzy objektů a pohybů](architecture-video-analytics.md).

Následující video poskytuje návod, jak pomocí _šablony aplikace pro detekci video Analytics – objekt a pohyb_ nasadit řešení IoT Central:

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

V této sadě kurzů se dozvíte, jak:

> [!div class="checklist"]
> * Nasazení aplikace
> * Nasazení instance IoT Edge, která se připojuje k aplikaci
> * Monitorování a Správa aplikace

## <a name="prerequisites"></a>Požadavky

Doporučuje se předplatné Azure. Alternativně můžete použít bezplatnou, 7. zkušební verzi. Pokud nemáte předplatné Azure, můžete ho vytvořit na [stránce registrace do Azure](https://aka.ms/createazuresubscription).

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

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení práce s aplikací můžete odebrat všechny prostředky, které jste vytvořili následujícím způsobem:

1. V aplikaci IoT Central přejděte na stránku **aplikace** v části **Správa** . Vyberte **Odstranit**.
1. V Azure Portal odstraňte skupinu prostředků **lva-RG** .
1. V místním počítači zastavte kontejner Docker pro **amp Viewer** .

## <a name="next-steps"></a>Další kroky

Nyní máte přehled o krocích nasazení a použití šablony aplikace video Analytics, viz

> [!div class="nextstepaction"]
> [Vytvoření aplikace video Analytics v Azure IoT Central (Yolo V3)](tutorial-video-analytics-create-app-yolo-v3.md) nebo

> [!div class="nextstepaction"]
> Pokud chcete začít, [Vytvořte video Analytics v Azure IoT Central (OpenVINO &trade; )](tutorial-video-analytics-create-app-openvino.md) .