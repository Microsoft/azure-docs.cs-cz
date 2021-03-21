---
title: Ladění chyb při vytváření vlastní aplikace příkazů (Preview)
titleSuffix: Azure Cognitive Services
description: V tomto článku se naučíte, jak ladit chyby při vytváření aplikací pro vlastní příkazy.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: aeb90e8e064c44f4d17f920261ed58310f0e55f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95025697"
---
# <a name="debug-errors-when-authoring-a-custom-commands-application"></a>Ladění chyb při vytváření vlastní aplikace příkazů

Tento článek popisuje, jak ladit při zobrazení chyb při sestavování aplikace s vlastními příkazy. 

## <a name="errors-when-creating-an-application"></a>Chyby při vytváření aplikace
Vlastní příkazy také při vytváření vlastní aplikace pomocí příkazu vytvoří v [Luis](https://www.luis.ai/) aplikaci. 

[Luis omezuje počet aplikací na prostředek pro vytváření obsahu 500](../luis/luis-limits.md). Vytvoření aplikace LUIS může selhat, pokud používáte prostředek pro vytváření obsahu, který už má aplikace 500. 

Ujistěte se, že vybraný prostředek pro vytváření obsahu LUIS má méně než 500 aplikací. V takovém případě můžete vytvořit nový prostředek pro vytváření obsahu LUIS, přepnout na jiný nebo se pokusit vyčistit aplikace LUIS.  

## <a name="errors-when-deleting-an-application"></a>Chyby při odstraňování aplikace
### <a name="cant-delete-luis-application"></a>Nejde odstranit aplikaci LUIS.
Při odstraňování vlastní aplikace příkazů se můžou vlastní příkazy také pokusit odstranit aplikaci LUIS přidruženou k aplikaci Custom Commands.

Pokud se nepovedlo odstranit aplikaci LUIS, otevřete ji ručně tak, že přejdete na svůj účet [Luis](https://www.luis.ai/) .

### <a name="toomanyrequests"></a>TooManyRequests
Když se pokusíte odstranit velký počet aplikací najednou, pravděpodobně se zobrazí chyby "TooManyRequests". Tyto chyby znamenají, že Azure Získá omezení požadavků na odstranění. 

Aktualizujte svou stránku a pokuste se odstranit méně aplikací.

## <a name="errors-when-modifying-an-application"></a>Chyby při úpravě aplikace

### <a name="cant-delete-a-parameter-or-a-web-endpoint"></a>Nejde odstranit parametr nebo webový koncový bod.
Při použití nemůžete odstranit parametr. Odeberte všechny odkazy na parametr v jakékoli odpovědi na řeč, ukázkové věty, podmínky a akce a zkuste to znovu.

### <a name="cant-delete-a-web-endpoint"></a>Nejde odstranit webový koncový bod.
Při použití nemůžete odstranit webový koncový bod. Před odebráním webového koncového bodu odeberte akci pro **webový koncový bod volání** , která používá tento koncový bod webu.

## <a name="errors-when-training-an-application"></a>Chyby při školení aplikace
### <a name="built-in-intents"></a>Built-In záměry
LUIS má integrované záměry ano/ne. V případě ukázkových vět, které mají pouze "Ano", "ne" by školení nevyhovělo. 

| Klíčové slovo | Odchylky | 
| ------- | --------- | 
| Yes | Ujistěte se, OK |
| No | Ne, ne | 

### <a name="common-sample-sentences"></a>Společné ukázkové věty
Vlastní příkazy neumožňují společné ukázkové věty sdílené mezi různými příkazy. Školení aplikace může selhat, pokud jsou některé ukázkové věty v jednom příkazu již definovány v jiném příkazu. 

Ujistěte se, že nemáte společné ukázkové věty sdílené mezi různými příkazy. 

Osvědčené postupy při vyrovnávání vzorových vět v různých příkazech najdete v tématu [osvědčené postupy pro Luis](../luis/luis-concept-best-practices.md).

### <a name="empty-sample-sentences"></a>Prázdné ukázkové věty
Pro každý příkaz musíte mít aspoň jednu ukázkovou větu.

### <a name="undefined-parameter-in-sample-sentences"></a>Nedefinovaný parametr v ukázkových větách
V vzorových větách se používá minimálně jeden parametr, ale není definovaný.

### <a name="training-takes-too-long"></a>Školení trvá moc dlouho.
Školení LUIS je určeno pro rychlé seznámení s méně příklady. Nepřidávejte příliš mnoho ukázkových vět. 

Pokud máte mnoho vzorových vět, které jsou podobné, definujte parametr, vyabstrakcte je do vzoru a přidejte ho k příkladům vět.

Například můžete definovat parametr {vehikul} pro níže uvedené příklady vět a přidat k příkladům pouze "knihu a {vehikulum}".

| Příklady vět | Vzor | 
| ------- | ------- | 
| Kniha a auto | Kniha a {vehikulum} | 
| Kniha a letu | Kniha a {vehikulum} |
| Kniha a taxislužby | Kniha a {vehikulum} |

Osvědčeným postupem pro LUIS školení najdete v [Luis osvědčené postupy](../luis/luis-concept-best-practices.md).

## <a name="cant-update-luis-key"></a>Nejde aktualizovat klíč LUIS.
### <a name="reassign-to-e0-authoring-resource"></a>Znovu přiřadit k E0 prostředku pro vytváření obsahu
LUIS nepodporuje změnu přiřazení aplikace LUIS k E0 vytváření prostředků.

Pokud potřebujete změnit prostředek pro vytváření obsahu z F0 na E0 nebo změnit na jiný prostředek E0, znovu vytvořte aplikaci. 

Pokud chcete rychle exportovat existující aplikaci a importovat ji do nové aplikace, přečtěte si téma [průběžné nasazování pomocí Azure DevOps](./how-to-custom-commands-deploy-cicd.md).

### <a name="save-button-is-disabled"></a>Tlačítko Uložit je zakázané.
Pokud do vaší aplikace nikdy nepřiřadíte prostředek předpovědi LUIS, tlačítko Uložit by se při pokusu o změnu prostředku pro vytváření obsahu bez přidání předpovědi neaktivovalo.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zobrazit ukázky na GitHubu](https://aka.ms/speech/cc-samples)