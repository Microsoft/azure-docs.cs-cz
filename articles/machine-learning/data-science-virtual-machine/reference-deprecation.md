---
title: 'Referenční dokumentace: Data Science Virtual Machine vyřazení imagí'
titleSuffix: Azure Data Science Virtual Machine
description: Podrobnosti o vyřazeních, která mají vliv na Data Science Virtual Machine Azure
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 07/17/2020
ms.topic: reference
ms.openlocfilehash: ac56e2e69cf62c8afae73e0a7616e7e7f80c0493
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816316"
---
# <a name="reference-retirements-of-dsvm-images"></a>Reference: vyřazení imagí DSVM

Dál se podíváme na vyřazení imagí (nepoužíváme) a návrhy na práci s nadcházejícími vyřazeními na Azure Data Science Virtual Machine.

## <a name="why-we-retire-dsvm-images"></a>Proč máme vyřazení imagí DSVM

Data Science Virtual Machine aktuálně má dvě edice:

* Ubuntu
* Windows Server

DSVM image pro tyto edice je postavená na konkrétní verzi operačního systému, třeba na Ubuntu 18,04 LTS. V průběhu času se časové období údržby pro _verzi_ operačního systému ukončí nebo nástroje pro datové vědy už nebudou podporovat starší verze operačního systému. Abychom zajistili aktuálnost image DSVM nejnovějšími operačními systémy a nástroji pro datové vědy, vydáváme novou image DSVM na základě novějších verzí operačního systému.

## <a name="how-we-retire-dsvm-images"></a>Vyřazení imagí DSVM

Vydáváme _oznámení o vyřazení_ , ve kterém se stávajícím uživatelům DSVM upozorní na nadcházející vyřazení imagí DSVM (e-mailem). Oznámení o vyřazení podrobností vyprší po _datu vyřazení_ (po tomto datu není k dispozici obrázek) a doporučení pro zmírnění rizik (například upgrade na novější bitovou kopii DSVM).

V poli datum _oznámení_ skryjeme obrázek na webu Marketplace, aby:

1. novým uživatelům by se zabránilo neúmyslnému zřízení vyřazené image DSVM.
2. stávající uživatelé můžou použít nasazení ARM až do data vyřazení.

Skrytý obrázek obdrží opravy operačního systému až do _data vyřazení_ , ale nebude __dostávat aktualizace__ nástrojů a platforem pro datové vědy. K _datu vyřazení_nebude bitová kopie k dispozici pro nasazení ARM.

Všechny zřízené image DSVM v předplatném budou fungovat i po datu vyřazení. Doporučujeme ale upgradovat na nejnovější image DSVM, abyste měli k dispozici nejnovější operační systémy a nástroje pro datové vědy.

## <a name="impact"></a>Dopad

Existující image DSVM zřízené ve vašem předplatném budou fungovat i po datu vyřazení. Nicméně doporučujeme, aby uživatelé upgradovali svou DSVM image na novější verzi pomocí šablony Azure Portal nebo ARM.

> [!WARNING]
> Vyřazení imagí DSVM zřízené pomocí Virtual Machine Scale Sets se po datu vyřazení nepůjde škálovat nahoru.
>
> Šablony ARM, které nebyly aktualizovány s novými podrobnostmi o imagi DSVM, se po datu vyřazení nepodaří nasadit.

## <a name="mitigating-upcoming-retirements"></a>Zmírnění nadcházejících důchodů

V této části probereme zmírnění zmírňující se na nadcházející vyřazení.

### <a name="upgrade-windows-2016-dsvm"></a>Upgrade systému Windows 2016 DSVM

Chcete-li migrovat datový disk ze stávající sady Windows 2016 DSVM do systému Windows 2019 DSVM, proveďte následující kroky:

1. Vytvořte nový systém Windows 2019 DSVM podle zobrazených [pokynů.](./provision-vm.md#create-your-dsvm)
1. Pomocí [těchto pokynů](../../virtual-machines/windows/detach-disk.md)odpojte stávající datové disky z image Windows 2016.
1. Pomocí [těchto pokynů](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)připojte disk z předchozího kroku k imagi Windows 2019.

### <a name="upgrade-ubuntu-1604-dsvm"></a>Upgrade Ubuntu 16,04 DSVM

Doporučujeme upgradovat stávající Ubuntu 16,04 DSVMs na [edici Ubuntu 18,04 DSVM](./dsvm-ubuntu-intro.md).
