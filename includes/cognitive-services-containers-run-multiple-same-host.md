---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 09aa5affefc576606984ea7116b3d9bda4ba83d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67704257"
---
### <a name="run-multiple-containers-on-the-same-host"></a>Spuštění více kontejnerů na stejném hostiteli

Pokud máte v úmyslu spustit více kontejnerů s vystavené porty, ujistěte se, že spustit každý kontejner s jiným vystavený port. Například spusťte první kontejner na portu 5000 a druhý kontejner na portu 5001.

Tento kontejner a jiný kontejner Azure Cognitive Services můžete mít spuštěné na HOST společně. Můžete mít také více kontejnerů stejného kontejneru služby Cognitive Services spuštěna.
