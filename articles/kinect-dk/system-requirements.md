---
title: Požadavky na systém pro sadu Kinect senzor sady Azure
description: Seznamte se s požadavky na systém pro sadu Azure Kinect senzor SDK v systému Windows a Linux.
author: qm13
ms.author: quentinm
ms.custom:
- CI 115266
- CSSTroubleshooting
manager: dcscontentpm
ms.prod: kinect-dk
ms.date: 03/05/2021
ms.topic: article
keywords: Azure, Kinect, požadavky na systém, procesor, GPU, USB, nastavení, nastavení, minimální požadavky, požadavky
ms.openlocfilehash: 558c1b9ca264874fa808aeba5f1e8a809a8f722d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656970"
---
# <a name="azure-kinect-sensor-sdk-system-requirements"></a>Požadavky na systém pro sadu Kinect senzor sady Azure

Tento dokument poskytuje podrobné informace o systémových požadavcích potřebných k instalaci sady snímač SDK a k úspěšnému nasazení služby Azure Kinect DK.

## <a name="supported-operating-systems-and-architectures"></a>Podporované operační systémy a architektury

- Windows 10. dubna 2018 (verze 1803, Build operačního systému 17134) vydání (x64) nebo novější verze
- Linux Ubuntu 18,04 (x64) s ovladačem GPU, který používá OpenGLv 4.4 nebo novější verzi

Sada snímač SDK je k dispozici pro rozhraní Windows API (Win32) pro nativní aplikace C/C++ pro Windows. Sada SDK není aktuálně k dispozici pro aplikace UWP. V režimu S Windows 10 se nepodporuje Azure Kinect DK.

## <a name="development-environment-requirements"></a>Požadavky na vývojové prostředí

Pokud chcete přispět ke vývojům sady snímač SDK, navštivte [GitHub](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK).

## <a name="minimum-host-pc-hardware-requirements"></a>Minimální požadavky na hardware počítače hostitele

Požadavek na hardware hostitele na počítači je závislý na frekvenci aplikace/algoritmu/snímku snímače/řešení provedené na hostitelském počítači. Doporučená konfigurace pro sadu SDK minimálního senzoru pro Windows je:

- Sedmý procesor Intel &reg; CoreTM i3 (Dual Core 2,4 GHz s HD620 GPU nebo rychlejší)
- 4 GB paměti
- Vyhrazený port USB3
- Podpora ovladače grafických prvků pro OpenGL 4,4 nebo DirectX 11,0

Nižší end nebo starší procesory můžou fungovat i v závislosti na vašem případu použití.

Výkon se liší taky při použití operačních systémů Windows/Linux a ovladačů grafiky.

## <a name="body-tracking-host-pc-hardware-requirements"></a>Sledování textu požadavky na hardware počítače hostitele

Požadavek na hostitele počítače sledování těla je přísnější než požadavek na obecné místo hostitele počítače. Doporučená minimální konfigurace sady SDK pro sledování textu pro Windows je:

- Sedmý &reg; CoreTM procesor Intel i5 (Quad Core 2,4 GHz nebo rychlejší)
- 4 GB paměti
- NVIDIA GEFORCE GTX 1050 nebo ekvivalentní
- Vyhrazený port USB3

Doporučená minimální konfigurace předpokládá K4A_DEPTH_MODE_NFOV_UNBINNED režim hloubky na 30fps sledování 5 lidí. Nižší nebo starší procesory a grafické procesory NVIDIA můžou fungovat i v závislosti na vašem případu použití.

## <a name="usb3"></a>USB3

Existují známé problémy s kompatibilitou s hostitelskými řadiči USB. Další informace najdete na [stránce věnované odstraňování potíží](troubleshooting.md#usb3-host-controller-compatibility) .

## <a name="next-steps"></a>Další kroky

- [Přehled Azure Kinect DK](about-azure-kinect-dk.md)

- [Nastavení sady Azure Kinect DK](set-up-azure-kinect-dk.md)

- [Nastavení sledování textu v Azure Kinect](body-sdk-setup.md)
