---
title: Jak funguje Personalizer - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer pomocí strojového učení chcete zjistit, jaká akce se má použít v kontextu. Každé smyčce learning má model, který se trénuje výhradně na datech, která jste odeslali do něj pomocí pořadí a volá potřebu. Každý studijní smyčky je zcela nezávisle na sobě navzájem.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 6b2237f27fba5eaf952932cd6592052649400b96
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027128"
---
# <a name="how-personalizer-works"></a>Jak funguje Personalizer

Personalizer pomocí strojového učení chcete zjistit, jaká akce se má použít v kontextu. Každé smyčce learning se model, který se trénuje výhradně na datech, která jste odeslali do ho přes **pořadí** a **Reward** volání. Každý studijní smyčky je zcela nezávisle na sobě navzájem. Vytvoření smyčky učení pro každou část nebo chování aplikace, které chcete přizpůsobit.

Pro každou smyčku **volání rozhraní API pořadí s** založenou na aktuálním kontextu se:

* Seznam možných akcí: obsah položky, ze kterého se má vybrat hlavní akce.
* Seznam [funkce kontextu](concepts-features.md): kontextově relevantní data, jako je například uživatel, obsah a kontextu.

**Pořadí** rozhraní API se rozhodne použít buď:

* _Ochrana Exploit_: Aktuální model rozhodnout nejvhodnějších akcí, které na základě posledních dat.
* _Prozkoumejte_: Vyberte jinou akci místo hlavní akce.

**Reward** rozhraní API:

* Shromažďuje data pro trénování modelu zaznamenáváním funkce a potřebu skóre každé pořadí volání.
* Tato data používá k aktualizaci modelu na základě zadané v nastavení _Learning zásad_.

## <a name="architecture"></a>Architektura

Následující obrázek ukazuje architekturu tok volání hodnocení a potřebu volání:

![alternativní text](./media/how-personalizer-works/personalization-how-it-works.png "jak funguje individuálního nastavení")

1. Personalizer používá interní modelů AI k určení pořadí v akci.
1. Služba rozhodne, zda aktuální model zneužití nebo prozkoumat nové možnosti pro model.  
1. Výsledek hodnocení se odešle do centra událostí.
1. Potřebu přijetí Personalizer potřebu se odešle do centra událostí. 
1. Počet rozměrů a potřebu se korelují.
1. AI dojde k aktualizaci modelu na základě výsledků korelace.
1. Modul odvození se aktualizuje s novým modelem. 

## <a name="research-behind-personalizer"></a>Výzkum za Personalizer

Personalizer je založeno na špičkové vědy a výzkumu v oblasti [Zpětnovazebnému učení](concepts-reinforcement-learning.md) včetně papíry, výzkumu a probíhající oblasti průzkum v Microsoft Research.

## <a name="terminology"></a>Terminologie

* **Učení smyčky**: Pro každou součást aplikace, který může přinést přizpůsobení může vytvořit smyčku učení. Pokud máte více než jednom prostředí můžete přizpůsobit, vytvořte pro každou smyčku. 

* **Akce**: Akce jsou položky obsahu, jako je například produktů nebo propagační akce lze vybírat. Personalizer vybere hlavní akce se zobrazí uživatelům, označované jako _odměna v rámci akce_, prostřednictvím rozhraní API pořadí. Každá akce může mít funkce odeslanou s požadavkem pořadí.

* **Kontext**: Poskytnout přesnější pořadí, zadejte informace o kontextu, například:
    * Vaše uživatele.
    * Zařízení, které se nachází na. 
    * Aktuální čas.
    * Další data o aktuální situaci.
    * Historická data o uživateli nebo kontext.

    Konkrétní aplikace může mít jiné kontextové informace. 

* **[Funkce](concepts-features.md)**: Jednotka informace o obsahu položky nebo kontextu uživatele.

* **Potřebu**: Míra jak uživatel odpověděl na rozhraní API pořadí vrácená akce, jako skóre mezi 0 a 1. Hodnota 0 až 1 je nastavena pomocí obchodní logiky a podle jak volba pomohl dosáhnout vašich obchodních cílů individuálního nastavení. 

* **Zkoumání**: Služba Personalizer k procházení, když místo vrácení nejvhodnějších akcí, zvolí jinou akci pro daného uživatele. Služba Personalizer zabraňuje odchylek stagnation a můžete přizpůsobit chování probíhající uživatelů, ve kterých. 

* **Doba trvání experimentu**: Množství času, které služba Personalizer čeká potřebu, od chvíle, kdy pořadí volání se naplnilo pro tuto událost.

* **Neaktivní události**: Neaktivní události je jedním kde volána pořadí, ale nejste jisti, že uživatel uvidí někdy výsledek z důvodu rozhodnutí klientské aplikace. Neaktivní události a umožňují vytvářet a ukládat výsledky přizpůsobení, potom se rozhodnete zahodíte později bez dopadu na modelu strojového učení.

* **Model**: Personalizer model zachytí všechna data se dozvěděli o chování uživatelů, získávání trénovací data z kombinace argumentů, které odesíláte do hodnocení a potřebu volání a s školení chování určené zásady učení. 

## <a name="next-steps"></a>Další postup

Vysvětlení [ve kterém mohou používat Personalizer](where-can-you-use-personalizer.md).