---
title: Vykreslovací aplikace – Azure Batch
description: Předinstalované aplikace Batch vykreslování
services: batch
ms.service: batch
author: laurenhughes
ms.author: lahugh
ms.date: 03/26/2018
ms.topic: conceptual
ms.openlocfilehash: 84b2ca30f1ccd49e18e2f9d42133f8672d3f8ad6
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58496016"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Předinstalované aplikace na vykreslování imagí virtuálních počítačů

Je možné používat všechny aplikace vykreslování pomocí služby Azure Batch. Image virtuálního počítače Azure Marketplace jsou však k dispozici s předinstalovanými běžných aplikací.

Kde je to možné, licencování průběžnými platbami podle využití je k dispozici pro předem nainstalované vykreslovacími aplikacemi. Při vytvoření fondu služby Batch je možné zadat požadované aplikace a budou se fakturovat i náklady na virtuální počítač a aplikací za minutu. Aplikace ceny jsou uvedené na [stránce s cenami služby Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Některé aplikace podporují jenom Windows, ale většina jsou podporované ve Windows a Linuxu.

## <a name="applications-on-centos-7-rendering-images"></a>Aplikace na CentOS 7 vykreslení obrázků

* Autodesk Maya I/O 2017 Update 5 (verze 201708032230)
* Autodesk Maya I/O 2018 Update 2 (Vyjmout 201711281015)
* Autodesk Arnold pro Maya 2017 (Arnold verze 5.0.1.1) MtoA-2.0.1.1-2017
* Autodesk Arnold pro Maya 2018 (Arnold verze 5.0.1.4) MtoA-2.1.0.3-2018
* Chaos Group V-Ray pro Maya 2017 (verze 3.60.04)
* Chaos Group V-Ray pro Maya 2018 (verze 3.60.04)
* Blender (2.68)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>Aplikace na nejnovější vykreslení obrázků systému Windows Server 2016

V následujícím seznamu se vztahuje na Windows Server 2016, verze 1.3.4 vykreslení obrázků.

* Autodesk Maya I/O 2017 Update 5 (verze 17.4.5459)
* Autodesk Maya I/O 2018 Update 4 (verze 18.4.0.7622)
* Autodesk 3ds Max vstupně-výstupních operací. 2019 Update 1 (verze 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (verze 20.4.0.4254)
* Autodesk Arnold pro Maya 2017 (Arnold verze 5.2.0.1) MtoA-3.1.0.1 – 2017
* Autodesk Arnold pro Maya 2018 (Arnold verze 5.2.0.1) MtoA – 3.1.0.1 – 2018
* Autodesk Arnold pro 3ds Max (verze 5.0.2.4)(version Arnold 1.2.926)
* Chaos Group V-Ray pro Maya 2018 (verze 3.52.03)
* Chaos Group V-Ray pro 3ds Max 2018 (verze 3.60.02)
* Chaos Group V-Ray pro Maya 2019 (verze 3.52.03)
* Chaos Group V-Ray pro 3ds Max 2019 (verze 4.10.01)
* Blender (2.79)

> [!NOTE]
> Chaos Group V-Ray pro 3ds Max 2019 (verze 4.10.01) přináší změny způsobující chyby V-ray. Pokud chcete použít předchozí verze (verze 3.60.02), použijte Windows Server 2016, verze 1.3.2 vykreslování uzly.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Aplikace na předchozí vykreslení obrázků systému Windows Server 2016

V následujícím seznamu se vztahuje na Windows Server 2016, verze 1.3.2 vykreslení obrázků.

* Autodesk Maya I/O 2017 Update 5 (verze 17.4.5459)
* Autodesk Maya I/O 2018 Update 4 (verze 18.4.0.7622)  
* Autodesk 3ds Max vstupně-výstupních operací. 2019 Update 1 (verze 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (verze 20.4.0.4254)
* Autodesk Arnold pro Maya 2017 (Arnold verze 5.2.0.1) MtoA-3.1.0.1 – 2017
* Autodesk Arnold pro Maya 2018 (Arnold verze 5.2.0.1) MtoA – 3.1.0.1 – 2018
* Autodesk Arnold pro 3ds Max (verze 5.0.2.4)(version Arnold 1.2.926)
* Chaos Group V-Ray pro Maya 2019 (verze 3.52.03)
* Chaos Group V-Ray pro 3ds Max 2018 (verze 3.60.02)
* Blender (2.79)

## <a name="next-steps"></a>Další postup

Použití imagí virtuálních počítačů vykreslování, musí být zadaný v konfiguraci fondu při vytváření fondu; Zobrazit [Batch možnosti fondu pro vykreslování](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).