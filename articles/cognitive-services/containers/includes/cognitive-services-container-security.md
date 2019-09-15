---
title: Zabezpečení kontejneru
titleSuffix: Azure Cognitive Services
description: Naučte se zabezpečit svůj kontejner.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: dapine
ms.openlocfilehash: d8d069dddbce6ab6ddb541db460634ad3f6fa067
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2019
ms.locfileid: "70994954"
---
## <a name="azure-cognitive-services-container-security"></a>Zabezpečení kontejneru Azure Cognitive Services

Zabezpečení by mělo být primárním fokusem při vývoji aplikací. Důležitosti zabezpečení je metrika pro úspěch. Při navrhování softwarového řešení, které zahrnuje Cognitive Services kontejnery, je důležité pochopit omezení a možnosti, které máte k dispozici. Další informace najdete v tématu věnovaném [zabezpečení Azure][az-security].

> [!IMPORTANT]
> Ve výchozím nastavení neexistuje *žádné zabezpečení* na Cognitive Services rozhraní API kontejneru. Důvodem je, že nejčastěji se kontejner spouští jako součást pod, která je chráněná proti síťovému mostu zvenčí. Je ale možné povolit ověřování, které funguje stejně jako ověřování používané při přístupu k [cloudovým Cognitive Services][request-authentication].

Následující diagram znázorňuje výchozí a **nezabezpečený** přístup:

![Zabezpečení kontejneru](../media/container-security.svg)

Jako alternativní a *bezpečný* přístup by spotřebitelé Cognitive Services kontejnerů mohli rozšířit kontejner s front-orientované komponentou a zachovat tak koncový bod kontejneru privátní. Pojďme se považovat za scénář, ve kterém používáme [Istio][istio] jako bránu příchozího přenosu dat. Istio podporuje protokol HTTPS/SSL a ověřování klientů a certifikátů. V tomto scénáři zveřejňuje Istio front-endu přístup k kontejneru a prezentuje klientský certifikát, který je na seznamu povolený předem s Istio.

[Nginx][nginx] je další oblíbená volba ve stejné kategorii. Istio i Nginx fungují jako síť služby a nabízejí další funkce, jako například vyrovnávání zatížení, směrování a řízení sazeb.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../../security/fundamentals/overview.md
