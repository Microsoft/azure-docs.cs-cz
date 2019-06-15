---
title: Skóre Reward – Personalizer
titleSuffix: Azure Cognitive Services
description: Potřebu skóre označuje, jak dobře přizpůsobení podle vlastní volby, RewardActionID, výsledkem je pro daného uživatele. Hodnota reward skóre je určeno obchodní logiky a podle pozorování chování uživatelů. Personalizer trénovat jeho modelů strojového učení s vyhodnocením výhody.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 06/07/2019
ms.author: edjez
ms.openlocfilehash: c64d43301fd173203bd1625b8d37120b71c22805
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077403"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>Potřebu skóre znamená úspěch individuálního nastavení

Potřebu skóre označuje, jak dobře přizpůsobení podle vlastní volby, [RewardActionID](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/rank/rank#response), byl pro uživatele. Hodnota reward skóre je určeno obchodní logiky a podle pozorování chování uživatelů.

Personalizer trénovat jeho modelů strojového učení s vyhodnocením výhody. 

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Použít účet rozhraní API k odeslání skóre reward Personalizer

Odměny se odesílají do Personalizer podle [Reward API](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/events/reward). Odměny je číslo od -1 a 1. Personalizer trénovat modelu k dosažení nejvyšší součet odměny v čase.

Odměny jsou odeslány po chování uživatelů se stalo, které by mohly být dnů později. Maximální množství času Personalizer počká, až událost je považována za mít žádná odměna nebo výchozí účet je nakonfigurovaný s [Reward čekací doby](#reward-wait-time) na webu Azure Portal.

Pokud nebyl přijat reward skóre pro události v rámci **Reward čekací doby**, pak bude **výchozí Reward** se použijí. Obvykle **[výchozí Reward](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** byl nakonfigurován jako nula.


## <a name="behaviors-and-data-to-consider-for-rewards"></a>Chování a dat, které je potřeba zvážit odměny

Vezměte v úvahu tyto signály a chování pro daný kontext reward skóre:

* Přímý vstup uživatele, pro návrhy při možnosti souvisejí ("se myslí pod X?").
* Délka relace.
* Čas mezi relacemi.
* Analýza mínění interakcí uživatele.
* Směrovat otázky a mini zjišťování, ve kterém robota požádá uživatele o zpětnou vazbu o použitelnosti, přesnost.
* Odpověď na oznámení, nebo zpoždění k reakci na výstrahy.

## <a name="composing-reward-scores"></a>Vytváření reward skóre

Potřebu skóre musí být vypočítány na obchodní logiku. Skóre můžou být vyjádřeny jako:

* Jedno číslo zaslán jednou 
* Skóre odeslána ihned (třeba 0,8) a další skóre odeslána později (obvykle 0,2 požadavku).

## <a name="default-rewards"></a>Výchozí odměny

Pokud neobdrží žádná odměna v rámci [Reward čekací doby](#reward-wait-time), doba trvání od pořadí volání, Personalizer implicitně platí **výchozí Reward** na tuto událost pořadí.

## <a name="building-up-rewards-with-multiple-factors"></a>Vytváření odměny několika faktory  

Pro efektivní přizpůsobení lze sestavit reward skóre (libovolné číslo od -1 a 1) založené na více faktorech. 

Například můžete použít tato pravidla pro přizpůsobení seznamu obsahu videa:

|Chování uživatelů|Částečné skóre hodnota|
|--|--|
|Uživatel kliknul na horní položku.|+0.5 rewards|
|Uživatel otevřel skutečný obsah této položky.|+0.3 rewards|
|Uživatel sledovali vysílání televizní 5 minut obsahu nebo 30 %, podle toho, co je delší.|+0.2 rewards|
|||

Celkový počet reward můžete odešlete do rozhraní API.

## <a name="calling-the-reward-api-multiple-times"></a>Volání rozhraní API pro potřebu více než jednou.

Můžete také volat rozhraní API Reward pomocí stejné ID události odeslání skóre jiný účet. Když Personalizer dostane těchto odměny, určuje konečný reward pro tuto událost na základě agregace je uvedená v nastavení Personalizer.

Nastavení agregace:

*  **První**: Přijímá první reward skóre přijaté události a zbytek zahodí.
* **Součet**: Přijímá všechny reward skóre shromážděných pro eventId a přidá je společně.

Všechny odměny pro událost, která jsou přijata po **Reward čekací doby**, se zahodí a nemají vliv na trénování modelů.

Součtem reward skóre, může být finální reward vyšší než 1 nebo nižší než -1. To neprovede služby nezdaří.

<!--
@edjez - is the number ignored if it is outside the acceptable range?
-->

## <a name="best-practices-for-calculating-reward-score"></a>Osvědčené postupy pro výpočet skóre rewards

* **Vezměte v úvahu true indikátory úspěšné přizpůsobení**: Je snadné uvažují v rámci kliknutí, ale vhodné reward je založena na to, co chtějí uživatelům *dosáhnout* místo chcete lidem *proveďte*.  Například odměny v kliknutí může vést k výběru obsah, který je clickbait náchylné k chybám.

* **Použití odměny skóre pro jak kvalitní individuálního nastavení spolupráci**: Přizpůsobení návrh film snad výsledkem by uživatel sledování videa a poskytuje tak vysoké hodnocení. Protože hodnocení filmu pravděpodobně závisí na mnoha věcech (kvalita acting nálady uživatele), není dobrý reward signál o tom, dobře *přizpůsobení* pracovali. Uživatel sledování prvních několik minut videa, ale může být signál lepší přizpůsobení efektivitu a odesílání reward 1 po 5 minutách bude lepší signál.

* **Odměny se vztahují jenom na RewardActionID**: Personalizer platí odměny pochopit účinnosti akce určená ve RewardActionID. Pokud se rozhodnete zobrazíte další akce a uživatel klikne na nich potřebu musí být nula.

* **Vezměte v úvahu nežádoucí důsledky**: Vytvořit reward funkce, které vést k zodpovědná výsledků při využití [etickými a využívání](ethics-responsible-use.md).

* **Použít přírůstkové odměny**: Přidání částečné odměny pro menší chování uživatelů pomáhá Personalizer dosahování lepší odměny. Tato přírůstkové reward umožňuje algoritmus vědět, že je to stále blíž k zapojení uživatel v poslední požadované chování.
    * Pokud seznam filmy, se zobrazují, pokud uživatel najede myší první z nich nějakou dobu zobrazíte další informace, můžete určit, že došlo k nějaké zapojení uživatelů. Chování můžete počítat s reward skóre 0,1. 
    * Pokud uživatel otevře na stránce a potom byla ukončena, může být skóre reward 0.2. 

## <a name="reward-wait-time"></a>Doba čekání rewards

Personalizer bude korelovat informace pořadí volání s odměny poslaná Reward volání pro trénování modelu. Ty můžou pocházet v různých časech. Personalizer čeká po omezenou dobu od pořadí volání se stalo, i v případě, že pořadí volání byla provedena jako neaktivní událost a aktivovat později.

Pokud **Reward čekací doby** vyprší platnost a nepřichází žádné informace o odměnu, reward výchozí se použije pro tuto událost pro vzdělávání. Maximální čekací doba je 6 dnů.

## <a name="best-practices-for-setting-reward-wait-time"></a>Osvědčené postupy pro nastavení reward doba čekání

Postupujte podle následujících doporučení pro lepší výsledky.

* Ujistěte se, Reward čekací doby krátký, protože je to možné, ale zároveň je nechává dost času na získání zpětné vazby uživatelů. 

<!--@Edjez - storage quota? -->

* Neklikejte dobu, po kterou je kratší než čas potřebný k získání zpětné vazby. Například pokud některé z vašich odměny poté, co uživatel má sledovali vysílání televizní 1 minuta videa, experiment by měl být alespoň double, která.

## <a name="next-steps"></a>Další postup

* [Zpětnovazebnému učení](concepts-reinforcement-learning.md) 
* [Zkuste pořadí rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [Zkuste účet rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)
