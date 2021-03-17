---
title: Modely AI pro Azure Percept
description: Další informace o modelech AI dostupných pro vytváření prototypů a nasazování
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: template-concept
ms.openlocfilehash: d0cdabb3b22d642a7903810181106b09c549e1a2
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218158"
---
# <a name="azure-percept-ai-models"></a>Modely AI pro Azure Percept

Azure Percept umožňuje vyvíjet a nasazovat modely AI přímo do Azure Percept DK z [Azure Percept studia](https://go.microsoft.com/fwlink/?linkid=2135819). Nasazení modelu využívá [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) a [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/#iotedge-overview).

## <a name="sample-ai-models"></a>Ukázkové modely AI

Azure Percept Studio obsahuje ukázkové modely pro následující aplikace:

- detekce lidí
- detekce vozidel
- Obecné zjišťování objektů
- zjišťování produktů na polici

S předem vyškolenými modely není nutné vytvářet žádné kódování ani školicí shromažďování dat. Jednoduše nasaďte požadovaný model do Azure Percept DK z portálu a otevřete Stream videa DevKit, abyste viděli model Inferencing v akci. K Inferencing telemetrie se dá dostat taky prostřednictvím nástroje [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases) .

## <a name="reference-solutions"></a>Referenční řešení

K dispozici je také [řešení pro počítání odkazů lidí](https://github.com/microsoft/Azure-Percept-Reference-Solutions/tree/main/people-detection-app) . Toto referenční řešení je open source aplikace AI, která poskytuje hraniční uživatele s událostmi vstupu a výstupu zóny definované uživatelem. Výstup videa a AI z místního hraničního zařízení je z provozu [Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/)s uživatelským rozhraním, které běží jako web Azure. Inferencing AI poskytuje Open Source model AI pro detekci osob.

:::image type="content" source="./media/overview-ai-models/people-detector.gif" alt-text="Předem připravené soubory GIF řešení prostorových analýz":::

## <a name="custom-no-code-solutions"></a>Vlastní řešení bez kódu

Prostřednictvím Azure Percept studia můžete vyvíjet vlastní řešení pro [vize](./tutorial-nocode-vision.md) a rozpoznávání řeči, nevyžaduje se žádné kódování.

Pro vlastní řešení Vision jsou k dispozici oba modely pro detekci a klasifikaci AI. Jednoduše nahrajte a označte své školicí obrázky, které se dají přímo s Azure Percept Vision SoM v Azure Percept DK, pokud je to potřeba. Školení a vyhodnocení modelu se snadno provádí v [Custom Vision](https://www.customvision.ai/), což je součástí [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/#overview).

Pro vlastní řešení pro rozpoznávání řeči jsou aktuálně k dispozici šablony hlasového asistenta pro následující aplikace:

- Pohostinství: hotelová místnost vybavená inteligentními zařízeními řízenými hlasem.
- Zdravotní péče: péče vybavené hlasem řízeným inteligentním zařízením.
- Inventář: centrum inventáře vybavené pomocí inteligentních zařízení řízených hlasem.
- Automobil: automobilový náboj vybavený hlasem řízenými inteligentními zařízeními.

Předem vytvořená klíčová slova a příkazy hlasového asistenta jsou k dispozici přímo prostřednictvím portálu. Vlastní klíčová slova a příkazy mohou být vytvořeny a vyškoleny v nástroji [Speech Studio](https://speech.microsoft.com/), který je také součástí služby Azure Cognitive Services.

## <a name="advanced-development"></a>Pokročilý vývoj

Podrobné pokyny, kurzy a příklady pro tyto věci najdete v tématu [Azure PERCEPT DK Advanced Development GitHub](https://github.com/microsoft/azure-percept-advanced-development) .

* Zavedení vlastního modelu AI do zařízení
* Aktualizace modelu, který už podporujeme s učením přenosu
* A další
