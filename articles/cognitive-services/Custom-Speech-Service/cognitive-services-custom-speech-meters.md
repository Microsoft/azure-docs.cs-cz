---
title: Vlastní rozpoznávání řeči služby měřidla a kvót v Azure | Microsoft Docs
description: Informace o měřidla a kvóty vlastní řeči služby v Azure.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/08/2017
ms.author: panosper
ms.openlocfilehash: d2225dec818c600febfad2f9ebc42594f6ac09ac
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342394"
---
# <a name="custom-speech-service-meters-and-quotas"></a>Vlastní rozpoznávání řeči služby měřidla a kvóty

S cloudovou službou řeči vlastní můžete přizpůsobit rozpoznávání řeči modely pro přepis řeči na text.

Chcete-li začít používat službu vlastní rozpoznávání řeči, přejděte na [portálu vlastní řeči](https://cris.ai).

Pro aktuální ceny měřidla, přejděte na [kognitivní služby ceny služby řeči vlastní](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/) stránky.

## <a name="tiers-explained"></a>Vysvětlení vrstev
Pro účely testování a prototypu pouze navrhujeme použít úroveň free F0. Pro produkční systémy navrhujeme použití S2 vrstvy. Pomocí vrstvě S2 můžete škálovat počet jednotek škálování (SUs), které váš scénář vyžaduje vaše nasazení.

> [!NOTE]
> Můžete *nelze* migrace mezi F0 vrstvou a vrstvou S2.
>

## <a name="meters-explained"></a>Vysvětlení měřidla

### <a name="scale-out"></a>Škálování na více systémů
Horizontální navýšení kapacity je nová funkce, která vydala s nový cenový model. Pomocí horizontální navýšení kapacity, můžete řídit počet souběžných požadavků, které může zpracovat váš model.

Souběžných požadavků lze nastavit pomocí příkazu SU míry v **vytvořit Model nasazení** zobrazení. Další informace najdete v tématu [vytvořit vlastní koncový bod řeči na text](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md). V závislosti na množství provozu, který počítat využívání modelu můžete vybrat odpovídající počet služby SUs. 

> [!NOTE]
> Jednotlivé jednotky škálování zaručuje 5 souběžných požadavků. Můžete si koupit 1 nebo více služby SUs podle potřeby. Vzhledem k tomu, že počet SUs zvyšuje v přírůstcích po 1, počet souběžných požadavků, které je zaručeno zvýšit v přírůstcích po 5.
>

### <a name="log-management"></a>Správa protokolu
Můžete se rozhodnout vypnout zvuk trasování pro nově nasazené model v dalších nákladů. Vlastní rozpoznávání řeči služba neprotokoluje zvuk žádosti nebo přepisy z tohoto modelu.

## <a name="next-steps"></a>Další postup
Další informace o tom, jak používat službu vlastní rozpoznávání řeči, přejděte na [portálu vlastní řeči](https://cris.ai).

* [Začínáme](cognitive-services-custom-speech-get-started.md)
* [Nejčastější dotazy](cognitive-services-custom-speech-faq.md)
* [Glosář](cognitive-services-custom-speech-glossary.md)
 