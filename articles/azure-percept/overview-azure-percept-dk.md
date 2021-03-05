---
title: Přehled Azure Percept DK
description: Další informace o Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: c93ecdc138e757f84e995a69a6c3d7f1b5460abe
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179370"
---
# <a name="azure-percept-dk-overview"></a>Přehled Azure Percept DK

Azure Percept DK je špičková sada souborů AI a IoT Development Kit navržená pro vývoj konceptů a audio AI. V kombinaci se službou [Azure Percept Studio](./overview-azure-percept-studio.md) a [službou Azure Percept audio](./overview-azure-percept-audio.md)se to znamená, že se ještě nejedná o výkonnou platformu, kterou je možné snadno použít k vytváření řešení AI na Edge pro nejrůznější aplikace v oblasti Vision nebo audio AI. K nákupu můžete použít [online obchod Microsoftu](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

:::image type="content" source="./media/overview-azure-percept-dk/dk-image.png" alt-text="Zařízení Azure Percept DK.":::

## <a name="key-features"></a>Klíčové funkce

- **Možnost spouštět AI na hraničních** zařízeních. Integrovaná hardwarová akcelerace může spouštět modely aplikace Vision AI bez připojení ke cloudu.
- **Kořenový adresář hardwaru integrovaného zabezpečení důvěryhodnosti**. Další podrobnosti najdete v tomto přehledu o [zabezpečení Azure Percept](./overview-percept-security.md) .
- **Bezproblémová integrace se službou [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)** a dalšími službami Azure. Například Azure IoT Hub, Azure Cognitive Services a [Live video Analytics](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/overview)
- **Bezproblémová integrace s volitelným [zvukem Azure Percept](./overview-azure-percept-audio.md)**
- **Podpora hlavních platforem AI**. Například ONNX a TensorFlow.
- **Integrace se systémem 80/20 kolejnice**. Vytváření prototypů v produkčních prostředích usnadňuje. Přečtěte si další informace o [integraci 80/20](./overview-8020-integration.md).

## <a name="hardware-components"></a>Hardwarové součásti

- Deska dopravců Azure Percept DK
    - Procesor NXP iMX8m
    - ČIP TPM (Trusted Platform Module) verze 2,0
    - Připojení Wi-Fi a Bluetooth
    - Zobrazit úplný [datový list](./azure-percept-dk-datasheet.md)
- Azure Percept visioning v modulu (SoM)
    - Intel Movidius nesčetných X (MA2085) Vision Processing Unit (VPU)
    - Senzor kamery RGB s možností přidat druhý
    - Zobrazit úplný [datový list](./azure-percept-vision-datasheet.md)

## <a name="get-started-with-the-azure-percept-dk"></a>Začínáme s Azure Percept DK

- Dokončete tyto rychlé starty.
    - [Unbox a sestavte Azure Percept DK](./quickstart-percept-dk-unboxing.md)
    - [Nastavte si Azure Percept DK a spusťte svůj první visionový model AI.](./quickstart-percept-dk-set-up.md)
- Zahájení vytváření důkazů konceptů pomocí těchto kurzů
    - [Vytvoření řešení pro vize bez kódu v Azure Percept studiu](./tutorial-nocode-vision.md)
    - [Vytvoření hlasového asistenta v Azure Percept studiu](./tutorial-no-code-speech.md)

## <a name="next-steps"></a>Další kroky

Objednat si Azure Percept DK v [online obchodě Microsoftu](https://go.microsoft.com/fwlink/p/?LinkId=2155270).
