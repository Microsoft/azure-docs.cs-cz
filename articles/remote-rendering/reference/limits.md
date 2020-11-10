---
title: Omezení
description: Omezení kódu pro funkce sady SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 33f5314c80dc33dbec50dc21a71f4cb507979e12
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427424"
---
# <a name="limitations"></a>Omezení

Množství funkcí má velikost, počet nebo jiná omezení.

## <a name="azure-frontend"></a>Azure front-end

Následující omezení se vztahují na front-endové rozhraní API (C++ a C#):
* Celkový počet instancí [AzureFrontend](/dotnet/api/microsoft.azure.remoterendering.azurefrontend) na proces: 16.
* Celkový počet instancí [AzureSession](/dotnet/api/microsoft.azure.remoterendering.azuresession) na [AzureFrontend](/dotnet/api/microsoft.azure.remoterendering.azurefrontend): 16.

## <a name="objects"></a>Objekty

* Celkem přípustných objektů jednoho typu ([entita](../concepts/entities.md), [CutPlaneComponent](../overview/features/cut-planes.md)atd.): 16 777 215.
* Celkový povolený počet aktivních vyjmutých ploch: 8.

## <a name="geometry"></a>Geometrie

* **Animace:** Animace jsou omezené na animace jednotlivých transformací [herních objektů](../concepts/entities.md). Animace základní s animacemi s použitím změny vzhledu a vrcholu se nepodporují. Animační běhy ze zdrojového souboru prostředků se nezachovají. Místo toho musí být animace transformace objektů řízeny klientským kódem.
* **Vlastní shadery:** Vytváření vlastních shaderů se nepodporuje. Je možné použít pouze předdefinované [barevné materiály](../overview/features/color-materials.md) nebo [materiály PBR](../overview/features/pbr-materials.md) .
* **Maximální počet různých materiálů** v prostředku: 65 535. Další informace o automatickém snížení počtu materiálu najdete v kapitole [de-duplikace materiálu](../how-tos/conversion/configure-model-conversion.md#material-de-duplication) .
* **Maximální rozměr jedné textury** : 16 384 × 16 384. V rámci procesu převodu se velikost větších textur od velkých zdrojů zmenší.

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