---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006492"
---
## <a name="setting-up-the-library"></a>Nastavení knihovny

Vyvolá `Start()` se, pokud chcete, aby vaše relace mohla zpracovávat data prostředí.

Chcete-li zpracovávat události vyvolané vaší relací, `delegate` nastavte vlastnost relace na objekt, jako je například zobrazení. Tento objekt musí implementovat `SSCCloudSpatialAnchorSessionDelegate` protokol.
