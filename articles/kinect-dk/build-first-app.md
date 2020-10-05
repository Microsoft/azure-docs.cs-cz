---
title: Rychlý Start – sestavení první aplikace Azure Kinect
description: Tento rychlý Start pomocí procesu vytváření nové aplikace provede uživatele Azure Kinect DK.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: Kinect, Azure, snímač, SDK, mikrofon, Access MICS, Mic data
ms.openlocfilehash: 3632145b3f3b63023e0c66e3cf99903231802edf
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277938"
---
# <a name="quickstart-build-your-first-azure-kinect-application"></a>Rychlý Start: Vytvoření první aplikace Azure Kinect

Začínáme s Azure Kinect DK? Tento rychlý Start vám pomůže začít pracovat se zařízením.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Jsou pokryté následující funkce:

- [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc)
- [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
- [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22)
- [`k4a_device_start_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
- [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
- [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)

## <a name="prerequisites"></a>Předpoklady

1. [Nastavte zařízení Azure Kinect DK](set-up-azure-kinect-dk.md).
2. [Stáhněte](sensor-sdk-download.md) a nainstalujte sadu Azure Kinect senzor SDK.

## <a name="headers"></a>Hlavičky

Je k dispozici pouze jedno záhlaví, které budete potřebovat `k4a.h` . Ujistěte se, že je váš kompilátor zvolený pomocí knihovny LIB sady SDK a složky zahrnutí. Budete také potřebovat soubory a, které jsou `k4a.lib` `k4a.dll` propojeny. Možná budete chtít odkazovat na [Přidání knihovny Azure Kinect do projektu](add-library-to-project.md).

```C
#include <k4a/k4a.h>
```

## <a name="finding-an-azure-kinect-dk-device"></a>Hledání zařízení v Azure Kinect DK

K vašemu počítači se dá připojit několik zařízení Azure Kinect DK. Nejdříve začneme tím, že zjistíte, kolik z nich je vůbec připojeno [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc) . Tato funkce by měla fungovat hned bez dalších nastavení.

```C
uint32_t count = k4a_device_get_installed_count();
```

Jakmile zjistíte, že je zařízení připojené k počítači, můžete ho otevřít pomocí [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) . Můžete zadat index zařízení, které chcete otevřít, nebo ho můžete použít jenom `K4A_DEVICE_DEFAULT` pro první z nich.

```C
// Open the first plugged in Kinect device
k4a_device_t device = NULL;
k4a_device_open(K4A_DEVICE_DEFAULT, &device);
```
Stejně jako u většiny věcí v knihovně Azure Kinect, při otevření nějakého byste ji měli také zavřít, až budete hotovi s nástrojem! Když vypínáte, nezapomeňte provést volání [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423) .

```C
k4a_device_close(device);
```

Jakmile je zařízení otevřené, můžeme provést test, abychom zajistili jeho funkčnost. Pojďme si tak přečíst sériové číslo zařízení.

```C
// Get the size of the serial number
size_t serial_size = 0;
k4a_device_get_serialnum(device, NULL, &serial_size);

// Allocate memory for the serial, then acquire it
char *serial = (char*)(malloc(serial_size));
k4a_device_get_serialnum(device, serial, &serial_size);
printf("Opened device: %s\n", serial);
free(serial);
```

## <a name="starting-the-cameras"></a>Spuštění kamer

Po otevření zařízení budete muset nastavit fotoaparát pomocí [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) objektu. Konfigurace kamery má několik různých možností. Vyberte nastavení, které nejlépe vyhovuje vašemu vlastnímu scénáři.

```C
// Configure a stream of 4096x3072 BRGA color data at 15 frames per second
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps       = K4A_FRAMES_PER_SECOND_15;
config.color_format     = K4A_IMAGE_FORMAT_COLOR_BGRA32;
config.color_resolution = K4A_COLOR_RESOLUTION_3072P;

// Start the camera with the given configuration
k4a_device_start_cameras(device, &config);

// ...Camera capture and application specific code would go here...

// Shut down the camera when finished with application logic
k4a_device_stop_cameras(device);
```

## <a name="error-handling"></a>Zpracování chyb

V zájmu zkrácení a srozumitelnosti neukazujeme zpracování chyb v některých vložených příkladech. Zpracování chyb je ale vždycky důležité! Mnoho funkcí vrátí obecný typ úspěch/selhání [`k4a_result_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga4b419a99aa2220b076a4520dc2afd1e5.html#ga4b419a99aa2220b076a4520dc2afd1e5) nebo konkrétnější variantu s podrobnými informacemi, jako je například [`k4a_wait_result_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga44c7c0c1cfba7c879e9e2da1a869e4ee.html#ga44c7c0c1cfba7c879e9e2da1a869e4ee) . Podívejte se na dokumenty nebo IntelliSense pro jednotlivé funkce, abyste viděli, jaké chybové zprávy byste měli očekávat od IT!.

Můžete použít [`K4A_SUCCEEDED`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___macros_ga8e5b48150bc243c6052793bd830c2fcd.html#ga8e5b48150bc243c6052793bd830c2fcd) [`K4A_FAILED`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___macros_ga7c2e32349135d008b6f836c571d434b4.html#ga7c2e32349135d008b6f836c571d434b4) makra a ke kontrole výsledku funkce. Takže místo pouhého otevření zařízení Azure Kinect DK můžeme toto volání funkce chránit tímto způsobem:

```C
// Open the first plugged in Kinect device
k4a_device_t device = NULL;
if ( K4A_FAILED( k4a_device_open(K4A_DEVICE_DEFAULT, &device) ) )
{
    printf("Failed to open k4a device!\n");
    return;
}
```

## <a name="full-source"></a>Úplný zdroj

```C
#pragma comment(lib, "k4a.lib")
#include <k4a/k4a.h>

#include <stdio.h>
#include <stdlib.h>

int main()
{
    uint32_t count = k4a_device_get_installed_count();
    if (count == 0)
    {
        printf("No k4a devices attached!\n");
        return 1;
    }

    // Open the first plugged in Kinect device
    k4a_device_t device = NULL;
    if (K4A_FAILED(k4a_device_open(K4A_DEVICE_DEFAULT, &device)))
    {
        printf("Failed to open k4a device!\n");
        return 1;
    }

    // Get the size of the serial number
    size_t serial_size = 0;
    k4a_device_get_serialnum(device, NULL, &serial_size);

    // Allocate memory for the serial, then acquire it
    char *serial = (char*)(malloc(serial_size));
    k4a_device_get_serialnum(device, serial, &serial_size);
    printf("Opened device: %s\n", serial);
    free(serial);

    // Configure a stream of 4096x3072 BRGA color data at 15 frames per second
    k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
    config.camera_fps       = K4A_FRAMES_PER_SECOND_15;
    config.color_format     = K4A_IMAGE_FORMAT_COLOR_BGRA32;
    config.color_resolution = K4A_COLOR_RESOLUTION_3072P;

    // Start the camera with the given configuration
    if (K4A_FAILED(k4a_device_start_cameras(device, &config)))
    {
        printf("Failed to start cameras!\n");
        k4a_device_close(device);
        return 1;
    }

    // Camera capture and application specific code would go here

    // Shut down the camera when finished with application logic
    k4a_device_stop_cameras(device);
    k4a_device_close(device);

    return 0;
}

```

## <a name="next-steps"></a>Další kroky

Naučte se najít a otevřít zařízení s Azure Kinect DK pomocí sady snímač SDK.
> [!div class="nextstepaction"]
>[Vyhledání a otevření zařízení](find-then-open-device.md)
