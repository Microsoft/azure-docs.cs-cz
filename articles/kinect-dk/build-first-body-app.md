---
title: 'Rychlý Start: Vytvoření aplikace pro sledování textu v Azure Kinect'
description: Podrobné pokyny k vytvoření první aplikace pro sledování textu v Azure Kinect
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: Kinect, Azure, snímač, SDK, tělo, sledování, společné, aplikace, nejdřív
ms.openlocfilehash: bdf8ee7a14bf59a151dfa316b11159830b4f63b8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277944"
---
# <a name="quickstart-build-an-azure-kinect-body-tracking-application"></a>Rychlý Start: Vytvoření aplikace pro sledování textu v Azure Kinect

Začínáme se sadou SDK pro sledování textu? Tento rychlý Start vám pomůže začít pracovat se sledováním textu! V tomto [úložišti Azure-Kinect-Sample](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples)najdete další příklady.

## <a name="prerequisites"></a>Předpoklady

- [Nastavení sady Azure Kinect DK](set-up-azure-kinect-dk.md)
- [Nastavení sady Body Tracking SDK](body-sdk-setup.md)
- Projděte si návod, jak [vytvořit první Azure Kinect Application](build-first-app.md) rychlý Start.
- Seznamte se s následujícími funkcemi sady senzor SDK:
  - [k4a_device_open ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
  - [k4a_device_start_cameras ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
  - [k4a_device_stop_cameras ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
  - [k4a_device_close ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)
- Přečtěte si dokumentaci o následujících funkcích sady SDK pro sledování textu:
  - [k4abt_tracker_create ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1aa71481b8441def94de11b29e0e3cbc.html#ga1aa71481b8441def94de11b29e0e3cbc)
  - [k4abt_tracker_enqueue_capture ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga093becd9bb4a63f5f4d56f58097a7b1e.html#ga093becd9bb4a63f5f4d56f58097a7b1e)
  - [k4abt_tracker_pop_result ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gaaf446fb1579cbbe0b6af824ee0a7458b.html#gaaf446fb1579cbbe0b6af824ee0a7458b)
  - [k4abt_tracker_shutdown ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga94036969ef94cbc414c78b3f6d04bfa5.html#ga94036969ef94cbc414c78b3f6d04bfa5)
  - [k4abt_tracker_destroy ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gab2c9afca092130976cd66077c0557ed1.html#gab2c9afca092130976cd66077c0557ed1)

## <a name="headers"></a>Hlavičky

Sledování textu používá jednu hlavičku `k4abt.h` . Zahrnout tuto hlavičku kromě `k4a.h` . Ujistěte se, že je váš kompilátor zvolený pro sadu SDK senzoru i pro sadu SDK a složky pro sledování textu `lib` `include` . Je také potřeba propojit `k4a.lib` `k4abt.lib` soubory a. Spuštění aplikace vyžaduje `k4a.dll` ,, `k4abt.dll` `onnxruntime.dll` a `dnn_model.onnx` v cestě spuštění aplikací.

```C
#include <k4a/k4a.h>
#include <k4abt.h>
```

## <a name="open-device-and-start-the-camera"></a>Otevřete zařízení a spusťte fotoaparát.

Vaše první aplikace pro sledování textu předpokládá jedno zařízení Azure Kinect připojené k počítači.

Sestavení sledování textu v sadě senzor SDK. Pokud chcete použít sledování textu, musíte nejdřív otevřít a nakonfigurovat zařízení. Pomocí funkce [k4a_device_open ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) otevřete zařízení a nakonfigurujte ho pomocí objektu [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) . Pro dosažení nejlepších výsledků nastavte režim hloubky na `K4A_DEPTH_MODE_NFOV_UNBINNED` nebo `K4A_DEPTH_MODE_WFOV_2X2BINNED` . Sledování textu se nespustí, pokud je režim hloubky nastavený na  `K4A_DEPTH_MODE_OFF` nebo `K4A_DEPTH_MODE_PASSIVE_IR` .

Další informace o hledání a otevírání zařízení najdete na [této stránce](find-then-open-device.md).

Další informace o režimech Kinect hloubky Azure najdete na těchto stránkách: [specifikace hardwaru](hardware-specification.md) a [k4a_depth_mode_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga3507ee60c1ffe1909096e2080dd2a05d.html#ga3507ee60c1ffe1909096e2080dd2a05d) výčty.

```C
k4a_device_t device = NULL;
k4a_device_open(0, &device);

// Start camera. Make sure depth camera is enabled.
k4a_device_configuration_t deviceConfig = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
deviceConfig.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;
deviceConfig.color_resolution = K4A_COLOR_RESOLUTION_OFF;
k4a_device_start_cameras(device, &deviceConfig);
```

## <a name="create-the-tracker"></a>Vytvořit sledovací modul

Prvním krokem při získávání výsledků sledování těla je vytvoření sledovacího sledování textu. Vyžaduje [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html) strukturu kalibrace senzorů. K kalibraci senzorů se dá dotázat pomocí funkce [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) .

```C
k4a_calibration_t sensor_calibration;
k4a_device_get_calibration(device, deviceConfig.depth_mode, deviceConfig.color_resolution, &sensor_calibration);

k4abt_tracker_t tracker = NULL;
k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker);
```

## <a name="get-captures-from-the-azure-kinect-device"></a>Získat zachycení ze zařízení Azure Kinect

Další informace o načítání dat obrázku najdete na [této stránce](retrieve-images.md).

```C
// Capture a depth frame
k4a_device_get_capture(device, &capture, TIMEOUT_IN_MS);
```

## <a name="enqueue-the-capture-and-pop-the-results"></a>Zařadit do fronty zachycené a automaticky otevírané výsledky

Modul pro sledování interně udržuje vstupní frontu a výstupní frontu pro asynchronní zpracování Azure Kinect v DK efektivněji zachytit. Dalším krokem je použití `k4abt_tracker_enqueue_capture()` funkce k přidání nového zachycení do vstupní fronty. Pomocí `k4abt_tracker_pop_result()` funkce můžete z výstupní fronty zobrazit výsledek. Hodnota časového limitu závisí na aplikaci a řídí dobu čekání ve frontě.

Vaše první aplikace pro sledování textu používá vzor zpracování v reálném čase. Podrobné vysvětlení dalších vzorů najdete v tématu [získání výsledků sledování textu](get-body-tracking-results.md) .

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
{
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
{
    // Successfully popped the body tracking result. Start your processing
    ...

    k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
}
```

## <a name="access-the-body-tracking-result-data"></a>Přístup k datům výsledků sledování textu

Výsledky sledování textu pro každé zachycení snímače jsou uloženy v těle [k4abt_frame_t](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/structk4abt__frame__t.html) struktury. Každý rámec těla obsahuje tři klíčové komponenty: kolekci struktur těla, mapu 2D těla a vstupní zachycení.

Vaše první aplikace pro sledování textu přistupuje jenom k počtu zjištěných subjektů. Podrobnější vysvětlení dat v těle najdete v tématu věnovaném [přístupu k datům v těle rámce](access-data-body-frame.md) .

```C
size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
printf("%zu bodies are detected!\n", num_bodies);
```

## <a name="clean-up"></a>Vyčištění

Posledním krokem je vypnutí sledování textu a uvolnění objektu sledování těla. Musíte také zařízení zastavit a zavřít.

```C
k4abt_tracker_shutdown(tracker);
k4abt_tracker_destroy(tracker);
k4a_device_stop_cameras(device);
k4a_device_close(device);
```

## <a name="full-source"></a>Úplný zdroj

```C
#include <stdio.h>
#include <stdlib.h>

#include <k4a/k4a.h>
#include <k4abt.h>

#define VERIFY(result, error)                                                                            \
    if(result != K4A_RESULT_SUCCEEDED)                                                                   \
    {                                                                                                    \
        printf("%s \n - (File: %s, Function: %s, Line: %d)\n", error, __FILE__, __FUNCTION__, __LINE__); \
        exit(1);                                                                                         \
    }                                                                                                    \

int main()
{
    k4a_device_t device = NULL;
    VERIFY(k4a_device_open(0, &device), "Open K4A Device failed!");

    // Start camera. Make sure depth camera is enabled.
    k4a_device_configuration_t deviceConfig = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
    deviceConfig.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;
    deviceConfig.color_resolution = K4A_COLOR_RESOLUTION_OFF;
    VERIFY(k4a_device_start_cameras(device, &deviceConfig), "Start K4A cameras failed!");

    k4a_calibration_t sensor_calibration;
    VERIFY(k4a_device_get_calibration(device, deviceConfig.depth_mode, deviceConfig.color_resolution, &sensor_calibration),
        "Get depth camera calibration failed!");

    k4abt_tracker_t tracker = NULL;
    k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
    VERIFY(k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker), "Body tracker initialization failed!");

    int frame_count = 0;
    do
    {
        k4a_capture_t sensor_capture;
        k4a_wait_result_t get_capture_result = k4a_device_get_capture(device, &sensor_capture, K4A_WAIT_INFINITE);
        if (get_capture_result == K4A_WAIT_RESULT_SUCCEEDED)
        {
            frame_count++;
            k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
            k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
            if (queue_capture_result == K4A_WAIT_RESULT_TIMEOUT)
            {
                // It should never hit timeout when K4A_WAIT_INFINITE is set.
                printf("Error! Add capture to tracker process queue timeout!\n");
                break;
            }
            else if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
            {
                printf("Error! Add capture to tracker process queue failed!\n");
                break;
            }

            k4abt_frame_t body_frame = NULL;
            k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
            if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
            {
                // Successfully popped the body tracking result. Start your processing

                size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
                printf("%zu bodies are detected!\n", num_bodies);

                k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
            }
            else if (pop_frame_result == K4A_WAIT_RESULT_TIMEOUT)
            {
                //  It should never hit timeout when K4A_WAIT_INFINITE is set.
                printf("Error! Pop body frame result timeout!\n");
                break;
            }
            else
            {
                printf("Pop body frame result failed!\n");
                break;
            }
        }
        else if (get_capture_result == K4A_WAIT_RESULT_TIMEOUT)
        {
            // It should never hit time out when K4A_WAIT_INFINITE is set.
            printf("Error! Get depth frame time out!\n");
            break;
        }
        else
        {
            printf("Get depth capture returned error: %d\n", get_capture_result);
            break;
        }

    } while (frame_count < 100);

    printf("Finished body tracking processing!\n");

    k4abt_tracker_shutdown(tracker);
    k4abt_tracker_destroy(tracker);
    k4a_device_stop_cameras(device);
    k4a_device_close(device);

    return 0;
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Získání výsledků sledování pohybu těla](get-body-tracking-results.md)
