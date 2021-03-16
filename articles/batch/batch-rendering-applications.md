---
title: Vykreslování aplikací
description: Je možné použít jakékoli aplikace pro vykreslování Azure Batch. Azure Marketplace imagí virtuálních počítačů ale jsou k dispozici společně s předinstalovanými běžnými aplikacemi.
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: c98e2e0a81051dad47c201de9eda9f89cc311cf2
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496639"
---
# <a name="pre-installed-applications-on-batch-rendering-vm-images"></a>Předinstalované aplikace na imagí virtuálních počítačů dávkového vykreslování

Je možné použít jakékoli aplikace pro vykreslování Azure Batch. Azure Marketplace imagí virtuálních počítačů ale jsou k dispozici společně s předinstalovanými běžnými aplikacemi.

V případě potřeby jsou pro předinstalované aplikace pro vykreslování k dispozici licence k platbám za použití. Když se vytvoří fond Batch, můžou se zadat požadované aplikace a účtují se náklady na virtuální počítače i aplikace za minutu. Ceny za aplikace jsou uvedené na [stránce s cenami Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Některé aplikace podporují jenom Windows, ale většina je podporovaná v systémech Windows i Linux.

> [!IMPORTANT]
> Vykreslování imagí virtuálních počítačů a licencování s platbami za použití se už [nepoužívá a vyřadí se od 29. února 2024](https://azure.microsoft.com/updates/azure-batch-rendering-vm-images-licensing-will-be-retired-on-29-february-2024/). Chcete-li použít dávku pro vykreslování, je [třeba použít vlastní image virtuálního počítače a licencování standardní aplikace.](batch-rendering-functionality.md#batch-pools-using-custom-vm-images-and-standard-application-licensing)

## <a name="applications-on-latest-centos-7-rendering-image"></a>Aplikace na nejnovější obrázek vykreslování CentOS 7

Následující seznam se vztahuje na obrázek vykreslování CentOS verze 1.2.0.

* Autodesk Maya I/O 2020 aktualizace 4,6
* Autodesk Arnold pro Maya 2020 (Arnold verze 6.2.0.0) MtoA-4.2.0-2020
* Chaos Group V-Ray pro Maya 2020 (verze 5.00.21)
* Blend (2,80)
* AZ 10

## <a name="applications-on-latest-windows-server-rendering-image"></a>Aplikace na nejnovější imagi vykreslování Windows serveru

Následující seznam se vztahuje na bitovou kopii Windows serveru pro vykreslování verzí 1.5.0.

* Autodesk Maya I/O 2020 aktualizace 4,4
* Autodesk 3ds Max I/O 2021 Update 3
* Autodesk Arnold pro Maya 2020 (Arnold verze 6.1.0.1) MtoA-4.1.1.1-2020
* Autodesk Arnold pro 3ds Max 2021 (Arnold verze 6.1.0.1) MAXtoA-4.2.2.20-2021
* Chaos Group V-Ray pro Maya 2020 (verze 5.00.21)
* Chaos Group V-Ray pro 3ds Max 2021 (verze 5.00.05)
* Blender (2.79)
* Blend (2,80)
* AZ 10

> [!IMPORTANT]
> Pokud chcete spustit V-Ray s Maya mimo [šablony rozšíření Azure Batch](https://github.com/Azure/batch-extension-templates), začněte `vrayses.exe` ještě před spuštěním vykreslování. Pokud chcete začít vrayses.exe mimo šablony, můžete použít následující příkaz `%MAYA_2020%\vray\bin\vrayses.exe"` .
>
> Příklad najdete v tématu spuštění úlohy [šablony Maya a v-Ray](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json) na GitHubu.

## <a name="applications-on-previous-windows-server-rendering-images"></a>Aplikace na předchozích obrázcích vykreslování Windows serveru

Následující seznam platí pro Windows Server 2016, verze vykreslování imagí 1.3.8.

* Autodesk Maya I/O 2017 Update 5 (verze 17.4.5459)
* Autodesk Maya I/O 2018 Update 6 (verze 18.4.0.7622)
* I/O Autodesk Maya v/v 2019
* Autodesk 3ds Max I/O 2018 Update 4 (verze 20.4.0.4254)
* Autodesk 3ds Max I/O 2019 Update 1 (verze 21.2.0.2219)
* Autodesk 3ds Max I/O 2020 Update 2
* Autodesk Arnold pro Maya 2017 (Arnold Version 5.3.0.2) MtoA-3.2.0.2-2017
* Autodesk Arnold pro Maya 2018 (Arnold Version 5.3.0.2) MtoA-3.2.0.2-2018
* Autodesk Arnold pro Maya 2019 (Arnold Version 5.3.0.2) MtoA-3.2.0.2-2019
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
* Blend (2,80)
* AZ 10

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

## <a name="applications-on-previous-centos-rendering-images"></a>Aplikace na předchozích CentOS vykreslování imagí

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

## <a name="next-steps"></a>Další kroky

Pokud chcete použít image virtuálních počítačů, musí se při vytvoření fondu zadat v konfiguraci fondu. Podívejte se na [Možnosti fondu Batch pro vykreslování](./batch-rendering-functionality.md).
