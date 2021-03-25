---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 02/18/2021
ms.author: crtreasu
ms.openlocfilehash: ada83d6263ef033208200d810c53c5f045acc9a7
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105737"
---
Dalším krokem je stažení balíčku kotev objektů Azure pro Unity.

# <a name="download-with-web-browser"></a>[Stáhnout pomocí webového prohlížeče](#tab/unity-package-web-ui)

[Tady](https://aka.ms/aoa/unity-sdk/package)vyhledejte balíček kotev objektů Azure pro Unity. Vyberte verzi, kterou chcete stáhnout, a Stáhněte balíček pomocí tlačítka pro **stažení** .

# <a name="download-with-npm"></a>[Stáhnout pomocí NPM](#tab/unity-package-npm)

Tento krok vyžaduje, aby byl nainstalovaný a dostupný <a href="https://www.npmjs.com/get-npm" target="_blank">npm</a> .

Spusťte následující příkaz nahrazující `<version_number>` verzi kotev objektů Azure, které chcete stáhnout:

```bash
npm pack com.microsoft.azure.object-anchors.runtime@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> Pokud chcete zobrazit seznam dostupných verzí balíčku kotev objektů Azure, spusťte následující příkaz:
>
> ```bash
> npm view com.microsoft.azure.object-anchors.runtime --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

Balíček kotev objektů Azure se stáhne do složky, ve které jste příkaz spustili.

# <a name="install-with-mixed-reality-feature-tool-beta"></a>[Instalace nástroje s funkcí Mixed reality (beta verze)](#tab/unity-package-mixed-reality-feature-tool)

Pokračujte k dalšímu kroku. V pozdějším kroku použijete <a a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">Nástroj funkce Mixed reality</a> .

---