---
title: Výsledky sledování textu ve službě Azure Kinect
description: Naučte se, jak získat výsledky sledování těla pomocí sady Kinect pro sledování textu v Azure.
author: qm13
ms.prod: kinect-dk
ms.author: quentinm
ms.reviewer: yijwan
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, snímač, SDK, tělo, sledování, společné
ms.openlocfilehash: 1b62022242144d5db51455a32ac04b67c3e5dd7a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277214"
---
# <a name="get-body-tracking-results"></a>Získání výsledků sledování pohybu těla

Sada SDK pro sledování textu používá objekt sledování textu ke zpracování Azure Kinect DK zachycuje a generuje výsledky sledování textu. Udržuje taky globální stav sledování, zpracování front a výstupní fronty. Existují tři kroky při použití sledování textu:

- Vytvoření sledovacího modulu
- Hloubka zachycení a obrázky IR pomocí Azure Kinect DK
- Zařadit do fronty zachycení a zobrazit výsledky.

## <a name="create-a-tracker"></a>Vytvoření sledovacího modulu


Prvním krokem při použití sledování textu je vytvoření sledovacího modulu, který vyžaduje předání [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html) struktury senzoru pro kalibraci. K kalibraci senzorů se dá dotázat pomocí funkce [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) sady snímačů pro Azure Kinect.

```C
k4a_calibration_t sensor_calibration;
if (K4A_RESULT_SUCCEEDED != k4a_device_get_calibration(device, device_config.depth_mode, K4A_COLOR_RESOLUTION_OFF, &sensor_calibration))
{
    printf("Get depth camera calibration failed!\n");
    return 0;
}

k4abt_tracker_t tracker = NULL;
k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
if (K4A_RESULT_SUCCEEDED != k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker))
{
    printf("Body tracker initialization failed!\n");
    return 0;
}
```

## <a name="capture-depth-and-ir-images"></a>Hloubka zachycení a obrázky IR

Záznam imagí pomocí Azure Kinect DK je popsaný na stránce [načíst bitové kopie](retrieve-images.md) .

>[!NOTE]
> `K4A_DEPTH_MODE_NFOV_UNBINNED``K4A_DEPTH_MODE_WFOV_2X2BINNED`pro nejlepší výkon a přesnost jsou Doporučené režimy. Nepoužívejte `K4A_DEPTH_MODE_OFF` `K4A_DEPTH_MODE_PASSIVE_IR` režimy nebo.

Podporované režimy Azure Kinect DK jsou popsané v tématu [specifikace hardwaru](hardware-specification.md) Azure Kinect DK a výčty [k4a_depth_mode_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga3507ee60c1ffe1909096e2080dd2a05d.html#ga3507ee60c1ffe1909096e2080dd2a05d) .

```C
// Capture a depth frame
switch (k4a_device_get_capture(device, &capture, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a capture\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a capture\n");
    goto Exit;
}
```

## <a name="enqueue-the-capture-and-pop-the-results"></a>Zařadit do fronty zachycené a automaticky otevírané výsledky

Modul pro sledování interně udržuje vstupní frontu a výstupní frontu pro asynchronní zpracování Azure Kinect v DK efektivněji zachytit. Pomocí funkce [k4abt_tracker_enqueue_capture ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga093becd9bb4a63f5f4d56f58097a7b1e.html#ga093becd9bb4a63f5f4d56f58097a7b1e) přidejte nové zachycení do vstupní fronty. Použijte funkci [k4abt_tracker_pop_result ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gaaf446fb1579cbbe0b6af824ee0a7458b.html#gaaf446fb1579cbbe0b6af824ee0a7458b) o místní nabídce výsledek z výstupní fronty. Použití hodnoty timeout závisí na aplikaci a řídí dobu čekání ve frontě.

### <a name="real-time-processing"></a>Zpracování v reálném čase
Tento model použijte pro aplikace s jedním vláknem, které vyžadují výsledky v reálném čase a můžou obsahovat vyřazení snímků. Ukázka, která se `simple_3d_viewer` nachází na [GitHubu Azure-Kinect-Samples](https://github.com/microsoft/Azure-Kinect-Samples) , je příkladem zpracování v reálném čase.

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, 0);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
{
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, 0);
if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
{
    // Successfully popped the body tracking result. Start your processing
    ...

    k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
}
```

### <a name="synchronous-processing"></a>Synchronní zpracování
Tento model použijte pro aplikace, které nepotřebují výsledky v reálném čase, nebo nemůžou vyhovovat vyřazeným snímkům.

Propustnost zpracování může být omezená.

Ukázka, která se `simple_sample.exe` nachází na [GitHubu Azure-Kinect-Samples](https://github.com/microsoft/Azure-Kinect-Samples) , je příkladem synchronního zpracování.

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
if (pop_frame_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Popping body tracking result failed!\n");
    break;
}
// Successfully popped the body tracking result. Start your processing
...

k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Přístup k datům ve snímku těla](access-data-body-frame.md)
