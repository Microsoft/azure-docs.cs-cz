---
title: Vykreslování aplikací – Azure Batch
description: Předem instalované aplikace dávkového vykreslování
services: batch
ms.service: batch
author: laurenhughes
ms.author: lahugh
ms.date: 09/10/2019
ms.topic: conceptual
ms.openlocfilehash: 2b0a132c156cc12d317bf51488625191bb8091fc
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881462"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Předem instalované aplikace na vykreslování imagí virtuálních počítačů

Je možné použít jakékoli aplikace pro vykreslování Azure Batch. Azure Marketplace imagí virtuálních počítačů ale jsou k dispozici společně s předinstalovanými běžnými aplikacemi.

V případě potřeby jsou pro předinstalované aplikace pro vykreslování k dispozici licence k platbám za použití. Když se vytvoří fond Batch, můžou se zadat požadované aplikace a účtují se náklady na virtuální počítače i aplikace za minutu. Ceny za aplikace jsou uvedené na [stránce s cenami Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Některé aplikace podporují jenom Windows, ale většina je podporovaná v systémech Windows i Linux.

## <a name="applications-on-centos-7-rendering-images"></a>Aplikace na vykreslování imagí v CentOS 7

Následující seznam se vztahuje na CentOS 7,6, verze vykreslování imagí 1.1.6.

* Autodesk Maya I/O 2017 Update 5 (verze 201708032230)
* I/O Autodesk Maya 2018 Update 2 (vyjmout 201711281015)
* Autodesk Maya I/O 2019 aktualizace 1
* Autodesk Arnold pro Maya 2017 (Arnold Version 5.3.1.1) MtoA-3.2.1.1-2017
* Autodesk Arnold pro Maya 2018 (Arnold Version 5.3.1.1) MtoA-3.2.1.1-2018
* Autodesk Arnold pro Maya 2019 (Arnold Version 5.3.1.1) MtoA-3.2.1.1-2019
* Chaos Group V-Ray pro Maya 2017 (verze 3.60.04)
* Chaos Group V-Ray pro Maya 2018 (verze 3.60.04)
* Blender (2.68)
* Blend (2,8)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>Aplikace na nejnovějších imagích pro vykreslování Windows serveru 2016

Následující seznam platí pro Windows Server 2016, verze vykreslování imagí 1.3.7.

* Autodesk Maya I/O 2017 Update 5 (verze 17.4.5459)
* Autodesk Maya I/O 2018 Update 4 (verze 18.4.0.7622)
* Autodesk 3ds Max I/O 2019 Update 1 (verze 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (verze 20.4.0.4254)
* Autodesk Arnold pro Maya 2017 (Arnold Version 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold pro Maya 2018 (Arnold Version 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold pro 3ds Max 2018 (Arnold verze 5.0.2.4) (verze 1.2.926)
* Autodesk Arnold pro 3ds Max 2019 (Arnold verze 5.0.2.4) (verze 1.2.926)
* Chaos Group V-Ray pro Maya 2018 (verze 3.52.03)
* Chaos Group V-Ray pro 3ds Max 2018 (verze 3.60.02)
* Chaos Group V-Ray pro Maya 2019 (verze 3.52.03)
* Chaos Group V-Ray pro 3ds Max 2019 (verze 4.10.01)
* Blender (2.79)


> [!NOTE]
> Chaos Group V-Ray pro 3ds Max 2019 (verze 4.10.01) přináší zásadní změny V-Ray. K použití předchozí verze (verze 3.60.02) použijte Windows Server 2016, uzly vykreslování verze 1.3.2.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Aplikace na předchozích obrázcích vykreslování Windows serveru 2016

Následující seznam platí pro Windows Server 2016, verze 1.3.2 vykreslování imagí.

* Autodesk Maya I/O 2017 Update 5 (verze 17.4.5459)
* Autodesk Maya I/O 2018 Update 4 (verze 18.4.0.7622)  
* Autodesk 3ds Max I/O 2019 Update 1 (verze 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (verze 20.4.0.4254)
* Autodesk Arnold pro Maya 2017 (Arnold Version 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold pro Maya 2018 (Arnold Version 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold pro 3ds Max (Arnold verze 5.0.2.4) (verze 1.2.926)
* Chaos Group V-Ray pro Maya 2019 (verze 3.52.03)
* Chaos Group V-Ray pro 3ds Max 2018 (verze 3.60.02)
* Blender (2.79)

## <a name="next-steps"></a>Další postup

Pokud chcete použít image virtuálních počítačů, musí se při vytvoření fondu zadat v konfiguraci fondu. Podívejte se na [Možnosti fondu Batch pro vykreslování](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).
