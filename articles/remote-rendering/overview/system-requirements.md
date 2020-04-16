---
title: Systémové požadavky
description: Uvádí systémové požadavky pro vzdálené vykreslování Azure.
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.openlocfilehash: 8573a88d5371bbde07a541c789f52e6c44f1e279
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411135"
---
# <a name="system-requirements"></a>Systémové požadavky

> [!IMPORTANT]
> **Vzdálené vykreslování Azure** je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tato kapitola uvádí minimální systémové požadavky pro práci s *Azure Remote Rendering* (ARR).

## <a name="development-pc"></a>Vývoj PC

* Windows 10 verze 1903 nebo vyšší.
* Aktuální grafické ovladače.
* Volitelné: H265 hardwarový video dekodér, pokud chcete použít místní náhled vzdáleně vykresleného obsahu (například v Unity).

> [!IMPORTANT]
> Windows Update ne vždy poskytuje nejnovější ovladače GPU, podívejte se na web výrobce GPU pro nejnovější ovladače:
>
> * [Ovladače AMD](https://www.amd.com/en/support)
> * [Ovladače Intel](https://www.intel.com/content/www/us/en/support/detect.html)
> * [Ovladače NVIDIA](https://www.nvidia.com/Download/index.aspx)

V následující tabulce je uvedeno, které gpu podporují dekódování h265 hardwarového videa.

| Výrobce GPU | Podporované modely |
|-----------|:-----------|
| Nvidia | Zkontrolujte **Matice podpory NVDEC** [v dolní části této stránky](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix). Gpu potřebuje ANO v **8bitovém sloupci H.265 4:2:0.** |
| Amd | GPU s alespoň verzí 6 [jednotného video dekodéru](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6)AMD . |
| Intel | Skylake a novější procesory |

I když může být nainstalován správný kodek H265, vlastnosti zabezpečení na kodeku DLL mohou způsobit selhání inicializace kodeku. [Průvodce odstraňováním potíží](../resources/troubleshoot.md#h265-codec-not-available) popisuje postup řešení tohoto problému. Problém dll může dojít pouze při použití služby v desktopové aplikaci, například v Unity.

## <a name="devices"></a>Zařízení

Azure Remote Rendering v současné době podporuje jenom **HoloLens 2** a Plochu Windows jako cílové zařízení. Podívejte se na sekci [omezení platformy.](../reference/limits.md#platform-limitations)

Je důležité používat nejnovější kodek HEVC, protože novější verze mají významné zlepšení latence. Chcete-li zkontrolovat, která verze je v zařízení nainstalována:

1. Spusťte **microsoft store**.
1. Klikněte na tlačítko **"..."** v pravém horním rohu.
1. Vyberte **položku Soubory ke stažení a aktualizace**.
1. Vyhledejte v seznamu **rozšíření HEVC Video Extensions od výrobce zařízení**.
1. Ujistěte se, že uvedený kodek má alespoň verzi **1.0.21821.0**.
1. Klikněte na tlačítko **Získat aktualizace** a počkejte, až se nainstaluje.

## <a name="network"></a>Síť

Stabilní síťové připojení s nízkou latencí je důležité pro dobré uživatelské prostředí.

Požadavky na [síť](../reference/network-requirements.md)naleznete v kapitole věnované této síti .

Řešení potíží se sítí naleznete v [příručce pro řešení potíží](../resources/troubleshoot.md#unstable-holograms).

## <a name="software"></a>Software

Musí být nainstalován následující software:

* Nejnovější verze **Visual Studia 2019** [(ke stažení)](https://visualstudio.microsoft.com/vs/older-downloads/)
* **Sada Windows SDK 10.0.18362.0** [(stáhnout)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **GIT** [(stáhnout)](https://git-scm.com/downloads)
* Volitelné: Chcete-li zobrazit datový proud videa ze serveru na stolním počítači, potřebujete **rozšíření HEVC Video Extensions** [(odkaz na microsoft store)](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7).

## <a name="unity"></a>Unity

Pro vývoj s Unity nainstalujte

* Unity 2019.3.1 [(ke stažení)](https://unity3d.com/get-unity/download)
* Nainstalujte tyto moduly v Unity:
  * **UPWP** – podpora sestavení univerzální platformy Windows
  * **IL2CPP** - Podpora sestavení systému Windows (IL2CPP)

## <a name="next-steps"></a>Další kroky

* [Úvodní příručka: Vykreslení modelu pomocí Unity](../quickstarts/render-model.md)
