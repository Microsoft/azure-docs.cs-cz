---
title: Použití sady Azure Kinect snímače SDK k záznamu formátu souboru
description: Seznamte se s tím, jak použít formát nahraného souboru sady Kinect senzor sady Azure.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: reference
keywords: Kinect, Azure, senzor, SDK, Hloubka, RGB, záznam, přehrávání, Matroska, MKV
ms.openlocfilehash: f4fa14b0841cb76b2ba191310ecbca312d29f805
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654587"
---
# <a name="use-azure-kinect-sensor-sdk-to-record-file-format"></a>Použití sady Azure Kinect snímače SDK k záznamu formátu souboru

Chcete-li zaznamenávat data ze senzorů, je použit formát kontejneru Matroska (. MKV), který umožňuje uložení více stop pomocí široké škály kodeků. Soubor nahrávky obsahuje stopy pro ukládání barev, hloubky, obrázků IR a IMU.

Podrobnosti nízké úrovně formátu kontejneru. MKV najdete na [webu Matroska](https://www.matroska.org/index.html).

| Název stopy | Formát kodeku                          |
|------------|---------------------------------------|
| BAREVNÝCH      | Mode-Dependent (MJPEG, NV12 úrovně nebo YUY2) |
| ÚROVNÍ      | b16g (16 bitů ve stupních šedi, Big Endian)   |
| IR         | b16g (16 bitů ve stupních šedi, Big Endian)   |
| IMU        | Vlastní struktura viz [imu Sample Structure](record-file-format.md#imu-sample-structure) níže. |

## <a name="using-third-party-tools"></a>Používání nástrojů třetích stran

Nástroje, jako je například `ffmpeg` nebo `mkvinfo` příkaz ze sady nástrojů [mkvtoolnix](https://mkvtoolnix.download/) , lze použít k zobrazení a extrakci informací ze souborů záznamu.

Například následující příkaz extrahuje hloubkovou stopu jako sekvenci 16bitového PNGs do stejné složky:

```
ffmpeg -i output.mkv -map 0:1 -vsync 0 depth%04d.png
```

`-map 0:1`Parametr extrahuje index sledování 1, který pro většinu nahrávek bude hloubka. Pokud záznam neobsahuje barevnou stopu, bude `-map 0:0` použit.

`-vsync 0`Parametr vynutí, aby ffmpeg extrakci snímků jako nedošlo k pokusu o porovnávací snímkový kmitočet 30 fps, 15 fps nebo 5 fps.

## <a name="imu-sample-structure"></a>Ukázková struktura IMU

Pokud jsou data IMU extrahována ze souboru bez použití rozhraní API pro přehrávání, budou data v binárním formátu.
Struktura dat IMU je níže. Všechna pole jsou Little endian.

| Pole                        | Typ     |
|------------------------------|----------|
| Časové razítko akcelerometru (μs) | UInt64   |
| Data akcelerometru (x, y, z) | float [3] |
| Časové razítko vybavený gyroskopem (μs)     | UInt64   |
| Data vybavený gyroskopem (x, y, z)     | float [3] |

## <a name="identifying-tracks"></a>Identifikace stop

Může být nutné určit, která stopa obsahuje barvu, hloubku, IR a tak dále. Identifikace stop je nutná při práci s nástroji třetích stran pro čtení souboru Matroska.
Čísla sledování se liší v závislosti na režimu kamery a sadě povolených běhů. Značky slouží k identifikaci významu jednotlivých stop.

Seznam značek níže je připojen k určitému prvku Matroska a lze jej použít k vyhledání odpovídající stopy nebo přílohy.

Tyto značky jsou zobrazitelné pomocí nástrojů, jako jsou `ffmpeg` a `mkvinfo` .
Úplný seznam značek je uveden na stránce [záznam a přehrávání](record-playback-api.md) .

| Název značky             | Označit jako cíl             | Hodnota značky             |
|----------------------|------------------------|-----------------------|
| K4A_COLOR_TRACK      | Barevný záznam            | UID Matroska záznamu    |
| K4A_DEPTH_TRACK      | Hloubka hloubky            | UID Matroska záznamu    |
| K4A_IR_TRACK         | INFRAČERVENá stopa               | UID Matroska záznamu    |
| K4A_IMU_TRACK        | IMU sledovat              | UID Matroska záznamu    |
| K4A_CALIBRATION_FILE | Příloha kalibrace | Název souboru přílohy   |

## <a name="next-steps"></a>Další kroky

[Záznam a přehrávání](record-playback-api.md)
