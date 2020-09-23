---
title: Omezení
description: Omezení kódu pro funkce sady SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: e6b12c2bac4a9732f868f6a6ac3491ef993f54c3
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90976555"
---
# <a name="limitations"></a>Omezení

Množství funkcí má velikost, počet nebo jiná omezení.

## <a name="azure-frontend"></a>Azure front-end

* Celkový počet instancí AzureFrontend na proces: 16.
* Celkový počet instancí AzureSession na AzureFrontend: 16.

## <a name="objects"></a>Objekty

* Celkem přípustných objektů jednoho typu (entita, CutPlaneComponent atd.): 16 777 215.
* Celkový povolený počet aktivních vyjmutých ploch: 8.

## <a name="geometry"></a>Geometrie

* Celkový počet povolených materiálů v prostředku: 65 535. Další informace najdete v tématu o [odstranění duplicit v materiálech](../how-tos/conversion/configure-model-conversion.md#material-de-duplication) .
* Maximální rozměr jedné textury: 16 384 × 16 384. Větší zdrojové textury se škálují podle procesu převodu.

## <a name="overall-number-of-polygons"></a>Celkový počet mnohoúhelníků

Povolený počet mnohoúhelníků pro všechny načtené modely závisí na velikosti virtuálního počítače, který je předaný do [REST API správy relace](../how-tos/session-rest-api.md#create-a-session):

| Velikost serveru | Maximální počet mnohoúhelníků |
|:--------|:------------------|
|standardní| 20 000 000 |
|nárok| bez omezení |

Podrobnější informace o tomto omezení najdete v části [velikost serveru](../reference/vm-sizes.md) .

## <a name="platform-limitations"></a>Omezení platformy

**Stolní počítač s Windows 10**

* Jedinou podporovanou platformou Win32 je Win32/x64. Win32/x86 se nepodporuje.

**HoloLens 2**

* Funkce [vykreslování z PV fotoaparátu](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) není podporována.
