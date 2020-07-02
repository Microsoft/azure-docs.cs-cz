---
title: Umělá inteligence
description: LUIS používá umělou inteligentní analýzu pro poskytování jazyka pro vaše data na základě schématu, které jste definovali.
ms.topic: conceptual
ms.date: 06/29/2020
ms.openlocfilehash: 4f145585e097a3cf6a2338dbab879f6fce07f71d
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2020
ms.locfileid: "85802650"
---
# <a name="artificial-intelligence-in-language-understanding-luis"></a>Umělá inteligentní funkce v Language Understanding (LUIS)

LUIS používá umělá inteligentní funkce k poskytování dat NLU (přirozený jazyk) na základě schématu, které jste definovali.

## <a name="natural-language-processing"></a>Zpracování přirozeného jazyka

Přirozené Language Understanding (NLU) je konkrétní dílčí téma zpracování přirozeného jazyka (NLP).

Zpracování přirozeného jazyka je širší koncept, který zpracovává jakoukoli formu zpracování textových dat, zahrnuje například:

* Tokenizace
* Označení částí řeči (POS)
* Segmentation
* Morfologická analýza
* Sémantická podobnost
* Nevyužít
* Překlad

## <a name="natural-language-processing-in-luis"></a>Zpracování přirozeného jazyka v LUIS

Pro aplikaci LUIS je k dispozici zpracování přirozeného jazyka následujícími způsoby:
* [Principy přirozeného jazyka](#natural-language-understanding) (Luis)
* Konfigurovatelné NLP aspekty v LUIS:
    * [Tokenizace](luis-language-support.md#tokenization)
    * Nastavení morfologie prostřednictvím diakritických znamének, interpunkčních znamének a [rozhraní](luis-reference-application-settings.md) Word Forms API
* Předběžná nebo následná zpracování dotazů utterance poskytovaných jinými [Cognitive Services](../Welcome.md) , jako jsou:
    * [NAT](../translator/translator-info-overview.md)

## <a name="natural-language-understanding"></a>Porozumění přirozenému jazyku

NLU je schopnost _transformovat_ lingvistické prohlášení na reprezentaci, která umožňuje pochopit uživatele přirozeně. Porozumění přirozenému jazyku zůstává velmi náročný problém a je definován jako problém _s pevným problémem AI_ .

LUIS je zaměřený na záměr a extrakci, což zahrnuje i schopnost identifikovat:
* Co chce uživatel
* O čem mluví.

LUIS má málo nebo žádné znalosti o širších _NLP_ aspektech, jako je například sémantická podobnost, bez explicitní identifikace v příkladech. Například následující tokeny (slova) jsou tři různé věci, dokud nejsou použity v podobných kontextech v uvedených příkladech:

* zakoupit
* nákupu
* jste

## <a name="next-steps"></a>Další kroky

* Další informace o [životním cyklu vývoje](luis-concept-app-iteration.md) aplikace Luis