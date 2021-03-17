---
title: Načtení dat imagí Kinect Azure
description: Naučte se, jak načíst data imagí Azure Kinect pomocí sady Kinect snímač SDK.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, načtení, senzor, kamera, sada SDK, Hloubka, RGB, obrázky, barva, zachycení, rozlišení, vyrovnávací paměť
ms.openlocfilehash: fed5c7340d287d9103ba35f0fd3d80c0fff6e3ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87538910"
---
# <a name="retrieve-azure-kinect-image-data"></a>Načtení dat imagí Kinect Azure

Tato stránka poskytuje podrobné informace o tom, jak načíst image z Azure Kinect. Tento článek ukazuje, jak zachytit a přistupovat k obrázkům koordinovaným mezi barvou a hloubkou zařízení. Abyste měli přístup k imagí, musíte nejdřív otevřít a nakonfigurovat zařízení a pak můžete zachytit image.
Předtím, než nakonfigurujete a zachytíte image, je nutné [Najít a otevřít zařízení](find-then-open-device.md).

Můžete se také podívat na [příklad streamování SDK](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/streaming) , který ukazuje, jak používat funkce v tomto článku.

Jsou pokryté následující funkce:

- [`k4a_device_start_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
- [`k4a_device_get_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4dac757a33657f4d3dbf1ae8b21c158a.html#ga4dac757a33657f4d3dbf1ae8b21c158a)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_image_get_buffer()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e)
- [`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f)
- [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6)
- [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
  
## <a name="configure-and-start-the-device"></a>Konfigurace a spuštění zařízení

Dva kamery dostupné v zařízení Kinect podporují více režimů, rozlišení a formáty výstupu. Úplný seznam najdete v tématu [specifikace hardwaru](hardware-specification.md)pro Azure Kinect Development Kit.

Konfigurace streamování se nastavuje pomocí hodnot ve [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) struktuře.

```C
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps = K4A_FRAMES_PER_SECOND_30;
config.color_format = K4A_IMAGE_FORMAT_COLOR_MJPG;
config.color_resolution = K4A_COLOR_RESOLUTION_2160P;
config.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;

if (K4A_RESULT_SUCCEEDED != k4a_device_start_cameras(device, &config))
{
    printf("Failed to start device\n");
    goto Exit;
}
```

Jakmile budou kamery spuštěné, budou data dál zaznamenávat do chvíle, kdy [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8) se zavolají nebo se zařízení zavře.

## <a name="stabilization"></a>Stabilizaci

Při spouštění zařízení pomocí funkce synchronizace více zařízení se důrazně doporučuje použít pevné nastavení expozice.
Když máte nastavenou ruční expozici, může trvat až osm zachycení ze zařízení před ustálením imagí a snímkem snímků. Díky automatické expozici může trvat až 20 zachytávání, než se dokončí stabilizace snímků a snímkových snímků.

## <a name="get-a-capture-from-the-device"></a>Získání zachycení ze zařízení

Obrázky jsou zachyceny ze zařízení v korelačním režimu. Každá zachycená image obsahuje hloubkový obrázek, obrázek IR, obrázek barvy nebo kombinaci obrázků.

Ve výchozím nastavení rozhraní API vrátí pouze zachycení, jakmile obdrží všechny požadované obrázky pro režim streamování. Můžete nakonfigurovat rozhraní API tak, aby vracelo částečné zachycení s pouze hloubkovou a barevnou imagí, jakmile budou k dispozici vymazáním [`synchronized_images_only`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t_a8208974f05d89fc1362c6a0900bdef4d.html#a8208974f05d89fc1362c6a0900bdef4d) parametru [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) .

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

Jakmile rozhraní API úspěšně vrátí zachycení, je nutné zavolat [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6) po dokončení pomocí objektu Capture.

## <a name="get-an-image-from-the-capture"></a>Získat obrázek ze zachycení

Chcete-li načíst zachycenou bitovou kopii, zavolejte příslušnou funkci pro každý typ obrázku. Jedna z těchto:

- [`k4a_capture_get_color_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga683e440b5f22215a2de58d7fa140488c.html#ga683e440b5f22215a2de58d7fa140488c)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_capture_get_ir_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga1531c3fa76a7c174b8f2eab24de91794.html#ga1531c3fa76a7c174b8f2eab24de91794)

[`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f) [`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html) Až budete s použitím image hotovi, musíte zavolat na jakýkoli popisovač vrácený těmito funkcemi.

## <a name="access-image-buffers"></a>Přístup k vyrovnávací paměti imagí

[`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html) má mnoho funkcí přistupujícího objektu pro získání vlastností obrázku.

Pro přístup k vyrovnávací paměti obrázku použijte [k4a_image_get_buffer](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e).

Následující příklad ukazuje, jak získat přístup k zachycené imagi hloubky. Stejný princip platí i pro ostatní typy obrázků. Nezapomeňte však nahradit proměnnou typu obrázku správným typem obrázku, jako je například IR nebo Color.

```C
// Access the depth16 image
k4a_image_t image = k4a_capture_get_depth_image(capture);
if (image != NULL)
{
    printf(" | Depth16 res:%4dx%4d stride:%5d\n",
            k4a_image_get_height_pixels(image),
            k4a_image_get_width_pixels(image),
            k4a_image_get_stride_bytes(image));

    // Release the image
    k4a_image_release(image);
}

// Release the capture
k4a_capture_release(capture);
```

## <a name="next-steps"></a>Další kroky

Nyní víte, jak zachytit a koordinovat image kamer mezi barevou a hloubkou pomocí zařízení Azure Kinect. Můžete také:

>[!div class="nextstepaction"]
>[Načtení ukázek IMU](retrieve-imu-samples.md)

>[!div class="nextstepaction"]
>[Přístup k mikrotelefonům](access-mics.md)
