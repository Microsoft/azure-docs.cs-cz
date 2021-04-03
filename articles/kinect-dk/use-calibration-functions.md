---
title: Použití funkcí kalibrace Azure Kinect
description: Naučte se používat funkce kalibrace pro Azure Kinect DK.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, senzor, SDK, systém souřadnic, kalibrace, funkce, kamera, vnitřní, extrinsic, projekt, neprojekt, transformace, RGB-d, Point Cloud
ms.openlocfilehash: a501765222154bde4ed35e878a9d18042893fca2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277152"
---
# <a name="use-azure-kinect-calibration-functions"></a>Použití funkcí kalibrace Azure Kinect

Funkce kalibrace umožňují transformovat body mezi systémy souřadnic každého snímače na zařízení Azure Kinect. Aplikace vyžadující převod celých imagí můžou využít urychlené operace dostupné v [transformačních funkcích](use-image-transformation.md).

## <a name="retrieve-calibration-data"></a>Načíst data kalibrace

Je nutné načíst kalibraci zařízení a provést koordinaci transformací systému. Data kalibrace se ukládají do datového typu [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) . Získává se ze zařízení prostřednictvím funkce [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78). Data kalibrace se netýkají pouze jednotlivých zařízení, ale také v provozním režimu fotoaparátů. Proto [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) vyžaduje `depth_mode` `color_resolution` jako vstup parametry a.

### <a name="opencv-compatibility"></a>Kompatibilita OpenCV

Parametry kalibrace jsou kompatibilní s [OpenCV](https://opencv.org/). Další informace o jednotlivých parametrech kalibrace kamery najdete také v [dokumentaci k OpenCV](https://docs.opencv.org/3.2.0/d9/d0c/group__calib3d.html#gga7041b2a9c8f9f8ee93a2796981bc5546a204766e24f2e413e7a7c9f8b9e93f16c). Podívejte se také na [příklad kompatibility OpenCV](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility) sady SDK, který ukazuje převod mezi typem [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) a odpovídajícími datovými strukturami OpenCV.

## <a name="coordinate-transformation-functions"></a>Funkce transformace souřadnic

Následující obrázek ukazuje různé systémy souřadnic Azure Kinect a také funkce pro jejich převod. Vynecháváme trojrozměrné systémy souřadnic vybavený gyroskopem a akcelerometr, aby se obrázek snadno zachovávat.

   ![Transformace souřadnic](./media/how-to-guides/coordinate-transformation.png)

Přeznačit při deformaci objektivu: 2D souřadnice vždy odkazují na deformované image v sadě SDK. [Příklad deformace](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort) sady SDK ukazuje deformaci obrázku. Obecně platí, že prostorové body nebudou nijak ovlivněny zkreslením objektivu.

### <a name="convert-between-3d-coordinate-systems"></a>Převod mezi prostorovými systémy souřadnic

Funkce [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e) převede prostor zdrojového souřadnicového systému na 3D bod cílového systému souřadnic pomocí kalibrace extrinsic kamery. Zdroj a cíl lze nastavit na libovolný ze čtyř 3D souřadnicových systémů, tj. barev kamery, hloubkové kamery, vybavený gyroskopem nebo akcelerometr. Pokud je zdroj a cíl identické, je vstupní 3D bod bez úprav vrácen jako výstup.

### <a name="convert-between-2d-and-3d-coordinate-systems"></a>Převod mezi 2D a 3D systémy souřadnic

Funkce [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) převede prostorový bod zdrojového systému souřadnic na souřadnici 2D pixelů cílové kamery. Tato funkce je často označována jako funkce projektu. I když je možné zdroj nastavit na některý ze čtyř 3D souřadnicových systémů, musí být cílem hloubka nebo barva kamery. Pokud se zdroj a cíl liší, vstupní prostorový bod se převede na 3D souřadnicový systém cílové kamery pomocí [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e). Jakmile je prostorový bod reprezentován v cílovém systému souřadnic kamery, vypočítají se odpovídající souřadnice 2D pixelu pomocí vnitřní kalibrace cílové kamery. Pokud se prostor z viditelné oblasti cílové kamery neprojeví, je platná hodnota nastavená na 0.

Funkce [k4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) převede souřadnici 2D pixelu zdrojové kamery na 3D bod cílového systému souřadnic kamery. Zdroj musí být barevný nebo hloubkový fotoaparát. Cíl lze nastavit na libovolný ze čtyř 3D souřadnicových systémů. Kromě souřadnic 2D pixelů je hodnota hloubky pixelu (v milimetrech) v obrázku zdrojové kamery povinná jako vstup do funkce. jedním ze způsobů, jak hodnotu hloubky v geometrii barevné kamery odvodit, je použití funkce [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514). Funkce vypočítá prostor od středu od zdrojového kontaktu zdrojové kamery přes zadanou souřadnici v pixelech pomocí vnitřní kalibrace zdrojové kamery. Hodnota hloubky se pak použije k vyhledání přesného umístění prostorového bodu v tomto ray. Tato operace je často označována jako neprojektová funkce. Pokud se zdrojový a cílový fotoaparát liší, funkce transformuje 3D bod na souřadnicový systém cíle prostřednictvím [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e). Pokud souřadnice 2D pixelu nespadají do viditelné oblasti zdrojové kamery, je platná hodnota nastavená na 0.

### <a name="converting-between-2d-coordinate-systems"></a>Převod mezi 2D souřadnicovým systémem

Funkce [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) převede souřadnici 2D pixelu zdrojové kamery na souřadnici 2D pixelů cílové kamery. Zdroj a cíl musí být nastaven na barevnou nebo hloubkovou kameru. Funkce vyžaduje hodnotu hloubky pixelu (v milimetrech) ve zdrojové imagi kamery jako vstup. jedním ze způsobů, jak odvodit hodnotu hloubky v geometrii barevné kamery, je použití funkce [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514). Volá [k4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) pro převod na 3D bod zdrojového systému kamery. Pak zavolá [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) pro převod na souřadnici 2D pixelu cílové kamery. Platná hodnota je nastavena na 0, pokud [k4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880ded) nebo [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) vrátí neplatný výsledek.

## <a name="related-samples"></a>Související ukázky

- [Příklad kompatibility OpenCV](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility)
- [Příklad deformace](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort)
- [Příklad cloudu pro rychlé body](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud)

## <a name="next-steps"></a>Další kroky

Nyní víte o kalibracích kamery. můžete se také seznámit s tím, jak
>[!div class="nextstepaction"]
>[Zachycení synchronizace zařízení](capture-device-synchronization.md)

Můžete také zkontrolovat

[Systémy souřadnic](coordinate-systems.md)
