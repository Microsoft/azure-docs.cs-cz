---
title: 'Text předběžného zpracování: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul textu předzpracování v Azure Machine Learning k vyčištění a zjednodušení textu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: 4112d26d6a21ac800e20bb67ce24a35ca9d09a13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905230"
---
# <a name="preprocess-text"></a>Předzpracování textu

Tento článek popisuje modul v Návrháři Azure Machine Learning.

K čištění a zjednodušení textu použijte modul **předzpracování textu** . Podporuje tyto běžné operace zpracování textu:

* Odebrání stop-slov
* Použití regulárních výrazů k vyhledání a nahrazení konkrétních cílových řetězců
* Lemmatizátor nebo předzpracování, který převede více souvisejících slov na jeden kanonický tvar
* Normalizace případu
* Odebrání určitých tříd znaků, například čísel, speciálních znaků a posloupností opakujících se znaků, jako je například "AAAA"
* Identifikace a odebrání e-mailů a adres URL

Modul pro **zpracování textu** v současné době podporuje pouze angličtinu.

## <a name="configure-text-preprocessing"></a>Konfigurace předběžného zpracování textu  

1.  Přidejte modul **textu předběžného zpracování** do vašeho kanálu v Azure Machine Learning. Tento modul můžete najít v části **Analýza textu**.

1. Připojte datovou sadu, která má alespoň jeden sloupec obsahující text.

1. V rozevíracím seznamu **jazyk** vyberte jazyk.

1. **Sloupec text, který se má vyčistit**: vyberte sloupec, který chcete předzpracovat.

1. **Odebrat slova stop**: tuto možnost vyberte, pokud chcete použít předdefinovaný seznam stopslovo do textového sloupce. 

    Seznamy stopslovo jsou závislé na jazyku a přizpůsobitelné.

1. **Lemmatizátor nebo předzpracování**: tuto možnost vyberte, pokud chcete, aby se slova reprezentují v kanonickém tvaru. Tato možnost je užitečná pro snížení počtu jedinečných výskytů jiných podobných textových tokenů.

    Proces lemmatizátor nebo předzpracování je velmi závislý na jazyku...

1. **Zjistit věty**: tuto možnost vyberte, pokud chcete, aby modul při provádění analýzy vkládal značku ohraničení věty.

    Tento modul používá sérii tří znaků kanálu `|||` , které reprezentují zakončení věty.

1. Provádět volitelné operace hledání a nahrazení pomocí regulárních výrazů.

    * **Vlastní regulární výraz**: Definujte text, který hledáte.
    * **Vlastní řetězec nahrazení**: Definujte jedinou nahrazující hodnotu.

1. **Normalizovat velikost písmen na malá písmena**: tuto možnost vyberte, pokud chcete převést znaky ASCII znaků na jejich tvary malých písmen.

    Pokud nejsou znaky normalizovány, stejné slovo velkými a malými písmeny je považováno za dvě odlišná slova.

1. Z zpracovaného výstupního textu můžete také odebrat následující typy znaků nebo sekvence znaků:

    * **Odebrat čísla**: tuto možnost vyberte, pokud chcete odebrat všechny číselné znaky pro zadaný jazyk. Identifikační čísla jsou závislá na doméně a závislé na jazyku. Pokud jsou číselné znaky integrální součástí známého slova, číslo se nemusí odebrat.
    
    * **Odebrat speciální znaky**: tuto možnost použijte, pokud chcete odebrat jakékoli jiné než alfanumerické speciální znaky.
    
    * **Odebrat duplicitní znaky**: tuto možnost vyberte, pokud chcete odebrat nadbytečné znaky v jakékoli sekvenci, která se opakuje více než dvakrát. Například sekvence jako "AAAAA" by se snížila na "AA".
    
    * **Odebrat e-mailové adresy**: tuto možnost vyberte, pokud chcete odebrat jakoukoli sekvenci formátu `<string>@<string>` .  
    * **Odebrat adresy URL**: tuto možnost vyberte, pokud chcete odebrat všechny sekvence obsahující následující PŘEDPONY adresy URL: `http` , `https` , `ftp` , `www`
    
1. **Rozbalit kontrakty operací**: Tato možnost platí jenom pro jazyky, které používají kontrakty operací. v současné době pouze v angličtině. 

    Když například vyberete tuto možnost, můžete *nahradit frázi. nezůstane tam* , kde *se nachází.*

1. **Normalizovat zpětná lomítka na lomítka**: tuto možnost vyberte, pokud chcete namapovat všechny instance `\\` na `/` .

1. **Rozdělit tokeny na speciální znaky**: tuto možnost vyberte, pokud chcete rozdělit slova na znaky, například `&` , `-` a tak dále. Tato možnost může také snížit speciální znaky, pokud se opakuje více než dvakrát. 

    Například řetězec `MS---WORD` by byl rozdělen na tři tokeny,, `MS` `-` a `WORD` .

1. Odešlete kanál.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 