---
title: Vykreslení aplikace batch
description: Předinstalované aplikace Batch vykreslování
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 28acd1b7275694d38a52f14d2b2c32b79cc8183e
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "40034752"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Předinstalované aplikace na vykreslování imagí virtuálních počítačů

Je možné používat všechny aplikace vykreslování pomocí služby Azure Batch. Image virtuálního počítače Azure Marketplace jsou však k dispozici s předinstalovanými běžných aplikací.

Kde je to možné, licencování průběžnými platbami podle využití je k dispozici pro předem nainstalované vykreslovacími aplikacemi. Při vytvoření fondu služby Batch je možné zadat požadované aplikace a budou se fakturovat i náklady na virtuální počítač a aplikací za minutu. Aplikace ceny jsou uvedené na [stránce s cenami služby Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Některé aplikace podporují jenom Windows, ale většina jsou podporované ve Windows a Linuxu.

## <a name="applications-on-centos-7-rendering-nodes"></a>Aplikace na CentOS 7 uzlů vykreslování

* Autodesk Maya I/O 2017 Update 5 (verze 201708032230)
* Autodesk Maya I/O 2018 Update 2 (Vyjmout 201711281015)
* Autodesk Arnold pro Maya 2017 (Arnold verze 5.0.1.1) MtoA-2.0.1.1-2017
* Autodesk Arnold pro Maya 2018 (Arnold verze 5.0.1.4) MtoA-2.1.0.3-2018
* Chaos Group V-Ray pro Maya 2017 (verze 3.60.04)
* Chaos Group V-Ray pro Maya 2018 (verze 3.60.04)
* Blender (2.68)

## <a name="applications-on-windows-server-2016-rendering-nodes"></a>Aplikace na Windows serveru 2016 uzlů vykreslování

* Autodesk Maya I/O 2017 Update 5 (verze 17.4.5459)
* Autodesk Maya vstupně-výstupních operací 2018 Update 3 (verze 18.3.0.7040)  
* Autodesk 3ds Max vstupně-výstupních operací. 2019 Update 1 (verze 21.10.1314)
* Autodesk 3ds Max I/O 2018 Update 4 (verze 20.4.0.4254)
* Autodesk Arnold pro Maya (Arnold verze 5.0.1.1) MtoA-2.0.1.1-2017
* Autodesk Arnold pro Maya (Arnold verze 5.0.1.4) MtoA-2.0.2.3-2018
* Autodesk Arnold pro 3ds Max (verze 5.0.2.4)(version Arnold 1.2.926)
* Chaos Group V-Ray pro Maya (verze 3.52.03)
* Chaos Group V-Ray pro 3ds Max (verze 3.60.02)
* Blender (2.79)

## <a name="next-steps"></a>Další postup

Použití imagí virtuálních počítačů vykreslování, musí být zadaný v konfiguraci fondu při vytváření fondu; Zobrazit [Batch možnosti fondu pro vykreslování](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).