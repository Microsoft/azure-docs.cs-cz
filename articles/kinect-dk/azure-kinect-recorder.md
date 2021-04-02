---
title: Záznam Azure Kinect DK
description: Seznamte se s postupem, jak zaznamenávat datové proudy ze sady senzorů do souboru pomocí zapisovače Azure Kinect.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Recording, přehrávání, čtenář, Matroska, MKV, Streams, Depth, RGB, fotoaparát, Color, imu, audio
ms.openlocfilehash: 5fb6895d4102956a991c67ffab836a26b7a3abb0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277159"
---
# <a name="azure-kinect-dk-recorder"></a>Záznam Azure Kinect DK

Tento článek popisuje, jak můžete pomocí `k4arecorder` nástroje příkazového řádku zaznamenat datové proudy ze sady senzorů do souboru.

>[!NOTE]
>Záznam služby Azure Kinect nezaznamenává zvuk.

## <a name="recorder-options"></a>Možnosti záznamu

`k4arecorder`Obsahuje různé argumenty příkazového řádku, které určují výstupní soubor a režimy záznamu.

Nahrávky se ukládají ve [formátu Matroska. MKV](record-file-format.md). Záznam používá více videí pro barvy a hloubku a také další informace, jako je například kalibrace a metadata kamery.

```console
k4arecorder [options] output.mkv

 Options:
  -h, --help              Prints this help
  --list                  List the currently connected K4A devices
  --device                Specify the device index to use (default: 0)
  -l, --record-length     Limit the recording to N seconds (default: infinite)
  -c, --color-mode        Set the color sensor mode (default: 1080p), Available options:
                            3072p, 2160p, 1536p, 1440p, 1080p, 720p, 720p_NV12, 720p_YUY2, OFF
  -d, --depth-mode        Set the depth sensor mode (default: NFOV_UNBINNED), Available options:
                            NFOV_2X2BINNED, NFOV_UNBINNED, WFOV_2X2BINNED, WFOV_UNBINNED, PASSIVE_IR, OFF
  --depth-delay           Set the time offset between color and depth frames in microseconds (default: 0)
                            A negative value means depth frames will arrive before color frames.
                            The delay must be less than 1 frame period.
  -r, --rate              Set the camera frame rate in Frames per Second
                            Default is the maximum rate supported by the camera modes.
                            Available options: 30, 15, 5
  --imu                   Set the IMU recording mode (ON, OFF, default: ON)
  --external-sync         Set the external sync mode (Master, Subordinate, Standalone default: Standalone)
  --sync-delay            Set the external sync delay off the master camera in microseconds (default: 0)
                            This setting is only valid if the camera is in Subordinate mode.
  -e, --exposure-control  Set manual exposure value (-11 to 1) for the RGB camera (default: auto exposure)
```

## <a name="record-files"></a>Soubory záznamů

Příklad 1 NFOV režim unbinned (640x576), RGB 1080p při 30 fps s IMU.
Stisknutím kláves **CTRL-C** zastavte nahrávání.

```
k4arecorder.exe output.mkv
```

Příklad 2. Záznam WFOV non-rozdělený (1MP), RGB 3072p při 15 fpsch bez IMU, po dobu 10 sekund.

```
k4arecorder.exe -d WFOV_UNBINNED -c 3072p -r 15 -l 10 --imu OFF output.mkv
```

Příklad 3. Zaznamenejte WFOV 2x2 rozdělený při 30 fps po dobu 5 sekund a uložte ji do souboru Output. mkv.

```
k4arecorder.exe -d WFOV_2X2BINNED -c OFF --imu OFF -l 5 output.mkv
```

>[!TIP]
>Pomocí služby [Azure Kinect Viewer](azure-kinect-viewer.md) můžete před záznamem nakonfigurovat ovládací prvky kamery RGB (například nastavit ruční vyvážení bílé).

## <a name="verify-recording"></a>Ověřit záznam

Výstupní soubor. MKV můžete otevřít pomocí [Azure Kinect Vieweru](azure-kinect-viewer.md).

K extrakci skladeb nebo zobrazení informací o souboru `mkvinfo` jsou k dispozici nástroje jako součást sady [mkvtoolnix](https://mkvtoolnix.download/) Toolkit.

## <a name="next-steps"></a>Další kroky

[Používání záznamu s externími synchronizovanými jednotkami](record-external-synchronized-units.md)