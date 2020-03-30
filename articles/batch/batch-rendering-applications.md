---
title: Vykreslovací aplikace – Azure Batch
description: Je možné použít všechny vykreslovací aplikace s Azure Batch. Image virtuálních počítačů Azure Marketplace jsou však dostupné s předinstalovanými běžnými aplikacemi.
services: batch
ms.service: batch
author: LauraBrenner
ms.author: labrenne
ms.date: 09/19/2019
ms.topic: conceptual
ms.openlocfilehash: 77672534b2aad993a44e9b637fbed58df8610e97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022983"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Předinstalované aplikace při vykreslování imitací virtuálních aplikací

Je možné použít všechny vykreslovací aplikace s Azure Batch. Image virtuálních počítačů Azure Marketplace jsou však dostupné s předinstalovanými běžnými aplikacemi.

V případě potřeby je pro předinstalované vykreslovací aplikace k dispozici licence na platbu za použití. Při vytvoření fondu dávek lze zadat požadované aplikace a náklady na virtuální ms i aplikace se budou účtovat za minutu. Ceny aplikací jsou uvedeny na [stránce ceny Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Některé aplikace podporují pouze systém Windows, ale většina z nich je podporována v systému Windows i Linuxu.

## <a name="applications-on-centos-7-rendering-images"></a>Aplikace na renderovacích obrázcích CentOS 7

Následující seznam se vztahuje na centos 7.6, verze 1.1.6 vykreslování obrázků.

* Autodesk Maya I/O 2017 Update 5 (verze 201708032230)
* Autodesk Maya I/O 2018 Aktualizace 2 (střih 201711281015)
* Autodesk Maya I/O 2019 Aktualizace 1
* Autodesk Arnold pro Maya 2017 (Arnold verze 5.3.1.1) MtoA-3.2.1.1-2017
* Autodesk Arnold pro Maya 2018 (Arnold verze 5.3.1.1) MtoA-3.2.1.1-2018
* Autodesk Arnold pro Maya 2019 (Arnold verze 5.3.1.1) MtoA-3.2.1.1-2019
* Chaos Group V-Ray pro Maya 2017 (verze 3.60.04)
* Chaos Group V-Ray pro Maya 2018 (verze 3.60.04)
* Blender (2.68)
* Mixér (2.8)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>Aplikace na nejnovějších vykreslování obrázků windows serveru 2016

Následující seznam se vztahuje na vykreslovací obrazy Windows Server 2016 verze 1.3.8.

* Autodesk Maya I/O 2017 Update 5 (verze 17.4.5459)
* Autodesk Maya I/O 2018 Aktualizace 6 (verze 18.4.0.7622)
* Autodesk Maya I/O 2019
* Autodesk 3ds Max I/O 2018 Update 4 (verze 20.4.0.4254)
* Autodesk 3ds Max I/O 2019 Aktualizace 1 (verze 21.2.0.2219)
* Autodesk 3ds Max I/O 2020 Aktualizace 2
* Autodesk Arnold pro Maya 2017 (Arnold verze 5.3.0.2) MtoA-3.2.0.2-2017
* Autodesk Arnold pro Maya 2018 (Arnold verze 5.3.0.2) MtoA-3.2.0.2-2018
* Autodesk Arnold pro Maya 2019 (Arnold verze 5.3.0.2) MtoA-3.2.0.2-2019
* Autodesk Arnold pro 3ds Max 2018 (Arnold verze 5.3.0.2) (verze 1.2.926)
* Autodesk Arnold pro 3ds Max 2019 (Arnold verze 5.3.0.2) (verze 1.2.926)
* Autodesk Arnold pro 3ds Max 2020 (Arnold verze 5.3.0.2) (verze 1.2.926)
* Chaos Group V-Ray pro Maya 2017 (verze 4.12.01)
* Chaos Group V-Ray pro Maya 2018 (verze 4.12.01)
* Chaos Group V-Ray pro Maya 2019 (verze 4.04.03)
* Chaos Group V-Ray pro 3ds Max 2018 (verze 4.20.01)
* Chaos Group V-Ray pro 3ds Max 2019 (verze 4.20.01)
* Chaos Group V-Ray pro 3ds Max 2020 (verze 4.20.01)
* Blender (2.79)
* Mixér (2.80)
* AZ 10

> [!IMPORTANT]
> Chcete-li spustit V-Ray s Maya mimo šablony `vrayses.exe` rozšíření Azure [Batch](https://github.com/Azure/batch-extension-templates), spusťte před spuštěním vykreslení. Chcete-li spustit vrayses.exe mimo šablony, můžete `%MAYA_2017%\vray\bin\vrayses.exe"`použít následující příkaz .
>
> Například viz počáteční úloha [mayské a v-ray šablony](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json) na GitHubu.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Aplikace na předchozích vykreslování bitových kopií Windows Serveru 2016

Následující seznam se vztahuje na obrázky vykreslování windows server 2016 verze 1.3.7.

* Autodesk Maya I/O 2017 Update 5 (verze 17.4.5459)
* Autodesk Maya I/O 2018 Aktualizace 4 (verze 18.4.0.7622)
* Autodesk 3ds Max I/O 2019 Aktualizace 1 (verze 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (verze 20.4.0.4254)
* Autodesk Arnold pro Maya 2017 (Arnold verze 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold pro Maya 2018 (Arnold verze 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold pro 3ds Max 2018 (Arnold verze 5.0.2.4) (verze 1.2.926)
* Autodesk Arnold pro 3ds Max 2019 (Arnold verze 5.0.2.4) (verze 1.2.926)
* Chaos Group V-Ray pro Maya 2018 (verze 3.52.03)
* Chaos Group V-Ray pro 3ds Max 2018 (verze 3.60.02)
* Chaos Group V-Ray pro Maya 2019 (verze 3.52.03)
* Chaos Group V-Ray pro 3ds Max 2019 (verze 4.10.01)
* Blender (2.79)

> [!NOTE]
> Chaos Group V-Ray pro 3ds Max 2019 (verze 4.10.01) zavádí nejnovější změny V-ray. Chcete-li použít předchozí verzi (verze 3.60.02), použijte vykreslovací uzly Windows Server 2016 verze 1.3.2.

## <a name="next-steps"></a>Další kroky

Chcete-li použít vykreslovací image virtuálního počítače, je třeba je zadat v konfiguraci fondu při vytvoření fondu; Naleznete [možnosti fondu dávek pro vykreslování](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).
