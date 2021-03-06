---
title: Vyhledejte a otevřete zařízení Azure Kinect
description: Naučte se najít a otevřít zařízení Azure Kinect pomocí sady Azure Kinect Senor SDK.
author: cedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, senzor, SDK, Hloubka, RGB, zařízení, najít, otevřít
ms.openlocfilehash: 67fc93b924d5d663bb43098969c54d1975bd5895
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277145"
---
# <a name="find-then-open-the-azure-kinect-device"></a>Vyhledejte a otevřete zařízení Azure Kinect

Tento článek popisuje, jak můžete najít a pak otevřít Azure Kinect DK. V tomto článku se dozvíte, jak zpracovat případ, kdy se k počítači připojuje více zařízení.

Můžete se také podívat na [příklad výčtu sady SDK](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/enumerate) , který ukazuje, jak používat funkce v tomto článku.

Jsou pokryté následující funkce:
 * [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc)
 * [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
 * [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22)
 * [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)

## <a name="discover-the-number-of-connected-devices"></a>Zjištění počtu připojených zařízení

Nejprve získejte počet aktuálně připojených zařízení Azure Kinect pomocí [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc) .

```C
uint32_t device_count = k4a_device_get_installed_count();

printf("Found %d connected devices:\n", device_count);
```

## <a name="open-a-device"></a>Otevření zařízení

Chcete-li získat informace o zařízení nebo číst data z něj, je nutné nejprve na zařízení otevřít popisovač pomocí [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) .

```C
k4a_device_t device = NULL;

for (uint8_t deviceIndex = 0; deviceIndex < device_count; deviceIndex++)
{
    if (K4A_RESULT_SUCCEEDED != k4a_device_open(deviceIndex, &device))
    {
        printf("%d: Failed to open device\n", deviceIndex);
        continue;
    }

    ...

    k4a_device_close(device);
}
```

`index`Parametr určuje, [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) které zařízení se má otevřít, pokud je k dispozici více než jedna připojená. Pokud očekáváte, že bude připojeno pouze jedno zařízení, můžete předat argument `K4A_DEVICE_DEFAULT` nebo 0, který označuje první zařízení.

Kdykoli otevřete zařízení, které potřebujete zavolat, až [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423) budete s použitím tohoto popisovače hotovi. Žádné jiné popisovače nelze otevřít na stejné zařízení, dokud neuzavřete popisovač.

## <a name="identify-a-specific-device"></a>Identifikace konkrétního zařízení

Pořadí vyčíslení zařízení podle indexu se nezmění, dokud nebudou zařízení připojena nebo odpojena. K identifikaci fyzického zařízení byste měli použít sériové číslo zařízení.

Pokud chcete načíst sériové číslo ze zařízení, použijte [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22) funkci po otevření popisovače.

Tento příklad ukazuje, jak přidělit správné množství paměti pro uložení sériového čísla.

```C
char *serial_number = NULL;
size_t serial_number_length = 0;

if (K4A_BUFFER_RESULT_TOO_SMALL != k4a_device_get_serialnum(device, NULL, &serial_number_length))
{
    printf("%d: Failed to get serial number length\n", deviceIndex);
    k4a_device_close(device);
    device = NULL;
    continue;
}

serial_number = malloc(serial_number_length);
if (serial_number == NULL)
{
    printf("%d: Failed to allocate memory for serial number (%zu bytes)\n", deviceIndex, serial_number_length);
    k4a_device_close(device);
    device = NULL;
    continue;
}

if (K4A_BUFFER_RESULT_SUCCEEDED != k4a_device_get_serialnum(device, serial_number, &serial_number_length))
{
    printf("%d: Failed to get serial number\n", deviceIndex);
    free(serial_number);
    serial_number = NULL;
    k4a_device_close(device);
    device = NULL;
    continue;
}

printf("%d: Device \"%s\"\n", deviceIndex, serial_number);
```

## <a name="open-the-default-device"></a>Otevřete výchozí zařízení.

Ve většině aplikací se ke stejnému počítači připojí jenom jedna Azure Kinect DK. Pokud se potřebujete připojit pouze k jednomu očekávanému zařízení, můžete volat pomocí nástroje [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) `index` `K4A_DEVICE_DEFAULT` a otevřít první zařízení.

```C
k4a_device_t device = NULL;
uint32_t device_count = k4a_device_get_installed_count();

if (device_count != 1)
{
    printf("Unexpected number of devices found (%d)\n", device_count);
    goto Exit;
}

if (K4A_RESULT_SUCCEEDED != k4a_device_open(K4A_DEVICE_DEFAULT, &device))
{
    printf("Failed to open device\n");
    goto Exit;
}
```

## <a name="next-steps"></a>Další kroky

>[!div class="nextstepaction"]
>[Načtení imagí](retrieve-images.md)

>[!div class="nextstepaction"]
>[Načíst ukázky IMU](retrieve-imu-samples.md)

