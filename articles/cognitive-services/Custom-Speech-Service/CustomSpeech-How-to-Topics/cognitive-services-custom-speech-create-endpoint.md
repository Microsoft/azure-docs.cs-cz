---
title: Vytvořte vlastní řeči koncový bod s vlastní službou řeči v Azure | Microsoft Docs
description: Naučte se vytvořit koncový bod vlastní řeči na text službou vlastní řeči v kognitivní služby.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/08/2017
ms.author: panosper
ms.openlocfilehash: 99bc275db1f0c1b45b3db440d2e03d0db9ab5cf6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342448"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>Vytvořte vlastní koncový bod řeči na text
Jakmile vytvoříte vlastní akustickými modely nebo jazyk modely, kterou můžete nasadit v koncový bod vlastní řeči na text. 

## <a name="create-an-endpoint"></a>Vytvoření koncového bodu
Chcete-li vytvořit nové vlastní koncový bod, vyberte **nasazení** na **vlastní řeči** nabídce v horní části stránky. Tato akce trvá, abyste **nasazení** stránky, který obsahuje tabulku aktuální vlastní koncové body. Pokud jste ještě nevytvořili žádné koncové body, je tabulka prázdná. Aktuální národní prostředí se projeví v názvu tabulky. 

Chcete-li vytvořit nasazení pro jiný jazyk, vyberte **změnu národního prostředí**. Další informace o podporovaných jazyků najdete v tématu [podporované národní prostředí ve službě vlastní řeči](cognitive-services-custom-speech-change-locale.md).

Chcete-li vytvořit nový koncový bod, vyberte **vytvořit nový**. V **vytvořit nasazení** podokně, zadejte informace do **název** a **popis** polí vlastní nasazení.

V **předplatné** pole se seznamem, vyberte odběr, který chcete použít. Pokud je předplatné S2, můžete vybrat jednotek škálování a obsahu protokolování. Další informace o protokolování a jednotek škálování najdete v tématu [vlastní řeči služby měřidla a kvóty](../cognitive-services-custom-speech-meters.md).

Následující tabulka ukazuje, jak mapování jednotek škálování na k dispozici souběžných požadavků:

| Jednotky škálování | Počet souběžných požadavků |
| ------ | ----- |
| 0 | 1 |
| 1 | 5 |
| 2 | 10 |
| 3 | 15 |
| n | 5 * n |

Můžete také vybrat, zda obsahu protokolování je přepnuta zapnout nebo vypnout. Vybíráte to znamená, zda je uložený provoz koncový bod pro interní použití společností Microsoft. Pokud není zaškrtnuto, ukládání přenosy dat budou potlačeny. Potlačení obsahu protokolování má za následek další náklady. Obrátit [informace o stránce s cenami](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/) podrobnosti.

> [!NOTE]
> Obsahu protokolování se nazývá "Ne Trace" na stránce s cenami.
>


Kromě toho společnost Microsoft poskytuje odhad náklady tak, že jste si vědomi dopad na náklady na jednotek škálování a obsahu protokolování. Tento odhad je odhad a může lišit od skutečné náklady.

> [!NOTE]
> Tato nastavení nejsou k dispozici s odběry F0 (úroveň free).
>

V **akustickými modelu** vyberte akustickými model, který chcete, a v **jazykový Model** vyberte model jazyk, který chcete. Možnosti pro modely acoustic a jazyk, vždy zahrňte základní modely společnosti Microsoft. Výběr základní modelu omezuje kombinace. Nelze kombinovat s search konverzačního základní modely a stanovují základní modelů.

![Stránka vytvořit nasazení](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-create2.png)

> [!NOTE]
> Je nutné přijmout podmínky použití a informace o cenách tak, že vyberete zaškrtnutím políčka.
>

Po výběru modely acoustic a jazyk, vyberte **vytvořit**. Tato akce vrátí, abyste **nasazení** stránky. Nyní zahrnuje položku, která odpovídá vaší nový koncový bod. Stav pro koncový bod odráží aktuální stav je při vytváření. To může trvat až 30 minut pro vytvoření instance nového koncového bodu se vaše vlastní modely. Pokud je stav nasazení *Complete*, koncový bod je připraven k použití.

![Stránka nasazení](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-ready.png)

Při nasazení je připraven, název nasazení se změní na odkaz. Výběr odkaz zobrazí **informace o nasazení** stránku, která zobrazuje svůj vlastní koncový bod adresy URL pro použití s buď požadavku HTTP nebo Microsoft kognitivní služby řeči klientské knihovny, který používá webové sokety.

![Stránka informace o nasazení](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-info2.png)

## <a name="next-steps"></a>Další postup

Další kurzy najdete v tématu:
* [Použít vlastní koncový bod řeči na text](cognitive-services-custom-speech-use-endpoint.md)
* [Vytvoření vlastní akustickými modelu](cognitive-services-custom-speech-create-acoustic-model.md)
* [Vytvoření vlastní jazyk modelu](cognitive-services-custom-speech-create-language-model.md)
