---
title: Požadavky na systém
description: Seznam požadavků na systém pro vzdálené vykreslování Azure
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.custom: references_regions
ms.openlocfilehash: 4380f14610fb0775c82aa79ec7cda9dc70cf0715
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2020
ms.locfileid: "97722687"
---
# <a name="system-requirements"></a>Požadavky na systém

> [!IMPORTANT]
> **Vzdálené vykreslování Azure** je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V této části jsou uvedeny minimální požadavky na systém pro práci s *Azure Remote rendering* (ARR).

## <a name="development-pc"></a>Vývojový počítač

* Windows 10 verze 1903 nebo vyšší.
* Aktuální grafické ovladače.
* Volitelné: [h265 hardwarové video dekodér](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7), pokud chcete použít místní verzi Preview vzdáleně vykresleného obsahu (například v Unity).

> [!IMPORTANT]
> Windows Update vždy neposkytuje nejnovější ovladače GPU. nejnovější ovladače najdete na webu výrobce GPU:
>
> * [Ovladače AMD](https://www.amd.com/en/support)
> * [Ovladače Intel](https://www.intel.com/content/www/us/en/support/detect.html)
> * [Ovladače NVIDIA](https://www.nvidia.com/Download/index.aspx)

Následující tabulka uvádí, které GPU podporují dekódování hardwarových videí h265.

| Výrobce GPU | Podporované modely |
|-----------|:-----------|
| GRAF | [V dolní části této stránky](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix)se podívejte na **NVDEC support Matrix** . Vaše GPU potřebuje Ano ve sloupci **H. 265 4:2:0 8-bit** . |
| AMD | GPU s minimální verzí 6 pro [Unified Video Decoder](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6)od společnosti AMD. |
| RSS | Skylake a novější procesory |

I když může být nainstalován správný kodek h265, vlastnosti zabezpečení v knihovně DLL kodeku mohou způsobit selhání inicializace kodeku. [Průvodce odstraňováním potíží](../resources/troubleshoot.md#h265-codec-not-available) popisuje kroky, jak tento problém vyřešit. K problému DLL může dojít pouze při použití služby v desktopové aplikaci, například v Unity.

## <a name="devices"></a>Zařízení

Vzdálené vykreslování Azure v současné době podporuje jenom **HoloLens 2** a Desktop Windows jako cílové zařízení. Viz část [omezení platformy](../reference/limits.md#platform-limitations) .

Je důležité používat nejnovější kodek HEVC, protože novější verze mají významnou vylepšení latence. Zjištění verze nainstalované v zařízení:

1. Spusťte **Microsoft Store**.
1. Klikněte na tlačítko **"..."** v pravém horním rohu.
1. Vyberte **soubory ke stažení a aktualizace**.
1. V seznamu vyhledejte **rozšíření videa HEVC od výrobce zařízení**. Pokud tato položka není uvedená v části aktualizace, nejnovější verze je už nainstalovaná.
1. Ujistěte se, že uvedený kodek má minimálně verzi **1.0.21821.0**.
1. Klikněte na tlačítko **získat aktualizace** a počkejte na jeho instalaci.

## <a name="network"></a>Síť

Stabilní síťové připojení s nízkou latencí je důležité pro dobré uživatelské prostředí.

[Požadavky na síť](../reference/network-requirements.md)najdete v tématu vyhrazená kapitola.

Informace o řešení problémů se sítí najdete v [Průvodci odstraňováním potíží](../resources/troubleshoot.md#unstable-holograms).

### <a name="network-firewall"></a>Síťová brána firewall

### <a name="sdk-version--0176"></a>Verze sady SDK >= 0.1.76

Virtuální počítače pro vzdálené vykreslování používají sdílené IP adresy z následujících rozsahů IP adres:

| Název             | Oblast         | Předpona IP adresy         |
|------------------|:---------------|:------------------|
| Austrálie – východ   | australiaeast  | 20.53.44.240/28   |
| East US          | eastus         | 20.62.129.224/28  |
| USA – východ 2        | eastus2        | 20.49.103.240/28  |
| Japan East       | japaneast      | 20.191.165.112/28 |
| Severní Evropa     | northeurope    | 52.146.133.64/28  |
| Středojižní USA | southcentralus | 20.65.132.80/28   |
| Southeast Asia   | southeastasia  | 20.195.64.224/28  |
| Spojené království – jih         | uksouth        | 51.143.209.144/28 |
| West Europe      | westeurope     | 20.61.99.112/28   |
| Západní USA 2        | westus2        | 20.51.9.64/28     |

Zajistěte, aby brány firewall (na zařízení, ve směrovačích atd.) neblokovaly tyto rozsahy IP adres a následující rozsahy portů:

| Port              | Protokol  | Povolit    |
|-------------------|---------- |----------|
| 49152-65534       | TCP/UDP | Odesílaná |

#### <a name="sdk-version--0176"></a>Verze sady SDK < 0.1.76

Ujistěte se, že brány firewall (na zařízení, ve směrovačích atd.) neblokují následující porty:

| Port              | Protokol | Povolit    | Popis |
|-------------------|----------|----------|-------------|
| 50051             | TCP      | Odesílaná | Počáteční připojení (HTTP handshake) |
| 8266              | UDP      | Odesílaná | Přenos dat |
| 5000, 5433, 8443  | TCP      | Odesílaná | Vyžadováno pro [Nástroj ArrInspector](../resources/tools/arr-inspector.md)|


## <a name="software"></a>Software

Musí být nainstalovaný následující software:

* Nejnovější verzi sady **Visual Studio 2019** [(Stáhnout)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Visual Studio Tools pro Mixed reality](/windows/mixed-reality/install-the-tools). Konkrétně jsou nutné následující instalace *úloh* :
  * **Vývoj desktopových aplikací v C++**
  * **Vývoj Univerzální platforma Windows (UWP)**
* **Windows SDK 10.0.18362.0** [(Stáhnout)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **Git** [(Stáhnout)](https://git-scm.com/downloads)
* Volitelné: Pokud chcete zobrazit datový proud videa ze serveru na stolním počítači, potřebujete **rozšíření videa HEVC** [(odkaz Microsoft Store)](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7). Zkontrolujte, jestli je nainstalovaná nejnovější verze, a to tak, že zkontrolujete aktualizace ve Storu.

## <a name="unity"></a>Unity

Pro vývoj s Unity nainstalujte

* Unity 2019.3.1 [(Stáhnout)](https://unity3d.com/get-unity/download)
* Nainstalujte tyto moduly v Unity:
  * Podpora **UWP** – Univerzální platforma Windows sestavení
  * **IL2CPP** – podpora sestavení pro Windows (IL2CPP)

## <a name="next-steps"></a>Další kroky

* [Rychlý Start: vykreslení modelu pomocí Unity](../quickstarts/render-model.md)