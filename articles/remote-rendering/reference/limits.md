---
title: Omezení
description: Omezení kódu pro funkce sady SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 68c0c04feba2779598a500c84b2ba4a9086b104d
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593941"
---
# <a name="limitations"></a>Omezení

Množství funkcí má velikost, počet nebo jiná omezení.

## <a name="azure-frontend"></a>Azure front-end

Následující omezení se vztahují na front-endové rozhraní API (C++ a C#):
* Celkový počet instancí [RemoteRenderingClient](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient) na proces: 16.
* Celkový počet instancí [RenderingSession](/dotnet/api/microsoft.azure.remoterendering.renderingsession) na [RemoteRenderingClient](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient): 16.

## <a name="objects"></a>Objekty

* Celkem přípustných objektů jednoho typu ([entita](../concepts/entities.md), [CutPlaneComponent](../overview/features/cut-planes.md)atd.): 16 777 215.
* Celkový povolený počet aktivních vyjmutých ploch: 8.

## <a name="geometry"></a>Geometrie

* **Animace:** Animace jsou omezené na animace jednotlivých transformací [herních objektů](../concepts/entities.md). Animace základní s animacemi s použitím změny vzhledu a vrcholu se nepodporují. Animační běhy ze zdrojového souboru prostředků se nezachovají. Místo toho musí být animace transformace objektů řízeny klientským kódem.
* **Vlastní shadery:** Vytváření vlastních shaderů se nepodporuje. Je možné použít pouze předdefinované [barevné materiály](../overview/features/color-materials.md) nebo [materiály PBR](../overview/features/pbr-materials.md) .
* **Maximální počet různých materiálů** v prostředku: 65 535. Další informace o automatickém snížení počtu materiálu najdete v kapitole [de-duplikace materiálu](../how-tos/conversion/configure-model-conversion.md#material-de-duplication) .
* **Maximální rozměr jedné textury**: 16 384 × 16 384. V rámci procesu převodu se velikost větších textur od velkých zdrojů zmenší.

### <a name="overall-number-of-polygons"></a>Celkový počet mnohoúhelníků

Povolený počet mnohoúhelníků pro všechny načtené modely závisí na velikosti virtuálního počítače, který je předaný do [REST API správy relace](../how-tos/session-rest-api.md#create-a-session):

| Velikost serveru | Maximální počet mnohoúhelníků |
|:--------|:------------------|
|standardní| 20 000 000 |
|nárok| bez omezení |

Podrobné informace o tomto omezení najdete v kapitole [velikost serveru](../reference/vm-sizes.md) .

## <a name="platform-limitations"></a>Omezení platformy

**Stolní počítač s Windows 10**

* Jedinou podporovanou platformou Win32 je Win32/x64. Win32/x86 se nepodporuje.

**HoloLens 2**

* Funkce [vykreslování z PV fotoaparátu](/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) není podporována.