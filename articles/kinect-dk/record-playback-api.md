---
title: Rozhraní API pro přehrávání Azure Kinect
description: Naučte se používat sadu Kinect senzor sady Azure pro otevření souboru záznamu pomocí rozhraní API pro přehrávání.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, senzor, SDK, Hloubka, RGB, záznam, přehrávání, Matroska, MKV
ms.openlocfilehash: fe403f314c1df415537d090433f34627eb1249e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277143"
---
# <a name="the-azure-kinect-playback-api"></a>Rozhraní API pro přehrávání Azure Kinect

Sada snímač SDK poskytuje rozhraní API pro záznam dat zařízení do souboru Matroska (. MKV). Formát kontejneru Matroska ukládá videosoubory, ukázky IMU a kalibraci zařízení. Nahrávky je možné vygenerovat pomocí poskytnutého nástroje příkazového řádku [k4arecorder](record-sensor-streams-file.md) . Nahrávky je také možné přizpůsobit a zaznamenat přímo pomocí rozhraní API pro záznam.

Další informace o rozhraní API pro nahrávání najdete v tématu [`k4a_record_create()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gae14f4181e9688e710d1c80b215413831.html#gae14f4181e9688e710d1c80b215413831) .

Další informace o specifikaci formátu souboru Matroska najdete na stránce [Formát souboru nahrávky](record-file-format.md) .

## <a name="use-the-playback-api"></a>Použití rozhraní API pro přehrávání

Soubory nahrávky se dají otevřít pomocí rozhraní API pro přehrávání. Rozhraní API pro přehrávání poskytuje přístup k datům snímačů ve stejném formátu jako zbytek sady SDK pro sadu senzorů.

### <a name="open-a-record-file"></a>Otevřít soubor záznamu

V následujícím příkladu jsme otevřeli nahrávání pomocí [`k4a_playback_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gacb254ac941b2ab3c202ca68f4537f368.html#gacb254ac941b2ab3c202ca68f4537f368) , vytiskli délku záznamu a pak soubor zavřeli [`k4a_playback_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga76f415f2076f1c8c544e094a649306ff.html#ga76f415f2076f1c8c544e094a649306ff) .

```C
k4a_playback_t playback_handle = NULL;
if (k4a_playback_open("recording.mkv", &playback_handle) != K4A_RESULT_SUCCEEDED)
{
    printf("Failed to open recording\n");
    return 1;
}

uint64_t recording_length = k4a_playback_get_last_timestamp_usec(playback_handle);
printf("Recording is %lld seconds long\n", recording_length / 1000000);

k4a_playback_close(playback_handle);
```

### <a name="read-captures"></a>Čtení zachycení

Jakmile je soubor otevřený, můžeme začít číst nahrávky ze záznamu. Tento další příklad načte všechna zachycení v souboru.

```C
k4a_capture_t capture = NULL;
k4a_stream_result_t result = K4A_STREAM_RESULT_SUCCEEDED;
while (result == K4A_STREAM_RESULT_SUCCEEDED)
{
    result = k4a_playback_get_next_capture(playback_handle, &capture);
    if (result == K4A_STREAM_RESULT_SUCCEEDED)
    {
        // Process capture here
        k4a_capture_release(capture);
    }
    else if (result == K4A_STREAM_RESULT_EOF)
    {
        // End of file reached
        break;
    }
}
if (result == K4A_STREAM_RESULT_FAILED)
{
    printf("Failed to read entire recording\n");
    return 1;
}
```

### <a name="seek-within-a-recording"></a>Hledání v rámci záznamu

Až se dostanete na konec souboru, můžeme se vrátit zpátky a znovu ho přečíst. Tento proces může být proveden pomocí zpětného čtení s [`k4a_playback_get_previous_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga54732e3aa0717e1ca4eb76ee385e878c.html#ga54732e3aa0717e1ca4eb76ee385e878c) , ale může být velmi pomalý v závislosti na délce záznamu.
Místo toho můžeme použít [`k4a_playback_seek_timestamp()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaea748994a121543bd77f90417cf428f6.html#gaea748994a121543bd77f90417cf428f6) funkci k přechodu na konkrétní bod v souboru.

V tomto příkladu určíme časová razítka v mikrosekundách pro hledání různých bodů v souboru.

```C
// Seek to the beginning of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to the end of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the start
if (k4a_playback_seek_timestamp(playback_handle, 10 * 1000000, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the end
if (k4a_playback_seek_timestamp(playback_handle, -10 * 1000000, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}
```

### <a name="read-tag-information"></a>Přečíst informace o značce

Záznamy mohou také obsahovat různá metadata, například sériové číslo zařízení a verze firmwaru. Tato metadata se ukládají do značek záznamu, ke kterým se dá dostat pomocí [`k4a_playback_get_tag()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga320f966fc89b4ba0d758f787f70d5143.html#ga320f966fc89b4ba0d758f787f70d5143) funkce.

```C
// Print the serial number of the device used to record
char serial_number[256];
size_t serial_number_size = 256;
k4a_buffer_result_t buffer_result = k4a_playback_get_tag(playback_handle, "K4A_DEVICE_SERIAL_NUMBER", &serial_number, &serial_number_size);
if (buffer_result == K4A_BUFFER_RESULT_SUCCEEDED)
{
    printf("Device serial number: %s\n", serial_number);
}
else if (buffer_result == K4A_BUFFER_RESULT_TOO_SMALL)
{
    printf("Device serial number too long.\n");
}
else
{
    printf("Tag does not exist. Device serial number was not recorded.\n");
}
```

### <a name="record-tag-list"></a>Záznam seznamu značek

Níže je uveden seznam všech výchozích značek, které mohou být zahrnuty do souboru záznamu. Mnohé z těchto hodnot jsou k dispozici jako součást [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) struktury a lze je číst pomocí [`k4a_playback_get_record_configuration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaab54a85c1f1e98d170d009042b449255.html#gaab54a85c1f1e98d170d009042b449255) funkce.

Pokud značka neexistuje, předpokládá se, že má výchozí hodnotu.

| Název značky                     | Výchozí hodnota      | [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) Dílčí | Poznámky     |
|------------------------------|--------------------|--------------------------------------|----------------------------------------------------------------------------------------------------------------|
| `K4A_COLOR_MODE`             | ZAOKROUHL              | `color_format` / `color_resolution`  | Možné hodnoty: "OFF", "MJPG_1080P", "NV12_720P", "YUY2_720P" atd.                                      |
| `K4A_DEPTH_MODE`             | ZAOKROUHL              | `depth_mode` / `depth_track_enabled` | Možné hodnoty: "OFF", "NFOV_UNBINNED", "PASSIVE_IR" atd.                                                |
| `K4A_IR_MODE`                | ZAOKROUHL              | `depth_mode` / `ir_track_enabled`    | Možné hodnoty: "vypnuto", "aktivní", "pasivní"                                                                    |
| `K4A_IMU_MODE`               | ZAOKROUHL              | `imu_track_enabled`                  | Možné hodnoty: ZAPNUTo, vypnuto                                                                                   |
| `K4A_CALIBRATION_FILE`       | "calibration.json" | Není k dispozici                                  | Si [`k4a_device_get_raw_calibration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8c4e46642cee3115aeb0b33e2b43b24f.html#ga8c4e46642cee3115aeb0b33e2b43b24f) |
| `K4A_DEPTH_DELAY_NS`         | "0"                | `depth_delay_off_color_usec`         | Hodnota uložená v nanosekundách poskytuje rozhraní API mikrosekundy.                                                        |
| `K4A_WIRED_SYNC_MODE`        | SKUPIN       | `wired_sync_mode`                    | Možné hodnoty: "samostatná", "MASTER", "PODŘÍZENá"                                                         |
| `K4A_SUBORDINATE_DELAY_NS`   | "0"                | `subordinate_delay_off_master_usec`  | Hodnota uložená v nanosekundách poskytuje rozhraní API mikrosekundy.                                                        |
| `K4A_COLOR_FIRMWARE_VERSION` | ""                 | Není k dispozici                                  | Barva zařízení verze firmwaru, například 1. x. xx                                                            |
| `K4A_DEPTH_FIRMWARE_VERSION` | ""                 | Není k dispozici                                  | Hloubka verze firmwaru zařízení, například 1. x. xx                                                            |
| `K4A_DEVICE_SERIAL_NUMBER`   | ""                 | Není k dispozici                                  | Záznam sériového čísla zařízení                                                                                 |
| `K4A_START_OFFSET_NS`        | "0"                | `start_timestamp_offset_usec`        | Viz [synchronizace časového razítka](record-playback-api.md#timestamp-synchronization) níže.                       |
| `K4A_COLOR_TRACK`            | Žádné               | Není k dispozici                                  | Viz [záznam formátu souboru – identifikace stop](record-file-format.md#identifying-tracks).                     |
| `K4A_DEPTH_TRACK`            | Žádné               | Není k dispozici                                  | Viz [záznam formátu souboru – identifikace stop](record-file-format.md#identifying-tracks).                     |
| `K4A_IR_TRACK`               | Žádné               | Není k dispozici                                  | Viz [záznam formátu souboru – identifikace stop](record-file-format.md#identifying-tracks).                     |
| `K4A_IMU_TRACK`              | Žádné               | Není k dispozici                                  | Viz [záznam formátu souboru – identifikace stop](record-file-format.md#identifying-tracks).                     |

## <a name="timestamp-synchronization"></a>Synchronizace časových razítek

Formát Matroska vyžaduje, aby nahrávky měly začínat nenulovým časovým razítkem. Při [externě synchronizovaných fotoaparátech](record-external-synchronized-units.md)může být první časové razítko z každého zařízení nenulové.

Chcete-li zachovat původní časová razítka ze zařízení mezi záznamem a přehráváním, uloží soubor posun, který se má použít pro časová razítka.

`K4A_START_OFFSET_NS`Značka slouží k zadání posunu časového razítka, aby bylo možné soubory po záznamu znovu synchronizovat. Tento posun časového razítka lze přidat do každého časového razítka v souboru, chcete-li rekonstruovat původní časová razítka zařízení.

Počáteční posun je také k dispozici ve [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) struktuře.
