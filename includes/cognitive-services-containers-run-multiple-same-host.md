---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 09aa5affefc576606984ea7116b3d9bda4ba83d5
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704257"
---
### <a name="run-multiple-containers-on-the-same-host"></a>Spouštění více kontejnerů na stejném hostiteli

Pokud máte v úmyslu spustit několik kontejnerů s vystavené porty, ujistěte se, že ke spuštění každý kontejner s jiným portem vystavené. Například spusťte první kontejner na portu 5000 a druhý kontejner na portu 5001.

Může mít tento kontejner a kontejner různých služeb Azure Cognitive Services běží na HOSTITELI společně. Také může mít několik kontejnerů stejného kontejneru služeb Cognitive Services.
