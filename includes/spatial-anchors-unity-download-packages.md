---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/30/2021
ms.author: parkerra
ms.openlocfilehash: 7faab3340483d99fa276de06f3fd7787457edb9e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076687"
---
Dalším krokem je stažení balíčků prostorových kotev Azure pro Unity. 

> [!WARNING]
> ASA SDK 2.7.0 je minimální podporovaná verze. Pokud používáte Unity 2020, je minimální podporovaná verze aplikace ASA SDK 2.9.0.

Pokud chcete používat prostorové kotvy Azure v Unity, budete si muset stáhnout základní balíček i balíček specifický pro platformu pro každou platformu (Android/iOS/HoloLens), kterou hodláte podporovat.

| Platforma | Název balíčku                                    |
|----------|-------------------------------------------------|
| Android/iOS/HoloLens  | rozhraní com. Microsoft. Azure. prostor-kotvy – sdk.core@ <version_number> |
| Android  | rozhraní com. Microsoft. Azure. prostor-kotvy – sdk.android@ <version_number> |
| iOS      | rozhraní com. Microsoft. Azure. prostor-kotvy – sdk.ios@ <version_number>     |
| HoloLens | rozhraní com. Microsoft. Azure. prostor-kotvy – sdk.windows@ <version_number> |

# <a name="download-with-web-browser"></a>[Stáhnout pomocí webového prohlížeče](#tab/unity-package-web-ui)

Vyhledejte v balíčku základní kotvy prostorových kotev Azure [pro Unity.](https://aka.ms/aoa/unity-sdk/package) Vyberte verzi, kterou chcete stáhnout, a Stáhněte balíček pomocí tlačítka pro **stažení** . Zopakováním tohoto kroku Stáhněte balíček pro každou platformu, kterou hodláte podporovat.

# <a name="download-with-npm"></a>[Stáhnout pomocí NPM](#tab/unity-package-npm)

Tento krok vyžaduje, aby byl nainstalovaný a dostupný <a href="https://www.npmjs.com/get-npm" target="_blank">npm</a> .

Spusťte následující příkaz nahrazující `<version_number>` verzi prostorových kotev Azure, které chcete stáhnout do aktuální složky:

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> Chcete-li zobrazit seznam dostupných verzí balíčku prostorových kotev Azure, spusťte následující příkaz:
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

Do složky, ve které jste spustili příkaz, se stáhne balíček prostorových kotev Azure. Zopakováním tohoto kroku Stáhněte balíček pro každou platformu, kterou hodláte podporovat.

# <a name="install-with-mixed-reality-feature-tool-beta"></a>[Instalace nástroje s funkcí Mixed reality (beta verze)](#tab/unity-package-mixed-reality-feature-tool)

Pokračujte k dalšímu kroku. V pozdějším kroku použijete <a a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">Nástroj funkce Mixed reality</a> .

---