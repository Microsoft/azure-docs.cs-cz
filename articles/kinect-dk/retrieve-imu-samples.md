---
title: Načtení ukázek Azure Kinect IMU
description: Naučte se, jak načíst ukázky Azure Kinect IMU pomocí sady Azure Kinect SDK.
author: qm13
ms.author: cedmonds
ms.date: 06/26/2019
ms.prod: kinect-dk
ms.topic: conceptual
keywords: Kinect, Azure, konfigurace, Hloubka, barva, RBG, kamera, senzor, SDK, IMU, snímač pohybu, pohyb, vybavený gyroskopem, Gyro, akcelerometr, FPS
ms.openlocfilehash: 649dd5b9de62d43d59e74d53adff1ec7de8dfd32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277153"
---
# <a name="retrieve-azure-kinect-imu-samples"></a>Načtení ukázek Azure Kinect IMU

Zařízení Azure Kinect poskytuje přístup k jednotkám pohybu Inertial (IMUs), včetně typů akcelerometr a vybavený gyroskopem. Abyste měli přístup k ukázkám IMUs, musíte nejdřív otevřít a nakonfigurovat své zařízení a pak zachytit IMU data. Další informace najdete v tématu [vyhledání a otevření zařízení](find-then-open-device.md).

Ukázky IMU jsou vygenerovány s mnohem vyšší frekvencí než obrázky. Ukázky jsou hlášeny hostiteli s nižší sazbou, než jsou vzorky. Při čekání na ukázku IMU bude často k dispozici několik ukázek současně.

Podrobnosti o sazbě sestav IMU najdete v tématu [specifikace hardwaru](hardware-specification.md) Azure Kinect DK.

## <a name="configure-and-start-cameras"></a>Konfigurace a spuštění fotoaparátů

> [!NOTE]
> Senzory IMU můžou fungovat jenom v případě, že jsou spuštěné barvy nebo hloubkové kamery. Senzory IMU nemůžou pracovat samostatně.

Chcete-li spustit fotoaparáty, použijte [k4a_device_start_cameras ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a).

```C
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps = K4A_FRAMES_PER_SECOND_30;
config.color_format = K4A_IMAGE_FORMAT_COLOR_MJPG;
config.color_resolution = K4A_COLOR_RESOLUTION_2160P;

if (K4A_RESULT_SUCCEEDED != k4a_device_start_cameras(device, &config))
{
    printf("Failed to start cameras\n");
    goto Exit;
}

if (K4A_RESULT_SUCCEEDED != k4a_device_start_imu(device))
{
    printf("Failed to start imu\n");
    goto Exit;
}
```

## <a name="access-imu-samples"></a>Přístup k ukázkám IMU

 Každý [k4a_imu_sample_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__imu__sample__t.html#details) obsahuje akcelerometr a vybavený gyroskopem čtení zaznamenané téměř ve stejnou dobu.

Ukázky IMU můžete získat buď ve stejném vlákně, ve kterém obdržíte zachycení imagí, nebo v samostatných vláknech.

Chcete-li načíst ukázky IMU, jakmile jsou k dispozici, můžete chtít zavolat [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) na vlastní vlákno. Rozhraní API má také dostatek interních front, aby bylo možné kontrolovat pouze vzorky po vrácení každého zachycení obrázku.

Vzhledem k tomu, že existují interní IMU ukázky, můžete použít následující vzor, aniž byste museli vyřadit jakákoli data:

1. Počkejte na zachytávání, a to v libovolném kmitočtu snímků.
2. Zpracujte zachycení.
3. Načte všechny ukázky IMU ve frontě.
4. Opakujte čekání na další zachytávání.

Chcete-li načíst všechny ukázky IMU ve frontě, můžete volat [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) s `timeout_in_ms` 0 ve smyčce, dokud funkce nevrátí hodnotu `K4A_WAIT_RESULT_TIMEOUT` . `K4A_WAIT_RESULT_TIMEOUT` indikuje, že nejsou žádné ukázky zařazené do fronty a žádná nepřišla do zadaného časového limitu.

## <a name="usage-example"></a>Příklad použití

```C
k4a_imu_sample_t imu_sample;

// Capture a imu sample
switch (k4a_device_get_imu_sample(device, &imu_sample, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a imu sample\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a imu sample\n");
    goto Exit;
}

// Access the accelerometer readings
if (imu_sample != NULL)
{
    printf(" | Accelerometer temperature:%.2f x:%.4f y:%.4f z: %.4f\n",
            imu_sample.temperature,
            imu_sample.acc_sample.xyz.x,
            imu_sample.acc_sample.xyz.y,
            imu_sample.acc_sample.xyz.z);
}

```

## <a name="next-steps"></a>Další kroky

Nyní víte, jak pracovat s ukázkami IMU, můžete také
>[!div class="nextstepaction"]
>[Přístup k vstupním datům mikrofonu](access-mics.md)
