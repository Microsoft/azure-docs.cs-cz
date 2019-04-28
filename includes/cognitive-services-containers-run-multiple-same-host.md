---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 02/21/2019
ms.openlocfilehash: e80feac7dbf16652cc2e2a6176ed8b2c8c48e35b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598904"
---
### <a name="running-multiple-containers-on-the-same-host"></a>Spouštění více kontejnerů na stejném hostiteli

Pokud máte v úmyslu spustit několik kontejnerů s vystavené porty, ujistěte se, že ke spuštění každý kontejner s jiným portem. Například spusťte první kontejner na portu 5000 a druhý kontejner na portu 5001.

Nahradit `<container-registry>` a `<container-name>` hodnotami kontejnerů použijete. Toto nemusí být stejný kontejner. Máte kontejner pro rozpoznávání tváře a LUIS kontejner společně běží na HOSTITELI nebo může mít několik kontejnerů pro rozpoznávání tváře systémem. 

Spuštění prvního kontejneru na portu 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Spusťte na portu 5001 druhý kontejner.


```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Každé následné kontejner by měl být na jiném portu. 
