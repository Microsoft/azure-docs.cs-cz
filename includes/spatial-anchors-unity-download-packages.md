---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/18/2021
ms.author: parkerra
ms.openlocfilehash: d91c0aeda2b7ae2f133d2099cbc9d238fd19d287
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104719736"
---
Chcete-li stáhnout požadované balíčky, budete muset <a href="https://www.npmjs.com/get-npm" target="_blank">npm</a> nainstalovat.

Spusťte následující příkaz nahrazující `<version_number>` verzi prostorových kotev Azure, které chcete stáhnout do aktuální složky:

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM
```

> [!NOTE]
> Chcete-li zobrazit seznam dostupných verzí balíčku prostorových kotev Azure, spusťte následující příkaz:
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM versions
> ```

> [!WARNING]
> ASA SDK 2.7.0 je minimální podporovaná verze. Pokud používáte Unity 2020, je minimální podporovaná verze aplikace ASA SDK 2.9.0.

Do složky, ve které jste spustili příkaz, se stáhne balíček prostorových kotev Azure.

Zopakováním tohoto kroku Stáhněte balíček pro každou platformu (Android/iOS/HoloLens), kterou chcete v projektu podporovat.

| Platforma | Název balíčku                                    |
|----------|-------------------------------------------------|
| Android  | rozhraní com. Microsoft. Azure. prostor-kotvy – sdk.android@ <version_number> |
| iOS      | rozhraní com. Microsoft. Azure. prostor-kotvy – sdk.ios@ <version_number>     |
| HoloLens | rozhraní com. Microsoft. Azure. prostor-kotvy – sdk.windows@ <version_number> |