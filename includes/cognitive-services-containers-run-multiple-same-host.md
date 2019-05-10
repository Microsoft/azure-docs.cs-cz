---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 19726330561abfad08aec3c4908c855616c6ee29
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2019
ms.locfileid: "65520903"
---
### <a name="running-multiple-containers-on-the-same-host"></a>Spouštění více kontejnerů na stejném hostiteli

Pokud máte v úmyslu spustit několik kontejnerů s vystavené porty, ujistěte se, že ke spuštění každý kontejner s jiným portem vystavené. Například spusťte první kontejner na portu 5000 a druhý kontejner na portu 5001.

Můžete mít tento kontejner a kontejner různé služby Cognitive Services spuštěných na HOSTITELI, společně nebo může mít několik kontejnerů stejného kontejneru služby Cognitive Services.
