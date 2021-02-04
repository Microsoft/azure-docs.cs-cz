---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 2/3/21
ms.author: parkerra
ms.openlocfilehash: c97067c66296e8980a36b21298a7b2061e0f9b4c
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550374"
---
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