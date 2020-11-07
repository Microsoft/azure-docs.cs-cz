---
title: Stažení sady SDK pro sledování textu Azure Kinect
description: Seznamte se s postupem, jak stáhnout jednotlivé verze sady Kinect senzorů Azure v systému Windows nebo Linux.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, SDK, stažení aktualizace, nejnovější, dostupná, instalace, tělo, sledování
ms.openlocfilehash: 0ac0598d893617f341b9e1fd4d45c0c3e3f3c619
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359591"
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
1.0.1 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100942) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1)
1.0.0 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100848) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0)
0.9.5 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100636) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.5)
0.9.4 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100415) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.4)
0.9.3 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100307) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.3)
0.9.2 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100128) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.2)
0.9.1 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100063) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.1)
0.9.0 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=58402) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.0)

## <a name="linux-installation-instructions"></a>Pokyny k instalaci pro Linux

V současné době je jedinou podporovanou distribucí Ubuntu 18,04. Chcete-li požádat o podporu pro další distribuce, přečtěte si [tuto stránku](https://aka.ms/azurekinectfeedback).

Nejdřív budete muset nakonfigurovat [úložiště balíčků Microsoftu](https://packages.microsoft.com/), a to podle pokynů uvedených [tady](/windows-server/administration/linux-package-repository-for-microsoft-software).

`libk4abt<major>.<minor>-dev`Balíček obsahuje hlavičky a soubory cmake pro sestavení `libk4abt` .
`libk4abt<major>.<minor>`Balíček obsahuje sdílené objekty, které jsou potřebné ke spouštění spustitelných souborů, které jsou závislé na `libk4abt` nástroji, a také v prohlížeči příkladu.

Základní kurzy vyžadují `libk4abt<major>.<minor>-dev` balíček. Pokud ho chcete nainstalovat, spusťte

`sudo apt install libk4abt1.0-dev`

Pokud příkaz uspěje, sada SDK je připravena k použití.

> [!NOTE]
> Při instalaci sady SDK si zapamatujte cestu, do které jste nainstalovali. Například "C:\Program Files\Azure Kinect text Tracking SDK 1.0.0". Ukázky, na které se odkazuje v článcích v této cestě, najdete v článku.
> Ukázky sledování těla jsou umístěné ve složce [sledování obsahu](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) v úložišti Azure-Kinect-Samples. Ukázky, na které se odkazuje v článcích, najdete tady.

## <a name="change-log"></a>Protokol změn

### <a name="v101"></a>v 1.0.1
* [Oprava chyby] Opravte problém, že sada SDK selže při načítání onnxruntime.dll z cesty v systému Windows Build 19025 nebo novějším: [odkaz](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>v 1.0.0
* Zapnut Do instalačního programu MSI přidejte obálku C#.
* [Oprava chyby] Opravte problém, že otočení pozice nelze rozpoznat správně: [propojit](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [Oprava chyby] Opravte problém, že využití CPU na počítači se systémem Linux překračuje až 100%: [odkaz](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* Vzory Přidejte do vzorového úložiště dvě ukázky. Ukázka 1 ukazuje, jak transformovat výsledky sledování těla z prostoru hloubky na [odkaz](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample)na barevný prostor; Ukázka 2 ukazuje, jak detekovat [propojení](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample) roviny základny

### <a name="v095"></a>v 0.9.5
* Zapnut Podpora jazyka C#. Obálka C# je zabalená v balíčku NuGet.
* Zapnut Podpora více sledovacích služeb. Vytváření více sledovacích stop je povoleno. Nyní může uživatel vytvořit více stop pro sledování institucí z různých zařízení Kinect Azure.
* Zapnut Podpora zpracování více vláken pro režim procesoru. Při spuštění v režimu CPU se k maximalizaci rychlosti použijí všechny jádra.
* Zapnut Přidat `gpu_device_id` do `k4abt_tracker_configuration_t` struktury Umožňuje uživatelům zadat zařízení GPU, které je jiné než výchozí, aby bylo možné spustit algoritmus sledování textu.
* [Oprava chyby/zásadní změna] Opravte překlepy ve společném názvu. Změňte název společného názvu z `K4ABT_JOINT_SPINE_NAVAL` na `K4ABT_JOINT_SPINE_NAVEL` .

### <a name="v094"></a>v 0.9.4
* Zapnut Přidejte podporu ručních spojek. Sada SDK bude poskytovat informace pro tři další spoje pro každou ruku: ruka, HANDTIP, palec.
* Zapnut Přidejte úroveň spolehlivosti předpovědi pro každé zjištěné spoje.
* Zapnut Přidejte podporu režimu CPU. Změnou `cpu_only_mode` hodnoty v `k4abt_tracker_configuration_t` systému teď může sada SDK běžet v režimu CPU, který nevyžaduje, aby uživatel měl výkonnou grafickou kartu.

### <a name="v093"></a>v 0.9.3
* Zapnut Publikujte nový model DNN dnn_model_2_0. Onnx, který značně vylepšuje odolnost sledování textu.
* Zapnut Zakáže dočasné vyhlazení ve výchozím nastavení. Sledované spoje budou lépe reagovat.
* Zapnut Vylepšete přesnost mapy hlavního indexu.
* [Oprava chyby] Oprava chyby, která nastavení orientace senzoru neplatí.
* [Oprava chyby] Změňte body_index_map typ z K4A_IMAGE_FORMAT_CUSTOM na K4A_IMAGE_FORMAT_CUSTOM8.
* [Známý problém] Dva uzavírací texty mohou být sloučeny do segmentu jedné instance.

### <a name="v092"></a>v 0.9.2
* [Zásadní změna] Aktualizace závisí na nejnovější sadě Azure Kinect senzor SDK 1.2.0.
* [Změna rozhraní API] `k4abt_tracker_create` funkce začne zadávat `k4abt_tracker_configuration_t` vstup. 
* [Změna rozhraní API] Změňte `k4abt_frame_get_timestamp_usec` rozhraní API tak, aby `k4abt_frame_get_device_timestamp_usec` bylo přesnější a konzistentní se sadou 1.2.0 SDK pro senzory.
* Zapnut Umožňuje uživatelům zadat orientaci připojení senzoru při vytváření sledovacího modulu, aby bylo možné při připojování k různým úhlům dosáhnout přesnější výsledky sledování textu.
* Zapnut Poskytněte nové rozhraní API `k4abt_tracker_set_temporal_smoothing` , abyste změnili velikost dočasného vyhlazení, které chce uživatel použít.
* Zapnut Přidejte obálku C++ K4ABT. HPP.
* Zapnut Přidejte hlavičku definice verze k4abtversion. h.
* [Oprava chyby] Opravte chybu, která způsobila extrémně vysoké využití procesoru.
* [Oprava chyby] Oprava chyby při selhání protokolovacího nástroje

### <a name="v091"></a>v 0.9.1
* [Oprava chyby] Oprava nevracení paměti při zničení sledování
* [Oprava chyby] Lepší chybové zprávy pro chybějící závislosti
* [Oprava chyby] Selhání bez chyby při vytváření druhé instance sledovacího modulu
* [Oprava chyby] Proměnné prostředí protokolovacího nástroje teď fungují správně.
* Podpora Linuxu

### <a name="v090"></a>v 0.9.0

* [Zásadní změna] Přechod na verzi sady SDK na CUDA 10,0 (z CUDA 10,1). Běhový modul ONNX se oficiálně podporuje jenom až CUDA 10,0.
* [Zásadní změna] Přepnuto na modul runtime ONNX namísto modulu runtime Tensorflow. Snižuje čas spuštění prvního snímku a využití paměti. Zároveň se tím zmenší binární velikost sady SDK.
* [Změna rozhraní API] Přejmenováno `k4abt_tracker_queue_capture()` na `k4abt_tracker_enqueue_capture()`
* [Změna rozhraní API] Podařilo přerušit `k4abt_frame_get_body()` do dvou samostatných funkcí: `k4abt_frame_get_body_skeleton()` a `k4abt_frame_get_body_id()` . Nyní můžete zadat dotaz na ID textu bez nutnosti kopírování celé kostry struktury.
* [Změna rozhraní API] Přidání  `k4abt_frame_get_timestamp_usec()` funkce pro zjednodušení kroků pro uživatele k dotazování časového razítka rámce textu.
* Další vylepšení přesnosti algoritmu sledování textu a sledování spolehlivosti

## <a name="next-steps"></a>Další kroky

- [Přehled Azure Kinect DK](about-azure-kinect-dk.md)

- [Nastavení sady Azure Kinect DK](set-up-azure-kinect-dk.md)

- [Nastavení sledování textu v Azure Kinect](body-sdk-setup.md)