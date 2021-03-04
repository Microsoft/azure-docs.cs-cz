---
title: Modely AI pro Azure Percept
description: Další informace o modelech AI dostupných pro vytváření prototypů a nasazování
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: template-concept
ms.openlocfilehash: 28a8de231f179cf69342da81e6a2ae1989d2a5d6
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041582"
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

## <a name="pre-built-solutions"></a>Předem připravená řešení

K dispozici je také [předem připravené řešení pro zjišťování prostorových analýz pro lidi](https://github.com/george-moore/Santa-Cruz-AI-App) . Předem připravené řešení je open source aplikace AI, která poskytuje hraniční uživatele s událostmi vstupu a výstupu zóny definované uživatelem. Výstup videa a AI z místního hraničního zařízení je z provozu [Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/)s uživatelským rozhraním, které běží jako web Azure. Inferencing AI poskytuje Open Source model AI pro detekci osob.

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

V případě pokročilých vývojářů je dostupný [Jupyter Poznámkový blok](https://github.com/microsoft/Project-Santa-Cruz-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) přenosu dat pomocí předem připraveného TensorFlow modelu (MobileNetSSDV2Lite) v Pythonu s vlastní datovou sadou pro detekci objektů. Poznámkový blok využívá vzdálené výpočetní instance prostřednictvím [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/#product-overview) a dá se spouštět v cloudu pomocí portálu AzureML nebo místně v [Visual Studio Code](https://code.visualstudio.com/).

K dispozici jsou také některé užitečné [skripty](https://github.com/microsoft/Project-Santa-Cruz-Preview/tree/main/Sample-Scripts-and-Notebooks/Official/Scripts) Pythonu pro správu datových sad a [instalační program sady nástrojů pro vývojáře](https://github.com/microsoft/Project-Santa-Cruz-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/dev-tools-installer.md), který nainstaluje a nakonfiguruje všechny nástroje, které jsou nezbytné pro vývoj pokročilého řešení AI.
