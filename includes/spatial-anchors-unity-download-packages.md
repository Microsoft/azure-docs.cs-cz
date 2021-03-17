---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 2/3/21
ms.author: parkerra
ms.openlocfilehash: f6c2780ccbb914228a9870cb3b5fe4b0e3d0b214
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99569546"
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

Do složky, ve které jste spustili příkaz, se stáhne balíček prostorových kotev Azure.

Zopakováním tohoto kroku Stáhněte balíček pro každou platformu (Android/iOS/HoloLens), kterou chcete v projektu podporovat.

| Platforma | Název balíčku                                    |
|----------|-------------------------------------------------|
| Android  | rozhraní com. Microsoft. Azure. prostor-kotvy – sdk.android@ <version_number> |
| iOS      | rozhraní com. Microsoft. Azure. prostor-kotvy – sdk.ios@ <version_number>     |
| HoloLens | rozhraní com. Microsoft. Azure. prostor-kotvy – sdk.windows@ <version_number> |