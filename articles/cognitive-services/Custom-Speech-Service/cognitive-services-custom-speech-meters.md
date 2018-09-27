---
title: Měřiče služby a kvóty – Custom Speech
titlesuffix: Azure Cognitive Services
description: Informace o měření a kvót služby Custom Speech Service v Azure.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: conceptual
ms.date: 07/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 68fba1d6038a044336ba6aecd3ff163d29131b1d
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224694"
---
# <a name="custom-speech-service-meters-and-quotas"></a>Vlastní měření Speech Service a kvóty

Pomocí cloudové služby Custom Speech Service můžete upravit modely řeči pro určené k transkripci řeči na text.

Chcete-li začít používat službu Custom Speech Service, přejděte [portál Custom Speech Service](https://cris.ai).

Pro aktuální ceny měřičů, přejděte [ceny služeb Cognitive Services Custom Speech Service](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/) stránky.

## <a name="tiers-explained"></a>Vysvětlení úrovně
Pro účely testování a prototypu pouze navrhujeme F0 úroveň free můžete použít. Pro produkční systémy navrhujeme používat úroveň S2. Pomocí na úrovni S2 můžete škálovat nasazení tak, aby počet jednotek škálování (su), které vyžaduje váš scénář.

> [!NOTE]
> Můžete *nelze* migrace mezi F0 úroveň a úroveň S2.
>

## <a name="meters-explained"></a>Vysvětlení měřiče

### <a name="scale-out"></a>Škálování na více systémů
Horizontální navýšení kapacity je nová funkce, které jsme vydali s nový cenový model. S použitím horizontální navýšení kapacity, můžete řídit počet souběžných požadavků, které může zpracovat váš model.

Souběžných požadavků můžete nastavit pomocí SU míra v **vytvořit nasazení modelu** zobrazení. Další informace najdete v tématu [vytvoření vlastního koncového bodu převodu řeči na text](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md). V závislosti na množství dat, která vám představit využívání modelu můžete zvolit odpovídající počet su. 

> [!NOTE]
> Každá jednotka škálování garantuje 5 souběžných požadavků. Můžete si koupit 1 nebo více su, podle potřeby. Vzhledem k tomu, že počet SUs zvyšuje o 1, počet souběžných požadavků je zaručeno zvyšují v přírůstcích po 5.
>

### <a name="log-management"></a>Správa protokolů
Můžete se rozhodnout vypnout zvukové stopy nově nasazeného modelu za další poplatek. Custom Speech Service neprotokoluje požadavků na zpracování zvuku nebo záznamy o studiu z tohoto modelu.

## <a name="next-steps"></a>Další postup
Další informace o tom, jak pomocí služby Custom Speech Service, přejděte [portál Custom Speech Service](https://cris.ai).

* [Začínáme](cognitive-services-custom-speech-get-started.md)
* [Nejčastější dotazy](cognitive-services-custom-speech-faq.md)
* [Glosář](cognitive-services-custom-speech-glossary.md)
 