---
title: Proces uzavírání Cognitive Services
titleSuffix: Azure Cognitive Services
description: Naučte se, jak použít pro počáteční přístup k novým kontejnerům Cognitive Services a rozhraním API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: a161cc0675eedee50608b8d6c288c57147fd6d23
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599510"
---
# <a name="gating-process-for-azure-cognitive-services"></a>Proces uzavírání pro Azure Cognitive Services

> [!NOTE]
> Jakmile nabídka služeb dokončí ověřovaný náhled, přejde do nepoužívané verze Public Preview, která nevyžaduje aplikaci pro přístup. Po dokončení procesu Preview se tato verze uvolní jako obecně dostupná (GA).

Po zavedení nových nabídek Azure Cognitive Services procházejí přes službu gated Preview, kde si zákazníci můžou vyžádat přístup prostřednictvím aplikace. Tento proces uzavírání pomáhá identifikovat příležitosti pro vylepšení nabídek služeb předtím, než budou široce dostupné. 

Tento článek vás provede procesem aplikace pro ověřované Cognitive Services nabídky.

## <a name="eligibility-and-approval-process"></a>Způsobilost a proces schvalování

Proces uzavírání je zaveden, aby bylo možné porozumět zájmu a lépe porozumět potřebám zákazníků. Tým Microsoftu přijímá [aplikace](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u) od komerčních zákazníků Microsoftu pomocí platného předplatného Azure a platného podnikového scénáře. Zákazníci mohou mít odepřené aplikace v těchto případech:

 - Nejsou přidruženy k žádné organizaci.
 - Nemají platné předplatné Azure.
 - Aplikace byla odeslána prostřednictvím osobního e-mailu ( @hotmail.com , @gmail.com , @yahoo.com ).
 - Nebyl poskytnut žádný řádný odůvodněný nebo podnikový scénář.

S ohledem na poptávku z různých zákaznických segmentů se snažíme urychlit proces schvalování. Na časovou osu ale nemůžeme potvrdit. Po provedení rozhodnutí vás tým Microsoftu spojí s vaším týmem správy účtů a provede vás další kroky. Vážíme si vašeho porozumění a trpělivosti.

Pokud je aplikace schválená, pošle tým Microsoftu e-mail s podrobnostmi, dokumentací a pokyny. [Zde](https://azure.microsoft.com/pricing/details/cognitive-services/)jsou uvedeny podrobnosti o cenách Cognitive Services.


V současné době jsou níže uvedené služby nabízeny prostřednictvím procesu uzavírání:

## <a name="gated-web-apis"></a>Ověřovaná webová rozhraní API

|Služba  |
|---------|
|Detektor anomálií v2     | 

## <a name="gated-online-containers"></a>Ověřované online kontejnery

| Služba                             | Kontejneru (y)                                                                  |
|-------------------------------------|-------------------------------------------------------------------------------|
| [Počítačové zpracování obrazu][cv-containers]    | Čtení                                                                          |
| [Rozpoznávání tváře][fa-containers]               | Rozpoznávání tváře                                                                          |
| [Rozpoznávání formulářů][fr-containers]    | Rozpoznávání formulářů                                                               |
| [Rozhraní API služby Speech][sp-containers] | Převod řeči na text (vlastní a standardní) a převod textu na řeč (vlastní a standardní) |
| [Translator Text][tt-containers]    | Translator Text                                                               |

> [!IMPORTANT]
> Pokud služba nebo kontejnerová nabídka není v seznamu uvedena, není buď ověřovaná, nebo není k dispozici.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Registrace k gated Services](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)

[ad-containers]: ./anomaly-detector/anomaly-detector-container-howto.md
[cv-containers]: ./computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ./face/face-how-to-install-containers.md
[fr-containers]: ./form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ./luis/luis-container-howto.md
[sp-containers]: ./speech-service/speech-container-howto.md
[ta-containers]: ./text-analytics/how-tos/text-analytics-how-to-install-containers.md
[tt-containers]: ./translator/how-to-install-containers.md
