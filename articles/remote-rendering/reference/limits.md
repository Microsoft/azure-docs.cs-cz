---
title: Omezení
description: Omezení kódu pro funkce sady SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b6e501cdc1d5091a27d86406c6919587bedb261a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417687"
---
# <a name="limitations"></a>Omezení

Řada funkcí má velikost, počet nebo jiná omezení.

## <a name="azure-frontend"></a>Azure Front-end

* Celkový počet instancí AzureFrontend na proces: 16.
* Celkový počet instancí AzureSession na AzureFrontend: 16.

## <a name="objects"></a>Objekty

* Celkový počet povolených objektů jednoho typu (entita, komponenta CutPlaneComponent atd.): 16 777 215.
* Celkový přípustný počet aktivních rovin řezu: 8.

## <a name="materials"></a>Materiály

* Celkový přípustný počet povolených materiálů v aktivu: 65 535.

## <a name="overall-number-of-polygons"></a>Celkový počet mnohonožek

Povolený počet mnohomocnic pro všechny načtené modely závisí na velikosti virtuálního počítače, jak je předán do [rozhraní REST API pro správu relací](../how-tos/session-rest-api.md#create-a-session):

| Velikost virtuálního počítače | Maximální počet mnohonožek |
|:--------|:------------------|
|Standardní| 20 milionů |
|Premium| bez omezení |


## <a name="platform-limitations"></a>Omezení platformy

**Stolní počítač s Windows 10**

* "Pc Standalone" nasazení z Unity není podporována. Místo toho použijte UPW.
* UWP/x86 je jedinou podporovanou platformou UPW. UWP/x64 není podporován.

**Hololens 2**

* Funkce [vykreslení z PV kamery](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) není podporována.
