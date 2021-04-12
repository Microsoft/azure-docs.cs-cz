---
title: Porovnání oken Azure Kinect DK
description: Rozdíly v různých hardwarových a softwarových systémech pro Azure Kinect DK a Kinect pro Windows v2
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: Kinect, Windows, v2, Azure Kinect, porovnání, SDK, rozdíly, hardware, software
ms.openlocfilehash: 1791767e842043bbb0eda058cc3ea92be90a00fb
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078294"
---
# <a name="azure-kinect-and-kinect-windows-v2-comparison"></a>Porovnání Azure Kinect a Kinect Windows v2

Nástroje pro vývoj hardwaru a softwaru ve službě Azure Kinect DK mají rozdíly oproti Kinect pro Windows v2. Všechny existující aplikace Kinect pro Windows v2 nebudou pracovat přímo se službou Azure Kinect DK a budou vyžadovat přenos do nové sady SDK.  

## <a name="hardware"></a>Hardware

V následující tabulce jsou uvedeny rozdíly vysoké úrovně mezi službou Azure Kinect Development Kit a Kinect pro Windows v2.

| Funkce | Typ | Azure Kinect DK | Kinect pro Windows v2 |
| ------- | ---- | --------------- | --------------------- |
| **Zvuk** | Podrobnosti  | 7. kruhové pole MIC | 4 – lineární pole s podporou MIC |
| **Snímač pohybu** | Podrobnosti | Gyro na 3 osy akcelerometr – osa 3 | akcelerometr – 3 osy |
| **Kamera RGB**    | Podrobnosti | 3840 x 2160 px @30 FPS | 1920 x 1080 px @30 FPS |
| **Hloubka kamery**  | Metoda   | Čas letu | Čas letu |
|                   | Řešení | 640 x 576 px @30 FPS | 512 x 424 px @ 30 fps |
|                   |            | 512 x 512 px @30 FPS |                       |
|                   |            | 1024x1024 px @15 FPS |                       |
| **Připojení** | Data | USB 3.1 Gen 1 s typem USB-C  | USB 3,1 Gen 1|
|  | Napájení | Externí PSU nebo USB-C | Externí PSU |
|  | Synchronizace | Hloubka & RGB interní, externí zařízení – zařízení| Pouze interní hloubka & RGB |
| **Dřev** | Dimenze | 103 × 39 × 126 mm | 249 x 66 × 67 mm |
|  | Hromadné | 440 g | 970 g |
| | Připojení | One 1/4-20 UNC. Čtyři interní šroubové body | One 1/4-20 UNC |

Další podrobnosti najdete v [hardwarovém dokumentu Azure Kinect DK](hardware-specification.md) .

## <a name="sensor-access"></a>Přístup ke senzoru

Následující tabulka poskytuje porovnání funkcí přístupu snímače zařízení nízké úrovně.

| **Funkce**| **Azure Kinect** | **Kinect pro Windows** | **Poznámky** |
|---------|---------|------------|---------|
| **Úrovní** | ✔️ | ✔️ |    | 
| **IR** | ✔️ | ✔️ |  |
| **Barva** | ✔️ | ✔️ | Formát barev podporuje rozdíly, Azure Kinect DK podporuje tyto ovládací prvky kamery: expozici, vyvážení bílé, jas, kontrast, sytost, ostrost a řízení získání. |
| **Zvuk** | ✔️ | ✔️ | K Azure Kinect DK MICS se dostanete prostřednictvím sady Speech SDK nebo rozhraní API systému Windows Native. |
| **IMU** | ✔️ |  | Azure Kinect DK má plné IMU na ose 6 a Kinect pro Windows poskytuje jenom 1 osu. |
| **Data kalibrace** | ✔️ | ✔️ | Kalibrace modelu kamery kompatibilního s OpenCV |
| **Hloubka – interní synchronizace RGB** | ✔️ | ✔️ |  |
| **Externí synchronizace**| ✔️|  | Azure Kinect DK umožňuje programovatelné zpoždění pro externí synchronizaci. |
| **Sdílení přístupu s více klienty** | | ✔️ | Sada Kinect SDK pro Azure se spoléhá na WinUSB/libUSB pro přístup k zařízení a nemá implementovánou službu, která umožňuje sdílení přístupu k zařízení s více procesy. |
| **Nástroj záznam/přehrávání datového proudu** | ✔️ | ✔️ | Azure Kinect DK používá open source implementaci založené na kontejnerech Matroska. |

## <a name="features"></a>Funkce

Sada funkcí sady Azure Kinect SDK se liší od Kinect pro Windows v2, jak je popsáno níže:

| **Funkce Kinect v2** | **Datový typ Kinect v2** | **Sada Azure Kinect SDK/služba** |
|--------|--------|------|
| Přístup k datům ze senzorů |DepthFrame| [SADA senzorů – načtení imagí](retrieve-images.md) 
| |InfraredFrame | [SADA senzorů – načtení imagí](retrieve-images.md) 
| | ColorFrame | [SADA senzorů – načtení imagí](retrieve-images.md) | 
| | AudioBeamFrame |Aktuálně se nepodporuje. 
| Sledování textu | BodyFrame | Sada SDK pro sledování textu |
| | BodyIndexFrame | Sada SDK pro sledování textu  |
| Mapování souřadnic|CoordinateMapper| [SADA senzorů – transformace obrázků](use-image-transformation.md) |
|Sledování obličeje | FaceFrame | [Cognitive Services: Face](https://azure.microsoft.com/services/cognitive-services/face/)       |
|    Rozpoznávání řeči    |    –                      |    [Cognitive Services: řeč](https://azure.microsoft.com/services/cognitive-services/directory/speech/)     |

## <a name="next-steps"></a>Další kroky

[Kinect pro vývojářské stránky pro Windows](https://developer.microsoft.com/windows/kinect)
