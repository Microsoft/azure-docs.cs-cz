---
title: Stažení sady SDK pro sadu Azure Kinect snímače
description: Přečtěte si, jak stáhnout a nainstalovat sadu Azure Kinect senzor SDK v systému Windows a Linux.
author: Brent-A
ms.author: brenta
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, SDK, stažení aktualizace, nejnovější, dostupná, instalace
ms.openlocfilehash: 591fcba4c887e298cf667c5d95c19184bc213ffe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102179625"
---
# <a name="azure-kinect-sensor-sdk-download"></a>Stažení sady SDK pro sadu Azure Kinect snímače

Tato stránka obsahuje odkazy ke stažení pro jednotlivé verze sady Azure Kinect senzor SDK. Instalační program poskytuje všechny potřebné soubory pro vývoj pro Azure Kinect.

## <a name="azure-kinect-sensor-sdk-contents"></a>Obsah sady SDK pro Azure Kinect snímač

- Hlavičky a knihovny pro sestavení aplikace s využitím Azure Kinect DK
- Distribuovatelné knihovny DLL, které potřebují aplikace s využitím Azure Kinect DK.
- [Prohlížeč Azure Kinect](azure-kinect-viewer.md).
- [Záznam služby Azure Kinect](azure-kinect-recorder.md).
- [Nástroj pro firmware Azure Kinect](azure-kinect-firmware-tool.md).

## <a name="windows-installation-instructions"></a>Pokyny k instalaci Windows

Podrobné informace o instalaci pro nejnovější a předchozí verze sady Azure Kinect senzor SDK a firmwaru najdete [tady](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

Zdrojový kód můžete najít [zde](https://github.com/microsoft/Azure-Kinect-Sensor-SDK).

> [!NOTE]
> Při instalaci sady SDK si zapamatujte cestu, do které jste nainstalovali. Například "C:\Program Files\Azure Kinect SDK 1,2". Nástroje, na které se odkazuje v článcích v této cestě, najdete v článku.

## <a name="linux-installation-instructions"></a>Pokyny k instalaci pro Linux

V současné době je jedinou podporovanou distribucí Ubuntu 18,04. Chcete-li požádat o podporu pro další distribuce, přečtěte si [tuto stránku](https://aka.ms/azurekinectfeedback).

Nejdřív budete muset nakonfigurovat [úložiště balíčků Microsoftu](https://packages.microsoft.com/), a to podle pokynů uvedených [tady](/windows-server/administration/linux-package-repository-for-microsoft-software).

Nyní můžete nainstalovat potřebné balíčky. `k4a-tools`Balíček zahrnuje [Azure Kinect Viewer](azure-kinect-viewer.md), [zapisovač Azure Kinect](record-sensor-streams-file.md)a [nástroj Azure Kinect firmware](azure-kinect-firmware-tool.md). Chcete-li nainstalovat balíček, spusťte příkaz:

`sudo apt install k4a-tools`
 
Tento příkaz nainstaluje balíčky závislostí, které jsou vyžadovány pro správné fungování nástrojů, včetně nejnovější verze nástroje `libk4a<major>.<minor>` . Pro přístup k Azure Kinect DK budete muset přidat pravidla udev, aniž by se jednat o uživatele root. Pokyny najdete v tématu [instalace zařízení se systémem Linux](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md#linux-device-setup). Alternativně můžete spustit aplikace, které zařízení používají jako kořen.

`libk4a<major>.<minor>-dev`Balíček obsahuje hlavičky a soubory cmake pro sestavování aplikací a spustitelných souborů `libk4a` .

`libk4a<major>.<minor>`Balíček obsahuje sdílené objekty potřebné ke spouštění aplikací nebo spustitelných souborů, které jsou závislé na `libk4a` .

Základní kurzy vyžadují `libk4a<major>.<minor>-dev` balíček. Chcete-li nainstalovat balíček, spusťte příkaz:

`sudo apt install libk4a<major>.<minor>-dev` 

Pokud příkaz uspěje, sada SDK je připravena k použití.

Nezapomeňte nainstalovat verzi odpovídajícího nástroje `libk4a<major>.<minor>` s nástrojem `libk4a<major>.<minor>-dev` . Pokud například nainstalujete `libk4a4.1-dev` balíček, nainstalujte odpovídající `libk4a4.1` balíček, který obsahuje odpovídající verzi souborů sdílených objektů. Nejnovější verzi nástroje najdete v `libk4a` odkazech v následující části.

## <a name="change-log-and-older-versions"></a>Změnit protokol a starší verze

Protokol změn pro sadu Azure Kinect senzor SDK najdete [tady](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/CHANGELOG.md).

Pokud potřebujete starší verzi sady Kinect senzorů Azure, Najděte ji [tady](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

## <a name="next-steps"></a>Další kroky

[Nastavení sady Azure Kinect DK](set-up-azure-kinect-dk.md)
