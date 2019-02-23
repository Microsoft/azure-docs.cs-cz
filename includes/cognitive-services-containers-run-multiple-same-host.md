---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 02/21/2019
ms.openlocfilehash: 820ea4c401d560d4cf1c937d2efd7d7bde579a91
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2019
ms.locfileid: "56675734"
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
docker run --rm -it -p 5001:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Každé následné kontejner by měl být na jiném portu. 