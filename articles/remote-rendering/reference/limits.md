---
title: Omezení
description: Omezení kódu pro funkce sady SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b6e501cdc1d5091a27d86406c6919587bedb261a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417687"
---
# <a name="limitations"></a>Omezení

Množství funkcí má velikost, počet nebo jiná omezení.

## <a name="azure-frontend"></a>Azure front-end

* Celkový počet instancí AzureFrontend na proces: 16.
* Celkový počet instancí AzureSession na AzureFrontend: 16.

## <a name="objects"></a>Objekty

* Celkem přípustných objektů jednoho typu (entita, CutPlaneComponent atd.): 16 777 215.
* Celkový povolený počet aktivních vyjmutých ploch: 8.

## <a name="materials"></a>Materiály

* Celkový počet povolených materiálů v prostředku: 65 535.

## <a name="overall-number-of-polygons"></a>Celkový počet mnohoúhelníků

Povolený počet mnohoúhelníků pro všechny načtené modely závisí na velikosti virtuálního počítače, který je předaný do [REST API správy relace](../how-tos/session-rest-api.md#create-a-session):

| Velikost virtuálního počítače | Maximální počet mnohoúhelníků |
|:--------|:------------------|
|standardní| 20 000 000 |
|nárok| bez omezení |


## <a name="platform-limitations"></a>Omezení platformy

**Stolní počítač s Windows 10**

* Nasazení samostatného počítače z Unity se nepodporuje. Místo toho použijte UWP.
* UWP/x86 je jediná podporovaná platforma UWP. UWP/x64 se nepodporuje.

**HoloLens 2**

* Funkce [vykreslování z PV fotoaparátu](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) není podporována.
