---
title: Nejčastější dotazy na charakteru Chat - kognitivní služby Azure | Microsoft Docs
description: Nejčastější dotazy na charakteru chatu
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.topic: faq
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ms.openlocfilehash: 5304b25672f9951e004876dfd19af75ea7d86502
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343779"
---
# <a name="frequently-asked-questions"></a>Nejčastější dotazy

## <a name="why-doesnt-this-answer-every-question-i-ask-it-like-other-chat-bots"></a>Proč není to odpovídat na všechny otázky, které mohu požádat, jako ostatní robotů chatu?

Konverzace charakteru projektu, může zvýšit robota s běžné talk malá, která umožňující prezentovat charakteru a vytvoří obsáhlejší činnost koncového uživatele. Je není určený pro přenos na dlouhé konverzace o tématech, které nejsou relevantní pro primární funkce robota. Když ho může reagovat na všech konverzací, má omezená – ve verzi beta – doménám malá talk běžné.

## <a name="how-can-i-customize-the-personality-to-suit-my-brand"></a>Jak můžete přizpůsobit tak, aby vyhovovala Moje brand charakteru?

Vyberte nejbližší osobě dostupná výchozí osoby. V současné době můžete provést redakční knihovny a upravit odpovědi tak, aby lépe vyhovoval vaší značkou. V budoucnu můžete odeslat ukázkové sady utterances z vaší zvolené charakteru a najít jeho nejbližší ID verze osobě. Existují také způsoby přeučování a přizpůsobení modelu.

## <a name="is-this-service-powering-existing-intelligent-agents-such-as-zo"></a>Je tato služba pohánějící existující inteligentní prostředky, jako je například Zo?

Služba pohánějící Zo, Cortana a projekt charakteru Chat sdílet některé podobné techniky, ale jsou různé zásobníky. Ji doplnil learnings z zkušeností s Zo a Cortana.

## <a name="can-this-service-lead-to-bad-customer-experiences"></a>Tuto službu, může dojít k chybné zákazníka prostředí?

K poskytování a pestřejší prostředí, můžete charakteru Chat vygenerování odpovědí nad rámec těch v redakční datovou sadu a pokusí se interpretovat všechny vstup uživatele. Ano je možné, že odpověď nebude pravděpodobně vpravo v kontextu. Některé ovládací prvky byly uvedeny do k prevenci nepříznivý odpovědi, vychází ze znalostní báze od inteligentního agentů jako Zo. Ve výchozím projektu charakteru Chat nastavena reagovat výhradně na záměry rozpoznaný uživatel. Chcete otestovat, zda Chat charakteru projektu je vhodný pro vaše okolností. Vaše zpětná vazba je úvodní, pokud se zobrazí všechny položky, které potřebuje další školení. Pokud použijete tuto službu s vašimi zákazníky v budoucnu, doporučujeme že zvážit anonymizovaná protokolování, který vám pomůže identifikovat problémy s interakcí za provozu.