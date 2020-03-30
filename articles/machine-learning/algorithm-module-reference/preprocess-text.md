---
title: 'Text před zpracováním: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Předzpracování textu v Azure Machine Learning vyčistit a zjednodušit text.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: 6e4d4c8f798418e090caeba091dec33c71f0458f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477489"
---
# <a name="preprocess-text"></a>Předzpracování textu

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

K vyčištění a zjednodušení textu použijte modul **Předzpracování textu.** Podporuje tyto běžné operace zpracování textu:

* Odstranění stop-slov
* Použití regulárních výrazů k vyhledání a nahrazení určitých cílových řetězců
* Lemmatizace, která převádí více souvisejících slov na jednu kanonické formu
* Normalizace případu
* Odstranění určitých tříd znaků, například čísel, speciálních znaků a posloupností opakovaných znaků, například "aaaa"
* Identifikace a odstranění e-mailů a adres URL

Modul **Předzpracování textu** aktuálně podporuje pouze angličtinu.

## <a name="configure-text-preprocessing"></a>Konfigurace předběžného zpracování textu  

1.  Přidejte modul **Předprocesový text** do kanálu v Azure Machine Learning. Tento modul naleznete v části **Analýza textu**.

1. Připojte datovou sadu, která obsahuje alespoň jeden sloupec obsahující text.

1. V rozevíracím seznamu **Jazyk** vyberte jazyk.

1. **Sloupec Textu, který chcete vyčistit:** Vyberte sloupec, který chcete předem zpracovat.

1. **Odstranit slova stop**: Tuto možnost vyberte, pokud chcete na textový sloupec použít předdefinovaný seznam stopword. 

    Stopword seznamy jsou jazykově závislé a přizpůsobitelné.

1. **Lemmatizace**: Tuto možnost vyberte, pokud chcete, aby slova byla reprezentována v jejich kanonické podobě. Tato možnost je užitečná pro snížení počtu jedinečných výskytů jinak podobných textových tokenů.

    Proces lemmatizace je vysoce závislý na jazyce.

1. **Rozpoznat věty**: Tuto možnost vyberte, pokud chcete, aby modul při provádění analýzy vložil značku hranice věty.

    Tento modul používá řadu tří `|||` znaků kanálu k reprezentaci zakončení věty.

1. Proveďte volitelné operace hledání a nahrazování pomocí regulárních výrazů.

    * **Vlastní regulární výraz**: Definujte hledaný text.
    * **Vlastní náhradní řetězec**: Definujte jednu hodnotu nahrazení.

1. **Normalizovat malá a malá písmena**: Tuto možnost vyberte, pokud chcete převést velká písmena ASCII na jejich malé tvary.

    Pokud znaky nejsou normalizovány, stejné slovo velkými a velkými písmeny je považováno za dvě různá slova.

1. Ze zpracovaného výstupního textu můžete také odebrat následující typy znaků nebo posloupnostznaků:

    * **Odebrat čísla**: Tuto možnost vyberte, chcete-li odebrat všechny číselné znaky pro zadaný jazyk. Identifikační čísla jsou závislá na doméně a jazyková. Pokud číselné znaky jsou nedílnou součástí známého slova, nemusí být číslo odebráno.
    
    * **Odebrat speciální znaky**: Tuto možnost použijte k odebrání nealfanumerických speciálních znaků.
    
    * **Odstranit duplicitní znaky**: Tuto možnost vyberte, chcete-li odebrat další znaky v sekvencích, které se opakují více než dvakrát. Například sekvence jako "aaaaa" by být snížena na "aa".
    
    * **Odebrat e-mailové adresy**: Tuto možnost `<string>@<string>`vyberte, chcete-li odebrat libovolnou posloupnost formátu .  
    * **Odebrat adresy URL**: Tuto možnost vyberte, chcete-li `http` `https`odebrat všechny sekvence, které obsahují následující předpony adres URL: , , `ftp``www`
    
1. **Rozbalit slovesné kontrakce**: Tato možnost se vztahuje pouze na jazyky, které používají slovesné kontrakce; v současné době pouze v angličtině. 

    Například výběrem této možnosti můžete nahradit frázi *"nezůstane tam"* s *"by tam nezůstal"*.

1. **Normalizovat zpětná lomítka na lomítka**: Tuto možnost vyberte, chcete-li mapovat všechny instance souboru `\\` `/`.

1. **Rozdělit tokeny na speciální znaky**: Tuto možnost vyberte, `&` `-`pokud chcete rozdělit slova na znaky, jako je , a tak dále. Tato možnost může také snížit speciální znaky, když se opakuje více než dvakrát. 

    Řetězec `MS---WORD` by byl například rozdělen na `MS`tři `-`tokeny , a `WORD`.

1. Odešlete potrubí.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 