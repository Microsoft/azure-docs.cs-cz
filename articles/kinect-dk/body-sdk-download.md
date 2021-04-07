---
title: Stažení sady SDK pro sledování textu Azure Kinect
description: Seznamte se s postupem, jak stáhnout jednotlivé verze sady Kinect senzorů Azure v systému Windows nebo Linux.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/18/2021
ms.topic: conceptual
keywords: Azure, Kinect, SDK, stažení aktualizace, nejnovější, dostupná, instalace, tělo, sledování
ms.openlocfilehash: 60018df56433785f3cb723dd54cc96a8e5289b67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654488"
---
# <a name="download-azure-kinect-body-tracking-sdk"></a>Stáhnout sadu SDK pro sledování textu v Azure Kinect

Tento dokument obsahuje odkazy na instalaci jednotlivých verzí sady SDK pro sledování textu v Azure Kinect.

## <a name="azure-kinect-body-tracking-sdk-contents"></a>Obsah sady SDK pro sledování textu v Azure Kinect

- Hlavičky a knihovny pro sestavování aplikace pro sledování obsahu pomocí Azure Kinect DK
- Distribuovatelné knihovny DLL vyžadované aplikacemi pro sledování textu s využitím Azure Kinect DK
- Ukázka aplikací pro sledování těla.

## <a name="windows-download-links"></a>Odkazy ke stažení pro Windows

Verze       | Stáhnout
--------------|----------
1.1.0 | [soubor](https://www.microsoft.com/en-us/download/details.aspx?id=102901)
1.0.1 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100942) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1)
1.0.0 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100848) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0)

## <a name="linux-installation-instructions"></a>Pokyny k instalaci pro Linux

V současné době je jedinou podporovanou distribucí Ubuntu 18,04 a 20,04. Chcete-li požádat o podporu pro další distribuce, přečtěte si [tuto stránku](https://aka.ms/azurekinectfeedback).

Nejdřív budete muset nakonfigurovat [úložiště balíčků Microsoftu](https://packages.microsoft.com/), a to podle pokynů uvedených [tady](/windows-server/administration/linux-package-repository-for-microsoft-software).

`libk4abt<major>.<minor>-dev`Balíček obsahuje hlavičky a soubory cmake pro sestavení `libk4abt` .
`libk4abt<major>.<minor>`Balíček obsahuje sdílené objekty, které jsou potřebné ke spouštění spustitelných souborů, které jsou závislé na `libk4abt` nástroji, a také v prohlížeči příkladu.

Základní kurzy vyžadují `libk4abt<major>.<minor>-dev` balíček. Pokud ho chcete nainstalovat, spusťte

`sudo apt install libk4abt<major>.<minor>-dev`

Pokud příkaz uspěje, sada SDK je připravena k použití.

> [!NOTE]
> Při instalaci sady SDK si zapamatujte cestu, do které jste nainstalovali. Například "C:\Program Files\Azure Kinect text Tracking SDK 1.0.0". Ukázky, na které se odkazuje v článcích v této cestě, najdete v článku.
> Ukázky sledování těla jsou umístěné ve složce [sledování obsahu](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) v úložišti Azure-Kinect-Samples. Ukázky, na které se odkazuje v článcích, najdete tady.

## <a name="change-log"></a>Protokol změn

### <a name="v110"></a>v 1.1.0
* Zapnut Přidání podpory pro DirectML (pouze Windows) a TensorRT provádění modelu odhadu pozice. Přečtěte si nejčastější dotazy týkající se nových prostředí pro provádění.
* Zapnut Přidat `model_path` do `k4abt_tracker_configuration_t` struktury Umožňuje uživatelům zadat model odhadu cesty for pozice. Ve výchozím nastavení se použije `dnn_model_2_0_op11.onnx` standardní model odhadu pozice umístěný v aktuálním adresáři.
* Zapnut Zahrňte `dnn_model_2_0_lite_op11.onnx` model odhadu pozice Lite. Tento model obchoduje. o dvojnásobku zvýšení výkonu u ~ 5% snížení přesnosti.
* Zapnut Ověřené ukázky jsou zkompilovány pomocí sady Visual Studio 2019 a ukázky aktualizací pro použití v této verzi.
* [Zásadní změna] Aktualizujte ONNX runtime 1,6 s podporou pro PROCESORy, CUDA 11,1, DirectML (pouze Windows) a prostředí pro spouštění TensorRT 7.2.1. Vyžaduje R455 nebo lepší aktualizaci ovladačů NVIDIA.
* [Zásadní změna] Žádná instalace NuGet
* [Oprava chyby] Přidání podpory pro [odkaz](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481) GPU NVIDIA RTX 30xx Series
* [Oprava chyby] Přidání podpory pro integrované GPU technologie AMD a Intel (jenom Windows [)](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481)
* [Oprava chyby] Aktualizovat [odkaz](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1125) na CUDA 11,1
* [Oprava chyby] [Odkaz](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1248) na aktualizaci sady senzor SDK 1.4.1

### <a name="v101"></a>v 1.0.1
* [Oprava chyby] Opravte problém, že sada SDK selže při načítání onnxruntime.dll z cesty v systému Windows Build 19025 nebo novějším: [odkaz](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>v 1.0.0
* Zapnut Do instalačního programu MSI přidejte obálku C#.
* [Oprava chyby] Opravte problém, že otočení pozice nelze rozpoznat správně: [propojit](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [Oprava chyby] Opravte problém, že využití CPU na počítači se systémem Linux překračuje až 100%: [odkaz](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* Vzory Přidejte do vzorového úložiště dvě ukázky. Ukázka 1 ukazuje, jak transformovat výsledky sledování těla z prostoru hloubky na [odkaz](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample)na barevný prostor; Ukázka 2 ukazuje, jak detekovat [propojení](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample) roviny základny

## <a name="next-steps"></a>Další kroky

- [Přehled Azure Kinect DK](about-azure-kinect-dk.md)

- [Nastavení sady Azure Kinect DK](set-up-azure-kinect-dk.md)

- [Nastavení sledování textu v Azure Kinect](body-sdk-setup.md)