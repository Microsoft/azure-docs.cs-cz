---
title: Konfigurace pole mikrofonu – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Naučte se konfigurovat pole mikrofonu tak, aby ho sada Speech zařízení SDK mohla používat.
services: cognitive-services
author: mswellsi
manager: yanbo
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: wellsi
ms.openlocfilehash: cf0580c96f5bf78f0444b2bb39088f2a417fd658
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95025057"
---
# <a name="how-to-configure-a-microphone-array"></a>Jak nakonfigurovat pole mikrofonů

V tomto článku se dozvíte, jak nakonfigurovat [pole mikrofonu](./speech-devices-sdk-microphone.md). Zahrnuje nastavení pracovního úhlu a výběr, který mikrofon se používá pro sadu Speech Devices SDK.

Sada Speech Devices SDK funguje nejlépe s polem mikrofonu navrženým podle [našich pokynů](./speech-devices-sdk-microphone.md). Konfiguraci pole mikrofonu může poskytovat operační systém nebo dodávat pomocí jedné z následujících metod.

Sada Speech Devices SDK zpočátku podporovala pole mikrofonu výběrem z pevné sady konfigurací.

```java
private static String DeviceGeometry = "Circular6+1"; // "Circular6+1", "Linear4",
private static String SelectedGeometry = "Circular6+1"; // "Circular6+1", "Circular3+1", "Linear4", "Linear2"
```

V systému Windows je konfigurace pole mikrofonu dodána ovladačem zvuku.

Sada Speech Devices SDK v 1.11.0 také podporuje konfiguraci ze [souboru JSON](https://aka.ms/sdsdk-micarray-json).


## <a name="windows"></a>Windows
V systému Windows jsou informace o geometrii pole mikrofonu automaticky získány ze zvukového ovladače. Vlastnosti `DeviceGeometry` ,  `SelectedGeometry` a `MicArrayGeometryConfigFile` jsou tedy volitelné. K získání rozsahu beamforming používáme [soubor JSON](https://aka.ms/sdsdk-micarray-json) , který je k dispozici pomocí `MicArrayGeometryConfigFile` .

Pokud je zadáno pole mikrofonu pomocí `AudioConfig::FromMicrophoneInput` , používáme určený mikrofon. Pokud mikrofon není zadán nebo `AudioConfig::FromDefaultMicrophoneInput` je zavolán, použijeme výchozí mikrofon, který je určen v nastavení zvuku ve Windows.
Microsoft audio Stack v sadě Speech Devices SDK podporuje jenom vzorkování vzorků, které jsou integrálními násobky 16 KHz.

## <a name="linux"></a>Linux
V systému Linux musí být k dispozici informace o geometrii mikrofonu. Použití `DeviceGeometry` a `SelectedGeometry` zůstane podporováno. Lze ji také poskytnout prostřednictvím souboru JSON pomocí `MicArrayGeometryConfigFile` Vlastnosti. Podobně jako v systému Windows může být beamforming rozsah poskytnutý souborem JSON.

Pokud je zadáno pole mikrofonu pomocí `AudioConfig::FromMicrophoneInput` , používáme určený mikrofon. Pokud mikrofon není zadán nebo `AudioConfig::FromDefaultMicrophoneInput` je zavolán, pošleme záznam ze zařízení ALSA s názvem *Default*. Ve výchozím nastavení se *výchozí nastavení* vždy odkazuje na kartu 0 zařízení 0, ale uživatelé je můžou v `asound.conf` souboru změnit. 

Microsoft audio Stack v sadě Speech Devices SDK podporuje pouze převzorkování pro vzorkovací sazby, které jsou integrálními násobky 16 KHz. Kromě toho jsou podporovány následující formáty: 32-bit IEEE Little endian float, 32-bit Little endian signed int, 24 bitů Little-signed int, 16 bitů Little endian signed int a 8bitové celé číslo se znaménkem.

## <a name="android"></a>Android
Sada Speech Devices SDK podporuje aktuálně pouze [Roobo v1](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android) . Chování je stejné jako v předchozích verzích, s výjimkou toho, že `MicArrayGeometryConfigFile` vlastnost Now lze použít k určení souboru JSON obsahujícího rozsah beamforming.

## <a name="microphone-array-configuration-json"></a>Formát JSON pro konfiguraci pole mikrofonu

Soubor JSON pro konfiguraci geometrie pole mikrofonu bude následovat po [schématu JSON](https://aka.ms/sdsdk-micarray-json). Níže jsou uvedeny některé příklady, které následují po schématu.


```json
{
    "micArrayType": "Linear",
    "geometry": "Linear4"
}
```


Nebo


```json
{
    "micArrayType": "Planar",
    "horizontalAngleBegin": 0,
    "horizontalAngleEnd": 360,
    "numberOfMicrophones": 4,
    "micCoord": [
        {
            "xCoord": 0,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": 40,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": -35,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": 35,
            "zCoord": 0
        }
    ]
}
```

Nebo

```json
{
    "micArrayType": "Linear",
    "horizontalAngleBegin": 70,
    "horizontalAngleEnd": 110
}
```


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Volba zařízení pro rozpoznávání řeči](get-speech-devices-sdk.md)