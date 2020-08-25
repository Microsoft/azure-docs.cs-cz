---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "83006492"
---
## <a name="setting-up-the-library"></a>Nastavení knihovny

Vyvolá `Start()` se, pokud chcete, aby vaše relace mohla zpracovávat data prostředí.

Chcete-li zpracovávat události vyvolané vaší relací, nastavte `delegate` vlastnost relace na objekt, jako je například zobrazení. Tento objekt musí implementovat `SSCCloudSpatialAnchorSessionDelegate` protokol.
