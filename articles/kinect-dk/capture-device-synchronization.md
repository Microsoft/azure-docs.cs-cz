---
title: Zaznamenat synchronizaci zařízení s Kinect Azure
description: Naučte se synchronizovat zařízení se systémem Azure Kinect Capture pomocí sady Azure Kinect snímač SDK.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, senzor, SDK, Hloubka, RGB, interní, externí, synchronizace, uzavřené řetězení, posun fáze
ms.openlocfilehash: ce0c72d3d708d5696a9775b3885f278f0c23cac1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034301"
---
# <a name="capture-azure-kinect-device-synchronization"></a>Zaznamenat synchronizaci zařízení s Kinect Azure

Hardware Azure Kinect může zarovnávat dobu zachycení barev a hloubkových obrázků. Mezi fotoaparáty na jednom zařízení je **interní synchronizace**. Řazení času zachycení mezi několika připojenými zařízeními je **externí synchronizace**. Pole Microphone funguje nezávisle na barvách a hloubce kamery.

## <a name="device-internal-synchronization"></a>Interní synchronizace zařízení

Zachycení obrázku mezi jednotlivými fotoaparáty se synchronizuje v hardwaru. V každém [k4a_capture_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__capture__t.html) , který obsahuje obrázky ze senzoru barev i hloubky, jsou časová razítka obrázků zarovnána na základě operačního režimu hardwaru. Ve výchozím nastavení jsou obrázky zachycení zarovnány na střed. Relativní časování hloubky a zachytávání barev lze upravit pomocí `depth_delay_off_color_usec` pole [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html).

## <a name="device-external-synchronization"></a>Externí synchronizace zařízení

Viz [nastavení externí synchronizace](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices) pro instalaci hardwaru.

Software pro každé připojené zařízení musí být nakonfigurovaný tak, aby fungoval v **hlavním** nebo **podřízeném** režimu. Toto nastavení je konfigurováno na [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html).

Při použití externí synchronizace by měly být podřízené kamery vždy spouštěny před tím, než hlavní časové razítko bude správně zarovnáno.

### <a name="subordinate-mode"></a>Podřízený režim

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_SUBORDINATE
```

### <a name="master-mode"></a>Hlavní režim

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_MASTER;
```

### <a name="retrieving-synchronization-jack-state"></a>Načítá se stav konektoru synchronizace.

Chcete-li programově načíst aktuální stav vstupních a synchronizačních konektorů synchronizace, použijte funkci [k4a_device_get_sync_jack](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0209ac87bfd055163677321b0304e962.html#ga0209ac87bfd055163677321b0304e962) .

## <a name="next-steps"></a>Další kroky

Nyní víte, jak povolit a zachytit synchronizaci zařízení. Můžete si také projít, jak použít 

>[!div class="nextstepaction"]
>[Rozhraní API pro záznam a přehrávání sady SDK pro Azure Kinect snímače](record-playback-api.md)
