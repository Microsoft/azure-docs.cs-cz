---
title: Použití aktivního učení s využitím znalostní báze Knowledge Base – QnA Maker
description: Naučte se zlepšovat kvalitu znalostní báze s aktivním učením. Zkontrolujte, přijměte nebo odmítněte, přidejte bez odebrání nebo změny existujících otázek.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: ffc1a0a401de634c1932b9653f231b377c4f154e
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591918"
---
# <a name="active-learning"></a>Aktivní učení

Funkce _aktivní návrhy kurzů_ vám umožní zdokonalit kvalitu znalostní báze tím, že navrhuje alternativní otázky na základě počtu uživatelů, které odpovídají vašemu páru dotazů a odpovědí. Tyto návrhy si můžete prohlédnout, buď je přidáte do existujících otázek, nebo je odmítnete.

Vaše znalostní báze se nemění automaticky. Aby se změny projevily, musíte přijmout návrhy. Tyto návrhy přidávají otázky, ale nemění ani neodstraňují stávající otázky.

## <a name="what-is-active-learning"></a>Co je aktivní učení?

QnA Maker se učí nové variace otázek s implicitní a explicitní zpětnou vazbou.

* [Implicitní zpětná vazba](#how-qna-makers-implicit-feedback-works) – seznámení s tím, že má uživatelská otázka více odpovědí s výsledky, které jsou velmi blízko a považuje se za zpětnou vazbu. K tomu nemusíte nic dělat.
* [Explicitní názory](#how-you-give-explicit-feedback-with-the-train-api) – Pokud se ve znalostní bázi Knowledge Base vrátí více odpovědí s malým kolísáním skóre, klientská aplikace se zeptá, který dotaz je na správnou otázku. Explicitní zpětná vazba uživatele se pošle QnA Maker s využitím [rozhraní API pro vlaky](../How-To/improve-knowledge-base.md#train-api).

Obě metody poskytují pořadí s podobnými dotazy, které jsou v clusteru.

## <a name="how-active-learning-works"></a>Jak funguje aktivní učení

Aktivní učení se aktivuje na základě skóre nejčastějších odpovědí vrácených QnA Maker. Pokud rozdíly v skóre mezi páry QnA, které odpovídají dotazu, leží v malém rozsahu, dotaz je považován za možný návrh (jako alternativní otázka) pro každý z možných párů QnA. Jakmile přijmete navrhovanou otázku pro určitý pár QnA, je odmítnut pro ostatní páry. Po přijetí návrhů musíte pamatovat na uložení a výuku.

Aktivní učení nabízí nejlepší možné návrhy v případech, kdy koncové body získávají přijatelné množství a různé dotazy na používání. Když je 5 nebo více podobných dotazů clusterovaných, každých 30 minut QnA Maker navrhuje dotazy založené na uživateli v Návrháři znalostní báze, aby je bylo možné přijmout nebo odmítnout. Všechny návrhy jsou seskupené podle podobnosti a na základě frekvence konkrétních dotazů koncovými uživateli se zobrazují nejdůležitější návrhy na alternativní otázky.

Po navržení otázek na portálu QnA Maker musíte tyto návrhy projít a přijmout nebo odmítnout. Není k dispozici rozhraní API pro správu návrhů.

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

Použijte [rozhraní API pro vlak](/rest/api/cognitiveservices/qnamaker4.0/runtime/train) k odeslání správné odpovědi na QnA maker, až ho uživatel vybere.

## <a name="upgrade-runtime-version-to-use-active-learning"></a>Upgradovat verzi modulu runtime pro použití aktivního učení

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

Aktivní učení je podporované v modulu runtime verze 4.4.0 a vyšší. Pokud byla znalostní báze vytvořena v dřívější verzi, [upgradujte modul runtime](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) , aby tuto funkci používal.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

V QnA Maker spravovaném (ve verzi Preview), protože modul runtime je hostovaný pomocí samotné služby QnA Maker, není potřeba ručně upgradovat modul runtime.

---

## <a name="turn-on-active-learning-for-alternate-questions"></a>Zapnout aktivní učení pro alternativní otázky

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

Aktivní učení je ve výchozím nastavení vypnuté. Zapněte ho, aby se zobrazily navržené otázky. Po zapnutí aktivního učení je potřeba odeslat informace z klientské aplikace do QnA Maker. Další informace najdete v tématu [architektonický tok pro použití GenerateAnswer a rozhraní API pro učení z robota](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Vyberte **publikovat** a publikujte znalostní bázi. Aktivní výukové dotazy se shromažďují jenom z koncového bodu předpovědi rozhraní GenerateAnswer API. Dotazy do podokna test na portálu QnA Maker neovlivňují aktivní učení.

1. Pokud chcete zapnout aktivní učení na portálu QnA Maker, přečtěte si v pravém horním rohu, vyberte své **jméno** a pak přejít na [**nastavení služby**](https://www.qnamaker.ai/UserSettings).

    ![Na stránce nastavení služby zapněte možnosti pro navrhované otázky pro aktivní učení. V pravé horní nabídce vyberte své uživatelské jméno a pak vyberte nastavení služby.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Vyhledejte službu QnA Maker a potom přepněte **aktivní učení**.

    > [!div class="mx-imgBorder"]
    > [![Na stránce nastavení služby přepněte na funkci aktivní učení. Pokud nemůžete funkci zapnout, možná budete muset upgradovat službu.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > Přesná verze na předchozím obrázku se zobrazí jenom jako příklad. Vaše verze se může lišit.

    Po povolení **aktivního učení** znalostní báze v pravidelných intervalech navrhuje nové otázky na základě dotazů odeslaných uživatelem. Můžete zakázat **aktivní učení** tím, že znovu přepnete nastavení.
    
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

Ve výchozím **nastavení je aktivní učení v QnA maker** spravovaném (ve verzi Preview). Chcete-li zobrazit navrhované alternativní otázky, [použijte možnosti zobrazení](../How-To/improve-knowledge-base.md#view-suggested-questions) na stránce Upravit.

---

## <a name="review-suggested-alternate-questions"></a>Kontrola navrhovaných alternativních otázek

[Přečtěte si alternativní navrhované otázky](improve-knowledge-base.md) na stránce pro **Úpravy** jednotlivých znalostí znalostní báze.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření znalostní báze](./manage-knowledge-bases.md)
