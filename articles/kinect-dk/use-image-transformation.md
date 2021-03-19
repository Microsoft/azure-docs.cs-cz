---
title: Použití transformací imagí sady Kinect pro senzory Azure
description: Naučte se používat funkce transformace image sady Kinect pro senzory Azure.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.topic: conceptual
ms.date: 06/26/2019
keywords: Kinect, Azure, senzor, sada SDK, systém souřadnic, kalibrace, projekt, neprojekt, transformace, RGB (Point-d) – Point Cloud
ms.openlocfilehash: df7f2aa13c0e9c0241494e96e720b30f3ff1d8a5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277140"
---
# <a name="use-azure-kinect-sensor-sdk-image-transformations"></a>Použití transformací imagí sady Kinect pro senzory Azure

Pokud chcete používat a transformovat image mezi koordinovanými kamerami v Azure Kinect DK, postupujte podle konkrétních funkcí.

## <a name="k4a_transformation-functions"></a>funkce k4a_transformation

 Všechny funkce s předponou *k4a_transformation* fungují na celých obrázcích. Vyžadují popisovač transformace [k4a_transformation_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__transformation__t.html) získat prostřednictvím [k4a_transformation_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10) a jsou nepřiděleny prostřednictvím [k4a_transformation_destroy ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7d3ecaae66f26c1a89da9042b1bc6d44.html#ga7d3ecaae66f26c1a89da9042b1bc6d44). Můžete se také podívat na [příklad transformace](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation) sady SDK, který ukazuje, jak používat tři funkce v tomto tématu.

Jsou pokryté následující funkce:

* [`k4a_transformation_depth_image_to_color_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)
* [`k4a_transformation_depth_image_to_color_camera_custom()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb)
* [`k4a_transformation_color_image_to_depth_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29)
* [`k4a_transformation_depth_image_to_point_cloud()`](
https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70)

### <a name="k4a_transformation_depth_image_to_color_camera"></a>k4a_transformation_depth_image_to_color_camera

#### <a name="overview"></a>Přehled

 Funkce [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) transformuje mapu hloubky z pohledu hloubkové kamery do pohledu barevné kamery. Tato funkce je navržena tak, aby se vytvořila jako image RGB-D, kde D představuje další kanál obrázku, který zaznamenává hodnotu hloubky. Jak je vidět na následujícím obrázku, barva obrázku a výstup [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) vypadají, jako kdyby byly pořízeny ze stejného hlediska, tedy z pohledu barevné kamery.

   ![Transformace obrázku](./media/how-to-guides/image-transformation.png)

#### <a name="implementation"></a>Implementace

 Tato transformační funkce je složitější než jednoduché volání [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) pro každý pixel. Pokřiví trojúhelníkové sítě od geometrie kamery hloubky až po geometrii barevné kamery. Síť trojúhelníku se používá k tomu, aby se předešlo generování děr v transformované imagi hloubky. Vyrovnávací paměť Z-buffer zajišťuje správné zpracování Occlusions. Ve výchozím nastavení je pro tuto funkci povolená akcelerace GPU.

#### <a name="parameters"></a>Parametry

 Vstupní parametry jsou popisovačem transformace a obrázkem hloubky. Rozlišení obrázku hloubky se musí shodovat s ```depth_mode``` určením při vytváření popisovače transformace. Například pokud byl popisovač transformace vytvořen pomocí ```K4A_DEPTH_MODE_WFOV_UNBINNED``` režimu 1024x1024, musí být rozlišení obrázku hloubky 1024x1024 pixelů. Výstupem je transformovaný obrázek hloubky, který musí být přidělen uživatelem prostřednictvím volání [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). Rozlišení obrázku transformované hloubky musí odpovídat ```color_resolution``` zadané při vytváření popisovače transformace. Pokud se například barevné rozlišení nastavilo `K4A_COLOR_RESOLUTION_1080P` , musí být výstupní rozlišení obrazu 1080 pixelů. Rozteč výstupní Image je nastavená na `width * sizeof(uint16_t)` , protože image ukládá 16bitové hodnoty hloubky.

### <a name="k4a_transformation_depth_image_to_color_camera_custom"></a>k4a_transformation_depth_image_to_color_camera_custom

#### <a name="overview"></a>Přehled

 Funkce [k4a_transformation_depth_image_to_color_camera_custom ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb) transformuje mapu hloubky a vlastní obrázek z pohledu hloubkové kamery do pohledu barevné kamery. V rámci rozšíření [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)je tato funkce navržena tak, aby vytvořila odpovídající vlastní obrázek, pro který každý pixel odpovídá odpovídajícím souřadnicím obrazové kamery v obrázku s transformované hloubkou barev.

#### <a name="implementation"></a>Implementace

 Tato transformační funkce vytvoří obrázek transformované hloubky stejným způsobem jako [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514). K transformaci vlastní image Tato funkce poskytuje možnosti použití lineární interpolace nebo blížící se nejbližší sousední interpolace. Použití lineární interpolace by mohlo vytvořit nové hodnoty v transformované vlastní imagi. Výsledkem interpolace nejbližšího souseda bude zabránit tomu, aby hodnoty, které nejsou přítomny v původním obrázku, byly použity ve výstupní imagi, ale budou výsledkem méně hladkého obrázku. Vlastní image by měla být v jednom kanálu 8bitové nebo 16bitové. Ve výchozím nastavení je pro tuto funkci povolená akcelerace GPU.

#### <a name="parameters"></a>Parametry

 Vstupní parametry jsou transformační popisovač, obrázek hloubky, vlastní obrázek a typ interpolace. Obrázek hloubky a vlastní rozlišení obrázku se musí shodovat s `depth_mode` určením při vytváření popisovače transformace. Například pokud byl popisovač transformace vytvořen pomocí `K4A_DEPTH_MODE_WFOV_UNBINNED` režimu 1024x1024, musí být rozlišení obrázku hloubky a vlastní image 1024x1024 pixelů. `interpolation_type`Měla by být buď `K4A_TRANSFORMATION_INTERPOLATION_TYPE_LINEAR` nebo `K4A_TRANSFORMATION_INTERPOLATION_TYPE_NEAREST` . Výstupem je transformovaný obrázek hloubky a transformovaný vlastní obrázek, který musí uživatel přidělit prostřednictvím volání [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). Rozlišení transformované hloubkové image a transformované vlastní image se musí shodovat s `color_resolution` určením při vytváření popisovače transformace. Pokud se například barevné rozlišení nastavilo `K4A_COLOR_RESOLUTION_1080P` , musí být výstupní rozlišení obrazu 1080 pixelů. Rozteč výstupní hloubky obrázku je nastavená na `width * sizeof(uint16_t)` , protože obrázek ukládá 16bitové hodnoty hloubky. Vstupní vlastní image a transformovaná vlastní image musí být ve formátu `K4A_IMAGE_FORMAT_CUSTOM8` nebo `K4A_IMAGE_FORMAT_CUSTOM16` odpovídajícím způsobem musí být nastavena odpovídající rozteč obrázku. 

### <a name="k4a_transformation_color_image_to_depth_camera"></a>k4a_transformation_color_image_to_depth_camera

#### <a name="overview"></a>Přehled

 Funkce [k4a_transformation_color_image_to_depth_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29) transformuje obrázek barvy z pohledu barvy kamery do pohledu hloubky (viz obrázek výše). Dá se použít ke generování imagí RGB-D.

#### <a name="implementation"></a>Implementace

 Pro každý pixel mapy hloubky funkce používá hodnotu hloubky v pixelech k výpočtu odpovídající souřadnice podpixelu v obrázku barvy. Pak vyhledáme hodnotu barvy v této souřadnici v obrázku barvy. V barevném obrázku je provedena interpolace varianty k získání hodnoty barvy při přesnosti v pixelech. Pixel, který nemá přidruženou hloubku čtení, je přiřazený k hodnotě BGRA `[0,0,0,0]` ve výstupní imagi. Ve výchozím nastavení je pro tuto funkci povolená akcelerace GPU. Vzhledem k tomu, že tato metoda vytváří díry v transformované barvě obrazu a nezpracovává Occlusions, doporučujeme místo toho použít funkci [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) .

#### <a name="parameters"></a>Parametry

Vstupní parametry jsou transformační popisovač, obrázek hloubky a barevný obrázek. Rozlišení hloubkové a barevné image se musí shodovat s depth_mode a color_resolution určena při vytváření popisovače transformace. Výstupem je transformovaný barevný obrázek, který musí být přidělen uživatelem prostřednictvím volání [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). Rozlišení transformované barvy obrázku se musí shodovat s depth_resolution zadanou při vytváření popisovače transformace. Výstupní obrázek uchovává hodnoty 4 8, které představují BGRA pro každý pixel. Proto je rozteč obrázku ```width * 4 * sizeof(uint8_t)``` . Pořadí dat je prokládaných pixelů, tj. modrou hodnotou pixelů 0, zelená hodnota – pixel 0, červená hodnota – pixel 0, hodnota alfa – pixel 0, modrá hodnota – pixel 1 atd.

### <a name="k4a_transformation_depth_image_to_point_cloud"></a>k4a_transformation_depth_image_to_point_cloud

#### <a name="overview"></a>Přehled

Funkce [k4a_transformation_depth_image_to_point_cloud ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70) převede 2D mapu hloubky pořízenou fotoaparátem do cloudu 3D bodu v souřadnicovém systému stejné kamery. Fotoaparát může mít tedy hloubkovou nebo barevnou kameru.

#### <a name="implementation"></a>Implementace

 Tato funkce poskytuje ekvivalentní výsledky pro spuštění [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) pro každý pixel, ale výpočetní výkon. Při volání [k4a_transformation_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10)je předem vypočítána tabulka vyhledávacího vyhledávání s názvem, která obsahuje faktory x a y pro každý obrazový obraz. Když zavoláte [k4a_transformation_depth_image_to_point_cloud ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70), získáme 3D souřadnici v pixelech vynásobením faktoru x měřítka pixelu souřadnicí Z osy z. Obdobně se souřadnice 3D Y vypočítávají pomocí násobení pomocí faktoru měřítka y. [Příklad cloudu s rychlými body](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud) sady SDK ukazuje, jak se počítá tabulka XY. Uživatelé mohou postupovat podle příkladu kódu k implementaci vlastní verze této funkce, například k urychlení kanálu GPU.

#### <a name="parameters"></a>Parametry

 Vstupní parametry jsou transformační popisovač, specifikátor kamery a obrázek hloubky. Pokud je specifikátor kamery nastaven na hloubku, rozlišení obrázku hloubky musí odpovídat depth_mode určenému při vytváření popisovače transformace. V opačném případě, pokud je specifikátor nastaven na barevnou kameru, rozlišení se musí shodovat s rozlišením zvoleného color_resolution. Výstupní parametr je image XYZ, kterou musí uživatel přidělit prostřednictvím volání [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). Rozlišení obrázku XYZ musí odpovídat rozlišení mapy hloubky vstupu. Ukládáme tři podepsané 16bitové hodnoty souřadnic v milimetru pro každý pixel. Rozteč obrázku XYZ je proto nastavená na `width * 3 * sizeof(int16_t)` . Pořadí dat je prokládaných pixelů, tj. souřadnice X – pixelů 0, Y-souřadnic – pixel 0, Z-souřadnice – pixel 0, X-ová souřadnice – pixel 1 atd. Pokud pixel nejde převést na 3D, funkce přiřadí hodnoty `[0,0,0]` k obrazovému bodu.

## <a name="samples"></a>ukázky

[Příklad transformace](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation)

## <a name="next-steps"></a>Další kroky

Nyní víte, jak používat funkce pro transformaci imagí sady Azure Kinect snímače, můžete také získat informace o

>[!div class="nextstepaction"]
>[Funkce kalibrace sady Azure Kinect snímače](use-calibration-functions.md)

Můžete také zkontrolovat

[Systémy souřadnic](coordinate-systems.md)
