---
title: Přístup k datům Azure Kinect DK v těle rámce
description: Seznamte se s daty v těle textu a funkcemi pro přístup k těmto datům ve službě Azure Kinect DK.
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: how-to
keywords: tělo, rámec, Azure, Kinect, tělo, sledování, tipy
ms.openlocfilehash: be44f59cb84e99129bf526575293eee69ca64598
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277216"
---
# <a name="access-data-in-body-frame"></a>Přístup k datům ve snímku těla

Tento článek popisuje data obsažená v rámci textového rámce a funkce pro přístup k těmto datům.

Jsou pokryté následující funkce:

- [k4abt_frame_get_body_id ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92)
- [k4abt_frame_get_body_index_map ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga0e4f2d0d7e330d444de7070fb1fee4f6.html#ga0e4f2d0d7e330d444de7070fb1fee4f6)
- [k4abt_frame_get_body_skeleton ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gac7032ab06268253538556750775064fb.html#gac7032ab06268253538556750775064fb)
- [k4abt_frame_get_capture ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gad9eef11f6496bbfe997536c374217d9a.html#gad9eef11f6496bbfe997536c374217d9a)
- [k4abt_frame_get_num_bodies ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga29ab088b1a0d1a246bdb5542e21aa3c3.html#ga29ab088b1a0d1a246bdb5542e21aa3c3)
- [k4abt_frame_get_device_timestamp_usec ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga04be7b814b40296cd6b97044ed7283e4.html#ga04be7b814b40296cd6b97044ed7283e4)

## <a name="key-components-of-a-body-frame"></a>Klíčové součásti základního rámce

Každý rámec těla obsahuje kolekci struktur těla, mapu struktury 2D těla a vstupní zachycení, které tento výsledek vygenerovalo.

![Komponenty rámce textu](./media/how-to-guides/body-frame.png)

## <a name="access-the-collection-of-body-structs"></a>Přístup ke kolekci struktur těla

V jednom zachycení může být zjištěno více subjektů. Můžete zadat dotaz na počet subjektů voláním funkce [k4abt_frame_get_num_bodies ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga29ab088b1a0d1a246bdb5542e21aa3c3.html#ga29ab088b1a0d1a246bdb5542e21aa3c3) .

```C
size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
```

Pomocí funkcí [k4abt_frame_get_body_id ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92) a [k4abt_frame_get_body_skeleton ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gac7032ab06268253538556750775064fb.html#gac7032ab06268253538556750775064fb) můžete iterovat každý index těla, kde najdete ID těla a společné informace o poloze nebo orientaci.

```C
for (size_t i = 0; i < num_bodies; i++)
{
    k4abt_skeleton_t skeleton;
    k4abt_frame_get_body_skeleton(body_frame, i, &skeleton);
    uint32_t id = k4abt_frame_get_body_id(body_frame, i);
}
```

## <a name="access-the-body-index-map"></a>Přístup k mapě hlavního indexu

Pro přístup k mapě hlavního indexu použijte funkci [k4abt_frame_get_body_index_map ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga0e4f2d0d7e330d444de7070fb1fee4f6.html#ga0e4f2d0d7e330d444de7070fb1fee4f6) . Podrobné vysvětlení mapy hlavního indexu najdete v [mapě hlavního indexu](body-index-map.md) . Nezapomeňte uvolnit mapu hlavního indexu, pokud už není potřeba.

```C
k4a_image_t body_index_map = k4abt_frame_get_body_index_map(body_frame);
...  // Do your work with the body index map
k4a_image_release(body_index_map);
```

## <a name="access-the-input-capture"></a>Přístup ke vstupnímu zachycení

Sledování textu je asynchronní rozhraní API. Původní zachytávání již mohl být vydaným časem odebrání výsledku. Pomocí funkce [k4abt_frame_get_capture ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gad9eef11f6496bbfe997536c374217d9a.html#gad9eef11f6496bbfe997536c374217d9a) můžete zadat dotaz na vstupní zachycení použité ke generování tohoto výsledku sledování textu. Počet odkazů pro k4a_capture_t se zvýší pokaždé, když je tato funkce volána. Funkci [k4a_capture_release ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6) použijte, pokud již není zachycení nutné.

```C
k4a_capture_t input_capture = k4abt_frame_get_capture(body_frame);
... // Do your work with the input capture
k4a_capture_release(input_capture);
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Sada Azure Kinect Body Tracking SDK](https://microsoft.github.io/Azure-Kinect-Body-Tracking/)
