---
title: Aktivní učení – Personalizer
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: edjez
ms.openlocfilehash: c44afc81a7ec9d05cdc04cc8bc46c77cd51ceaf8
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722526"
---
# <a name="active-learning-and-learning-policies"></a>Aktivní učení a učení zásady 

Když vaše aplikace volá rozhraní API pořadí, obdržíte řád obsah. Obchodní logiky můžete toto pořadí určí, jestli obsah by měl být displej tak, aby uživatel. Při zobrazení seřazený obsah, který je _aktivní_ pořadí událostí. Když vaše aplikace nezobrazuje, seřazené obsahu, který je _neaktivní_ pořadí událostí. 

Informace o aktivních pořadí událostí je vrácena Personalizer. Tyto informace slouží k trénování modelu přes aktuální zásady learning pokračovat.

## <a name="active-events"></a>Aktivní události

Aktivní události by měla být vždy zobrazí uživateli a potřebu volání se vrátit do překonáte bariéru učení. 

### <a name="inactive-events"></a>Aktivní události 

Neaktivní události neměli měnit základní model, protože uživatel Listener was not given možnost výběru z seřazený obsah.

## <a name="dont-train-with-inactive-rank-events"></a>Není trénování s neaktivní pořadí událostí 

Pro některé aplikace budete muset nainstalovat bez mého ještě, pokud vaše aplikace bude uživateli zobrazit výsledky volání rozhraní API pořadí. 

To se stane, když:

* Vám může být předem vykreslování některé uživatelské rozhraní, které uživatel může nebo nemůže získat zobrazíte. 
* Vaše aplikace může dělat prediktivní přizpůsobení, ve kterém pořadí volání s kontextem méně v reálném čase a jejich výstup může nebo nemusí být v aplikaci použít. 

### <a name="disable-active-learning-for-inactive-rank-events-during-rank-call"></a>Zakázat active learning pro neaktivní pořadí událostí během pořadí volání

Pro zakázání automatické učení, volejte pořadí s `learningEnabled = False`.

Učení pro neaktivní událost se aktivuje implicitně Pokud odesíláte reward pro počet rozměrů.

## <a name="learning-policies"></a>Učení zásady

Učení zásad určuje konkrétní *hyperparameters* školení modelu. Dva modely stejná data, školení na jiné výukové zásadách, budou se chovat jinak.

### <a name="importing-and-exporting-learning-policies"></a>Import a export zásad učení

Můžete importovat a exportovat learning soubory zásad z portálu Azure portal. To umožňuje uložit existující zásady, jejich testování, je nahradit a archivují ve vaší správě zdrojového kódu jako artefakty pro budoucí použití a auditu.

### <a name="learning-policy-settings"></a>Nastavení zásad učení

Nastavení **Learning zásad** nejsou určené ke změnám. Až porozumíte, jak by mohly mít dopad Personalizer jenom změňte nastavení. Změna nastavení, aniž by tyto znalosti způsobí vedlejší účinky, včetně zrušení platnosti Personalizer modely.

### <a name="comparing-effectiveness-of-learning-policies"></a>Porovnání efektivitu studijních zásady

Můžete porovnat jak různé zásady Learning by provedly z posledních dat v protokolech Personalizer prováděním [offline hodnocení](concepts-offline-evaluation.md).

[Nahrát vlastní zásady učení](how-to-offline-evaluation.md) má být porovnán s aktuální zásady učení.

### <a name="discovery-of-optimized-learning-policies"></a>Zjišťování optimalizované learning zásad

Personalizer zásadu můžete vytvořit více optimalizované learning při provádění [offline hodnocení](how-to-offline-evaluation.md). Více optimalizované learning zásady, které se zobrazí pro lepší odměny v offline zkušební verzi, budou poskytovat lepší výsledky při použití v Personalizer online.

Po vytvoření zásady optimalizované učení, můžete použít přímo na Personalizer nahradí aktuální zásady okamžitě, nebo můžete uložit pro další testování a rozhodnout se v budoucnu, jestli se má zahodit, uložit nebo použít později.