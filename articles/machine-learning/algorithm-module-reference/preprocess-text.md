---
title: 'Text předběžného zpracování: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se, jak používat modul textu předzpracování ve službě Azure Machine Learning k vyčištění a zjednodušení textu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 09/01/2019
ms.openlocfilehash: 4ee5e90b36f7a8fb3bfb42cad425cbb272553b3a
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210829"
---
# <a name="preprocess-text"></a>Předběžné zpracování textu

Tento článek popisuje modul vizuálního rozhraní (Preview) pro službu Azure Machine Learning.

K čištění a zjednodušení textu použijte modul **předzpracování textu** . Podporuje tyto běžné operace zpracování textu:

* Odebrání stop-slov
* Použití regulárních výrazů k vyhledání a nahrazení konkrétních cílových řetězců
* Lemmatizátor nebo předzpracování, který převede více souvisejících slov na jeden kanonický tvar
* Normalizace případu
* Odebrání určitých tříd znaků, například čísel, speciálních znaků a posloupností opakujících se znaků, jako je například "AAAA"
* Identifikace a odebrání e-mailů a adres URL

Modul pro **zpracování textu** v současné době podporuje pouze angličtinu.

## <a name="configure-text-preprocessing"></a>Konfigurace předběžného zpracování textu  

1.  Přidejte modul **textu předběžného zpracování** do experimentu ve službě Azure Machine Learning. Tento modul můžete najít v části **Analýza textu**.

1. Připojte datovou sadu, která má alespoň jeden sloupec obsahující text.

1. V rozevíracím seznamu **jazyk** vyberte jazyk.

1. **Sloupec textu, který se má vyčistit**: Vyberte sloupec, který chcete předzpracovat.

1. **Odebrat slova stop**: Tuto možnost vyberte, pokud chcete použít předdefinovaný seznam stopslovo do textového sloupce. 

    Seznamy stopslovo jsou závislé na jazyku a přizpůsobitelné.

1. **Lemmatizátor nebo předzpracování**: Tuto možnost vyberte, pokud chcete, aby se v kanonickém tvaru reprezentují slova. Tato možnost je užitečná pro snížení počtu jedinečných výskytů jiných podobných textových tokenů.

    Proces lemmatizátor nebo předzpracování je velmi závislý na jazyku...

1. **Zjistit věty**: Tuto možnost vyberte, pokud chcete, aby modul při provádění analýzy vkládal značku ohraničení věty.

    Tento modul používá sérii tří znaků `|||` kanálu, které reprezentují zakončení věty.

1. Provádět volitelné operace hledání a nahrazení pomocí regulárních výrazů.

    * **Vlastní regulární výraz**: Zadejte text, který hledáte.
    * **Vlastní řetězec nahrazení**: Definujte jedinou nahrazující hodnotu.

1. **Normalizovat malá a velká**písmena: Tuto možnost vyberte, pokud chcete převést znaky ASCII znaků na malá písmena.

    Pokud nejsou znaky normalizovány, stejné slovo velkými a malými písmeny je považováno za dvě odlišná slova.

1. Z zpracovaného výstupního textu můžete také odebrat následující typy znaků nebo sekvence znaků:

    * **Odebrat čísla**: Tuto možnost vyberte, pokud chcete pro zadaný jazyk odebrat všechny číselné znaky. Identifikační čísla jsou závislá na doméně a závislé na jazyku. Pokud jsou číselné znaky integrální součástí známého slova, číslo se nemusí odebrat.
    
    * **Odeberte speciální znaky**: Tuto možnost použijte, pokud chcete odebrat jakékoli jiné než alfanumerické speciální znaky.
    
    * **Odstraňte duplicitní znaky**: Tuto možnost vyberte, pokud chcete odebrat nadbytečné znaky v jakékoli sekvenci, která se opakuje více než dvakrát. Například sekvence jako "AAAAA" by se snížila na "AA".
    
    * **Odebrat e-mailové adresy**: Tuto možnost vyberte, pokud chcete odebrat jakoukoli sekvenci `<string>@<string>`formátu.  
    * **Odebrat adresy URL**: Tuto možnost vyberte, pokud chcete odebrat všechny sekvence obsahující následující předpony adresy URL `http`: `https`, `ftp`,,`www`
    
1. **Rozbalte položku kontrakty operací**: Tato možnost se vztahuje jenom na jazyky, které používají kontrakty operací. v současné době pouze v angličtině. 

    Když například vyberete tuto možnost, můžete nahradit frázi. nezůstane tam, kde se nachází.

1. Normalizuje zpětná **lomítka na lomítka**: Tuto možnost vyberte, pokud chcete namapovat `\\` všechny `/`instance na.

1. **Rozdělit tokeny na speciální znaky**: Tuto možnost vyberte `&`, pokud chcete rozdělit slova na znaky, například, `-`a tak dále. Tato možnost může také snížit speciální znaky, pokud se opakuje více než dvakrát. 

    Například řetězec `MS---WORD` by byl rozdělen na tři tokeny `-`, `MS`, a `WORD`.

1. Spusťte experiment.

## <a name="next-steps"></a>Další postup

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 