---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006492"
---
## <a name="setting-up-the-library"></a>Nastavení knihovny

Vyvolá `Start()` se, pokud chcete, aby vaše relace mohla zpracovávat data prostředí.

Chcete-li zpracovávat události vyvolané vaší relací, nastavte `delegate` vlastnost relace na objekt, jako je například zobrazení. Tento objekt musí implementovat `SSCCloudSpatialAnchorSessionDelegate` protokol.
