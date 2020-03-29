---
title: Návrhy aktivnívýuky - QnA Maker
description: Návrhy aktivnívýuky vám umožní zlepšit kvalitu vaší znalostní báze tím, že navrhnete alternativní otázky založené na uživatelských podáních na dvojici otázek a odpovědí.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: af4f6b399bfd537b38ea741d03e59371ee81e588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053144"
---
# <a name="active-learning-suggestions"></a>Návrhy aktivních učení

Funkce _Aktivní návrhy učení_ umožňuje zlepšit kvalitu znalostní báze tím, že navrhne alternativní otázky na základě uživatelských příspěvků na vaši dvojici otázek a odpovědí. Tyto návrhy zkontrolujete, a to buď jejich přidáním k existujícím otázkám, nebo jejich odmítnutím.

Znalostní báze se automaticky nezmění. Aby se jakákoli změna projevila, musíte návrhy přijmout. Tyto návrhy přidávají otázky, ale nemění ani neodstraňují existující otázky.

## <a name="what-is-active-learning"></a>Co je aktivní učení?

QnA Maker se učí nové varianty otázek s implicitní a explicitní zpětnou vazbou.

* [Implicitní zpětná vazba](#how-qna-makers-implicit-feedback-works) – ranker chápe, když uživatelská otázka obsahuje více odpovědí s skóre, které jsou velmi blízké, a považuje to za zpětnou vazbu. Nemusíš dělat nic, aby se to stalo.
* [Explicitní zpětná vazba](#how-you-give-explicit-feedback-with-the-train-api) – Při vrácení více odpovědí s malou odchylkou ve skóre ze znalostní báze se klientská aplikace zeptá uživatele, která otázka je správná otázka. Explicitní zpětná vazba uživatele je odeslána qnA makeru s [rozhraním TRAIN API](../How-to/improve-knowledge-base.md#train-api).

Obě metody poskytují ranker s podobnými dotazy, které jsou seskupeny.

## <a name="how-active-learning-works"></a>Jak aktivní učení funguje

Aktivní učení se aktivuje na základě skóre několika nejlepších odpovědí vrácených QnA Makerem. Pokud rozdíly skóre mezi QnA sady, které odpovídají dotazu leží v malém rozsahu, dotaz je považován za možný návrh (jako alternativní otázka) pro každý z možných párů QnA. Jakmile přijmete navrhovanou otázku pro konkrétní dvojici QnA, je odmítnuta pro ostatní páry. Musíte mít na paměti, uložit a trénovat, po přijetí návrhy.

Aktivní učení poskytuje nejlepší možné návrhy v případech, kdy koncové body získávají přiměřené množství a různé dotazy na použití. Při clusteru 5 nebo více podobných dotazů, každých 30 minut, QnA Maker navrhuje uživatelské otázky na znalostní bázi návrháře přijmout nebo odmítnout. Všechny návrhy jsou seskupeny podle podobnosti a nejlepší návrhy pro alternativní otázky jsou zobrazeny na základě četnosti konkrétních dotazů koncovými uživateli.

Jakmile jsou na portálu QnA Maker navrhovány otázky, musíte tyto návrhy zkontrolovat a přijmout nebo odmítnout. Neexistuje rozhraní API pro správu návrhů.

## <a name="turn-on-active-learning"></a>Zapnutí aktivního učení

Ve výchozím nastavení je aktivní učení **vypnuto**.
Použití aktivního učení:
* Musíte [zapnout aktivní učení,](../How-To/use-active-learning.md#turn-on-active-learning-for-alternate-questions) aby QnA Maker shromažďoval alternativní otázky pro vaši znalostní bázi.
* Chcete-li zobrazit navrhované alternativní otázky, [použijte možnosti zobrazení](../How-To/improve-knowledge-base.md#view-suggested-questions) na stránce Úpravy.

## <a name="how-qna-makers-implicit-feedback-works"></a>Jak funguje implicitní zpětná vazba qnA makeru

QnA Maker implicitní zpětná vazba používá algoritmus k určení blízkosti skóre pak dělá aktivní učení návrhy. Algoritmus k určení blízkosti není jednoduchý výpočet. Rozsahy v následujícím příkladu nejsou určeny k pevné, ale by měly být použity jako vodítko k pochopení dopadu algoritmu pouze.

Když je skóre otázky vysoce jisté, například 80 %, rozsah skóre, které jsou zvažovány pro aktivní učení, je široký, přibližně do 10 %. Jak se snižuje skóre spolehlivosti, například 40%, rozsah skóre klesá také, přibližně v rámci 4%.

V následující odpovědi JSON z dotazu na QnA Maker generateAnswer, skóre pro A, B a C jsou blízko a by být považovány za návrhy.

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

QnA Maker nebude vědět, která odpověď je nejlepší odpověď. Pomocí seznamu návrhů portálu QnA Maker vyberte nejlepší odpověď a znovu trénujte.


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Jak poskytnout explicitní zpětnou vazbu s train API

QnA Maker potřebuje explicitní zpětnou vazbu o tom, která z odpovědí byla nejlepší odpovědí. Jak je určena nejlepší odpověď, je na vás a může zahrnovat:

* Zpětná vazba od uživatelů, výběr jedné z odpovědí.
* Obchodní logika, jako je například určení přijatelného rozsahu skóre.
* Kombinace zpětné vazby od uživatelů a obchodní logiky.

Pomocí [rozhraní TRAIN API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train) odešlete správnou odpověď qnA makeru poté, co ji uživatel vybere.

## <a name="next-step"></a>Další krok

> [!div class="nextstepaction"]
> [Dotaz na znalostní bázi](query-knowledge-base.md)