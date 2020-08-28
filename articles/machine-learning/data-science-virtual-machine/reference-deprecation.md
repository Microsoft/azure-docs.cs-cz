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
ms.openlocfilehash: d5f541dec14eebc944e4eac11dbe569b38cb277e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001616"
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

