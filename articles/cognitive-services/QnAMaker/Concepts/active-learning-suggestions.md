---
title: Aktivní návrhy učení – QnA Maker
description: Přehledy aktivních kurzů vám umožní vylepšit kvalitu znalostní báze tím, že navrhují alternativní otázky na základě počtu uživatelů, a to na pár otázek a odpovědí.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: e8cdb1d471ceef2aef790c426424b9224faac5ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777033"
---
# <a name="active-learning-suggestions"></a>Aktivní návrhy výukových kurzů

Funkce _aktivní návrhy kurzů_ vám umožní zdokonalit kvalitu znalostní báze tím, že navrhuje alternativní otázky na základě počtu uživatelů, které odpovídají vašemu páru dotazů a odpovědí. Tyto návrhy si můžete prohlédnout, buď je přidáte do existujících otázek, nebo je odmítnete.

Vaše znalostní báze se nemění automaticky. Aby se změny projevily, musíte přijmout návrhy. Tyto návrhy přidávají otázky, ale nemění ani neodstraňují stávající otázky.

## <a name="what-is-active-learning"></a>Co je aktivní učení?

QnA Maker se učí nové variace otázek s implicitní a explicitní zpětnou vazbou.

* [Implicitní zpětná vazba](#how-qna-makers-implicit-feedback-works) – seznámení s tím, že má uživatelská otázka více odpovědí s výsledky, které jsou velmi blízko a považuje se za zpětnou vazbu. K tomu nemusíte nic dělat.
* [Explicitní názory](#how-you-give-explicit-feedback-with-the-train-api) – Pokud se ve znalostní bázi Knowledge Base vrátí více odpovědí s malým kolísáním skóre, klientská aplikace se zeptá, který dotaz je na správnou otázku. Explicitní zpětná vazba uživatele se pošle QnA Maker s využitím [rozhraní API pro vlaky](../How-to/improve-knowledge-base.md#train-api).

Obě metody poskytují pořadí s podobnými dotazy, které jsou v clusteru.

## <a name="how-active-learning-works"></a>Jak funguje aktivní učení

Aktivní učení se aktivuje na základě skóre nejčastějších odpovědí vrácených QnA Maker. Pokud rozdíly v skóre mezi páry QnA, které odpovídají dotazu, leží v malém rozsahu, dotaz je považován za možný návrh (jako alternativní otázka) pro každý z možných párů QnA. Jakmile přijmete navrhovanou otázku pro určitý pár QnA, je odmítnut pro ostatní páry. Po přijetí návrhů musíte pamatovat na uložení a výuku.

Aktivní učení nabízí nejlepší možné návrhy v případech, kdy koncové body získávají přijatelné množství a různé dotazy na používání. Když je 5 nebo více podobných dotazů clusterovaných, každých 30 minut QnA Maker navrhuje dotazy založené na uživateli v Návrháři znalostní báze, aby je bylo možné přijmout nebo odmítnout. Všechny návrhy jsou seskupené podle podobnosti a na základě frekvence konkrétních dotazů koncovými uživateli se zobrazují nejdůležitější návrhy na alternativní otázky.

Po navržení otázek na portálu QnA Maker musíte tyto návrhy projít a přijmout nebo odmítnout. Není k dispozici rozhraní API pro správu návrhů.

## <a name="turn-on-active-learning"></a>Aktivace aktivního učení

Ve výchozím nastavení je aktivní učení **vypnuté**.
Použití aktivního učení:
* Musíte [zapnout aktivní výuku](../How-To/use-active-learning.md#turn-on-active-learning-for-alternate-questions) , aby QnA maker shromažďovat alternativní otázky pro znalostní bázi.
* Chcete-li zobrazit navrhované alternativní otázky, [použijte možnosti zobrazení](../How-To/improve-knowledge-base.md#view-suggested-questions) na stránce Upravit.

## <a name="how-qna-makers-implicit-feedback-works"></a>Způsob fungování implicitní zpětné vazby QnA Maker

Implicitní zpětná vazba QnA Maker používá algoritmus k určení blízkosti skóre a následně zajišťuje aktivní návrhy učení. Algoritmus k určení blízkosti není jednoduchý výpočet. Rozsahy v následujícím příkladu nejsou určeny k pevnému, ale měly by být použity jako vodítko pro pochopení dopadu pouze na algoritmus.

Když je skóre otázky vysoce důvěrná, například 80%, rozsah skóre, která jsou považována za aktivní, je v širokém rozsahu přibližně do 10%. Vzhledem k poklesu spolehlivosti, jako je 40%, se rozsah hodnocení zmenší i přibližně do 4%.

V následující reakci JSON z dotazu na QnA Maker generateAnswer se skóre pro A, B a C blíží a budou považována za návrhy.

```json
{
  "activeLearningEnabled": true,
  "answers": [
    {
      "questions": [
        "Q1"
      ],
      "answer": "A1",
      "score": 80,
      "id": 15,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q2"
      ],
      "answer": "A2",
      "score": 78,
      "id": 16,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q3"
      ],
      "answer": "A3",
      "score": 75,
      "id": 17,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q4"
      ],
      "answer": "A4",
      "score": 50,
      "id": 18,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    }
  ]
}
```

QnA Maker neznáte, která odpověď je nejlepší odpovědí. Pomocí seznamu návrhů QnA Makerového portálu můžete vybrat nejlepší odpověď a znovu zahájit vlak.


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Způsob poskytnutí explicitní zpětné vazby pomocí rozhraní API pro vlaky

QnA Maker potřebuje explicitní zpětnou vazbu, na kterou odpověděla odpověď na odpověď. Způsob, jakým je nejlepší odpověď určena, je až na vás a může zahrnovat:

* Zpětnou vazbu od uživatele vyberte jednu z odpovědí.
* Obchodní logika, například určení přijatelného rozsahu skóre.
* Kombinace zpětné vazby uživatelů a obchodní logiky.

Použijte [rozhraní API pro vlak](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train) k odeslání správné odpovědi na QnA maker, až ho uživatel vybere.

## <a name="next-step"></a>Další krok

> [!div class="nextstepaction"]
> [Dotazování znalostní báze](query-knowledge-base.md)