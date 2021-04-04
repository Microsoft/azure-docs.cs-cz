---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 09aa5affefc576606984ea7116b3d9bda4ba83d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96000537"
---
### <a name="run-multiple-containers-on-the-same-host"></a>Spuštění více kontejnerů na stejném hostiteli

Pokud máte v úmyslu spustit více kontejnerů s vystavenými porty, ujistěte se, že každý kontejner spustíte s jiným vystaveným portem. Například spusťte první kontejner na portu 5000 a druhý kontejner na portu 5001.

Můžete mít tento kontejner a jiný kontejner Azure Cognitive Services běžící na hostiteli společně. Můžete mít také více kontejnerů stejného Cognitive Services kontejneru se systémem.
