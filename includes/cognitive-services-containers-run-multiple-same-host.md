---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 327654ca06a3997855d904414fa4258491ee6c88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66124367"
---
### <a name="run-multiple-containers-on-the-same-host"></a>Spouštění více kontejnerů na stejném hostiteli

Pokud máte v úmyslu spustit několik kontejnerů s vystavené porty, ujistěte se, že ke spuštění každý kontejner s jiným portem vystavené. Například spusťte první kontejner na portu 5000 a druhý kontejner na portu 5001.

Může mít tento kontejner a kontejner různých služeb Azure Cognitive Services běží na HOSTITELI společně. Také může mít několik kontejnerů stejného kontejneru služeb Cognitive Services.
