---
title: Omezení
description: Omezení kódu pro funkce sady SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 6e23c716a965cdf178c9f847900e66c141d5d7eb
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680333"
---
# <a name="limits"></a>Omezení

Řada funkcí má omezení velikosti nebo počtu kvůli interním podrobnostem spuštěného systému.

## <a name="azure-frontend"></a>Azure Front-end

* Celkový počet instancí AzureFrontend na proces: 16.
* Celkový počet instancí AzureSession na AzureFrontend: 16.

## <a name="objects"></a>Objekty

* Celkový počet povolených objektů jednoho typu (entita, komponenta CutPlaneComponent atd.): 16 777 215.
* Celkový přípustný počet aktivních rovin řezu: 8.

## <a name="materials"></a>Materials

* Celkový přípustný počet povolených materiálů v aktivu: 65 535.

## <a name="overall-number-of-polygons"></a>Celkový počet mnohonožek

Povolený počet mnohomocnic pro všechny načtené modely závisí na velikosti virtuálního počítače, jak je předán do [rozhraní REST API pro správu relací](../how-tos/session-rest-api.md#create-a-session):

| Velikost virtuálního počítače | Maximální počet mnohonožek |
|:--------|:------------------|
|Standardní| 20 milionů |
|Premium| bez omezení |



