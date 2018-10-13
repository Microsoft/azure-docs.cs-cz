---
title: O převod textu na řeč
description: Přehled možností převod textu na řeč.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: erhopf
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: erhopf
ms.openlocfilehash: bc60eed63fb40c42fc4331edb01e15f850bf6ecb
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166079"
---
# <a name="about-the-text-to-speech-api"></a>O převod textu na řeč rozhraní API

**Převod textu na řeč** Speech service rozhraní API (převod textu na ŘEČ) převede vstupní text na řeč přirozeného to nezní (také nazývané *syntézu řeči*).

Pro vygenerování řeči, vaše aplikace odešle požadavky HTTP POST do služby řeči. Tady je text syntetizovat do mluvené řeči lidských to nezní a vrátí jako zvukový soubor. Širokou škálu hlasy a jazyky jsou podporovány.

Mezi scénáře, v které řeči syntézu přijali patří:

* *Vylepšení usnadnění přístupu:* **převod textu na řeč** technologie umožňuje vlastníkům obsahu a vydavatelé reagovat uživatelům různými způsoby interakce s jeho obsahem. Lidé s zrakově nebo vyřešit potíže se čtení nebudou moct využívat obsah aurally si cení toho. Hlasový výstup také usnadňuje uživatelům využívat textový obsah, jako je například novinových článcích nebo blogy na mobilních zařízeních při jste na cestách nebo práv vyplývajících z.

* *Reagovat ve scénářích multitaskingu:* **převod textu na řeč** umožňuje uživatelům chránit před důležité informace rychle a pohodlně při zvyšování nebo jinak mimo pohodlnou čtení prostředí. Navigace je běžné aplikace v této oblasti. 

* *Vylepšení učení s několika režimy:* různí lidé další nejlepší různými způsoby. Online výuka odborníků ukázalo, že poskytuje pro hlasové hovory a textové společně vám pomohou lépe informace o další informace a zachovat.

* *Poskytuje intuitivní roboti nebo Asistenti:* schopnost komunikovat může být nedílnou součástí inteligentní chatovací robot nebo virtuální Pomocníka s nastavením. Více společností vyvíjíte chatovací roboty k poskytování poutavé prostředí služby pro své zákazníky. Hlasové přidá jinou dimenzi tím, že odpovědi bodu robotů také přijímat aurally (například telefonní).

## <a name="voice-support"></a>Podpora hlasu

Microsoft **převod textu na řeč** služba nabízí více než 75 hlasy ve více než 45 jazyků a národních prostředí. Pokud chcete použít tyto standardní "hlasová písma", stačí zadat název hlas s několika dalších parametrů při volání rozhraní REST API služby. Podrobnosti hlasy podporované, najdete v článku [podporované jazyky](language-support.md#text-to-speech). 

Pokud chcete jedinečný hlas pro vaši aplikaci, můžete vytvořit [vlastní hlasové písmo](how-to-customize-voice-font.md) z vlastní ukázky řeči.

## <a name="api-capabilities"></a>Funkce rozhraní API

Mnoho funkcí **převod textu na řeč** rozhraní API – zejména v oblasti Vlastní nastavení – jsou k dispozici prostřednictvím REST. V následující tabulce najdete souhrn možností jednotlivých metod přístupu k rozhraní API. Úplný seznam možností a podrobnosti o rozhraní API, obraťte se prosím [Swagger](https://swagger/service/11ed9226-335e-4d08-a623-4547014ba2cc#/),

| Případ použití | REST | Sady SDK |
|-----|-----|-----|----|
| Nahrání datových sad pro přizpůsobení hlasu | Ano | Ne |
| Vytvoření a správa modelů hlasového písma | Ano | Ne |
| Vytvoření a Správa nasazení hlasového písma | Ano | Ne |
| Vytvoření a správa testů hlasového písma| Ano | Ne |
| Správa předplatných | Ano | Ne |

> [!NOTE]
> Rozhraní API implementuje tohoto omezení požadavků rozhraní API 25 na 5 sekund. Zpráva hearders informuje omezení.

## <a name="next-steps"></a>Další postup

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Zobrazit postup syntetizace mluvené řeči přes rozhraní REST API](how-to-text-to-speech.md)
