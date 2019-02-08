---
title: Zlepšení znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: ''
author: diberry
manager: nitinme
displayName: active learning, suggestion, dialog prompt, train api, feedback loop, autolearn, auto-learn, user setting, service setting, services setting
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/29/2019
ms.author: diberry
ms.openlocfilehash: 6feb521aa47ca813b3067451c8c77111deb60e73
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55874001"
---
# <a name="use-active-learning-to-improve-knowledge-base"></a>Využití aktivního učení k vylepšení znalostní báze

Můžete aktivně učit a zlepšovat tak kvalitu znalostní báze Navrhněte alternativní dotazy založené na příspěvky uživatele, na vaše otázky a odpovědi spárování. Buď je přidáte do stávající dotazy nebo odmítat je zkontrolujete tyto návrhy. 

Znalostní báze se nezmění automaticky. Návrhy, aby se změny projevily, musíte přijmout. Tyto návrhy přidat dotazy, ale nechcete změnit nebo odebrat existující dotazy.

## <a name="active-learning"></a>Aktivní vzdělávání

Nástroj QnA Maker se učí novou otázku variace implicitní a explicitní odeslání zpětné vazby.
 
* Implicitní zpětná vazba – klasifikátor znalost, kdy otázku uživatele obsahuje více odpovědi s skóre, která jsou velmi podobné a to považuje za zpětnou vazbu. 
* Explicitní zpětnou vazbu – při více odpovědi s malou kolísání skóre, které jsou vráceny z ve znalostní bázi, klientská aplikace žádá uživatele, které otázka je správné otázky. Explicitní zpětné vazby uživatele se odešle do nástroje QnA Maker pomocí rozhraní API pro trénování. 

Některé z metod poskytuje klasifikátor s podobné dotazy, které jsou v clusteru.

Když jsou Clusterované podobné dotazy, navrhuje nástroje QnA Maker dotazy založené na uživatelích do znalostní báze knowledge base návrháře přijmout nebo odmítnout.

## <a name="how-active-learning-works"></a>Jak active learning funguje

Aktivní učení se aktivuje podle skóre nejčastější několik odpovědi vrácené QnA Maker pro libovolný daný dotaz. Pokud skóre rozdíly ležet v rozsahu malé, pak dotazu je považován za možný výskyt _návrh_ pro všechny možné odpovědi. 

Všechny návrhy jsou Clusterované společně odpovědným a nejčastějších návrhů pro alternativní otázky se zobrazují na základě četnosti konkrétní dotazů koncovými uživateli. Aktivní učení nabízí nejlepší možný návrhů v případech, kde se zobrazuje koncové body přiměřené množství a různorodost dotazů na využití.

## <a name="upgrade-version-to-use-active-learning"></a>Upgrade verze použití aktivního učení

Aktivní učení se podporuje v modulu runtime verze 4.4.0 a vyšší. Pokud se znalostní báze vytvořil v dřívější verzi, [upgrade vašeho prostředí runtime](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates) pro použití této funkce. 

## <a name="best-practices"></a>Osvědčené postupy

Osvědčené postupy při používání active learning, naleznete v tématu [osvědčené postupy](../Concepts/best-practices.md#active-learning).

## <a name="score-proximity-between-knowledge-base-questions"></a>Skóre vzdálenost mezi dotazy znalostní báze

Při vysoce jistí, jako je například 80 % na otázku skóre jsou široké, přibližně v rámci 10 % rozsahu skóre, které jsou považovány za pro aktivní učení. Podle skóre spolehlivosti sníží, jako je například 40 % rozsahu skóre, které se také snižuje přibližně v rámci 4 %. 

Algoritmů a určit blízkých výrazů není jednoduchý výpočet. Oblasti v předchozích ukázkách nejsou určeny vyřešit ale by měla sloužit jako vodítko k pochopení dopadu pouze algoritmus.

## <a name="turn-on-active-learning"></a>Zapnout aktivní učení

Aktivní učení je vypnuto ve výchozím nastavení. Zapněte navrhované dotazy. 

1. Chcete-li aktivní učení na platformě, klikněte na vaše **název**, přejděte na stránku [ **nastavení služby** ](https://www.qnamaker.ai/UserSettings) portálu QnA Maker v pravém horním rohu.  

    ![Na stránce nastavení služby přepnete na aktivní učení](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Najít službu QnA Maker, a potom přepnout **aktivně učit**. 

    [![Na stránce nastavení služby přepnete na aktivní učení](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    Jednou **aktivně učit** je povoleno, ve znalostní bázi navrhuje nové otázky v pravidelných intervalech podle uživatel odeslal dotazy. Můžete zakázat **aktivně učit** přepnutím nastavení znovu.

## <a name="add-active-learning-suggestion-to-knowledge-base"></a>Přidat aktivně učit návrh znalostní báze

1. Chcete-li zobrazit navržené otázky, na **upravit** znalostní báze stránce **zobrazit návrhy**. 

    [![Na stránce nastavení služby přepínací tlačítko Zobrazit návrhy](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtrovat ve znalostní bázi s páry otázek a odpovědí pouze zobrazit návrhy tak, že vyberete **filtrovat podle návrhy**.

    [![Na stránce nastavení služby filtrovat podle doporučení zobrazíte jenom na ty, otázek a odpovědí páry](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1.  Každý oddíl otázku pomocí návrhů se zobrazí nové otázky se značkou zaškrtnutí `✔` , tak, aby přijímal otázku nebo `x` odmítnout návrhy. Vyberte zaškrtávací políčko, chcete-li přidat otázku. 

    [![Na stránce nastavení služby přepnete na aktivní učení](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Můžete přidat nebo odstranit _všechny návrhy_ tak, že vyberete **přidejte všechny** nebo **odmítnout všechny**.

1. Vyberte **uložit a jejich trénování** a uložte změny do znalostní báze.


## <a name="determine-best-choice-when-several-questions-have-similar-scores"></a>Určení nejlepší volbou, kdy několik otázky mají podobné skóre

Pokud dotaz je příliš v skóre na další otázky, vývojář klientská aplikace může zvolit požádat o další informace.

### <a name="use-the-top-property-in-the-generateanswer-request"></a>Vlastnost horní v požadavku GenerateAnswer

Při odesílání dotaz do nástroje QnA Maker pro odpověď, součástí datové části JSON umožňuje pro vrácení více než jeden hlavní odpovědí:

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

Když klientská aplikace (jako je chatovací robot) obdrží odpověď, jsou vráceny nejdůležitější otázky týkající se 3:

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

### <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Klient aplikace zpracování při otázky mají podobné skóre

Klientská aplikace zobrazí všechny dotazy s možností na uživatele vyberte dotaz, který nejlépe reprezentuje svůj záměr. 

Když uživatel vybere jeden z existující dotazy. Nástroj QnA Maker se odešle zpětné vazby uživatelů [trénování](http://www.aka.ms/activelearningsamplebot) smyčky API pokračujte aktivně učit zpětnou vazbu. 

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

Další informace o tom, jak použití aktivního učení se [Azure Bot C# příklad](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-activelearning-bot)

## <a name="next-steps"></a>Další postup
 
> [!div class="nextstepaction"]
> [Použití rozhraní API nástroje QnA Maker](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
