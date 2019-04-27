---
title: Zlepšení znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: Můžete aktivně učit a zlepšovat tak kvalitu znalostní báze Navrhněte alternativní dotazy založené na příspěvky uživatele, na vaše otázky a odpovědi spárování. Buď je přidáte do stávající dotazy nebo odmítat je zkontrolujete tyto návrhy. Znalostní báze se nezmění automaticky. Je nutné přijmout návrhy pro všechny změny se projeví. Tyto návrhy přidat dotazy, ale nechcete změnit nebo odebrat existující dotazy.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: diberry
ms.openlocfilehash: f59f281b1bf7fa2851ab7759a0167b5d39ef44c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61372659"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Aktivní studium slouží ke zlepšení znalostní báze

Můžete aktivně učit a zlepšovat tak kvalitu znalostní báze Navrhněte alternativní dotazy založené na příspěvky uživatele, na vaše otázky a odpovědi spárování. Buď je přidáte do stávající dotazy nebo odmítat je zkontrolujete tyto návrhy. 

Znalostní báze se nezmění automaticky. Návrhy, aby se změny projevily, musíte přijmout. Tyto návrhy přidat dotazy, ale nechcete změnit nebo odebrat existující dotazy.

## <a name="what-is-active-learning"></a>Co je aktivní učení?

Nástroj QnA Maker se učí novou otázku variace implicitní a explicitní odeslání zpětné vazby.
 
* Implicitní zpětná vazba – klasifikátor znalost, kdy otázku uživatele obsahuje více odpovědi s skóre, která jsou velmi podobné a to považuje za zpětnou vazbu. 
* Explicitní zpětnou vazbu – při více odpovědi s malou kolísání skóre, které jsou vráceny z ve znalostní bázi, klientská aplikace žádá uživatele, které otázka je správné otázky. Explicitní zpětné vazby uživatele se odešle do nástroje QnA Maker pomocí rozhraní API pro trénování. 

Některé z metod poskytuje klasifikátor s podobné dotazy, které jsou v clusteru.

## <a name="how-active-learning-works"></a>Jak active learning funguje

Aktivní učení se aktivuje podle skóre nejčastější několik odpovědi vrácené QnA Maker pro libovolný daný dotaz. Pokud skóre rozdíly ležet v rozsahu malé, pak dotazu je považován za možný výskyt _návrh_ pro všechny možné odpovědi. 

Všechny návrhy jsou Clusterované společně odpovědným a nejčastějších návrhů pro alternativní otázky se zobrazují na základě četnosti konkrétní dotazů koncovými uživateli. Aktivní učení nabízí nejlepší možný návrhů v případech, kde se zobrazuje koncové body přiměřené množství a různorodost dotazů na využití.

Když jsou Clusterované 5 nebo další podobné dotazy, každých 30 minut, nástroj QnA Maker navrhuje dotazy založené na uživatelích do znalostní báze knowledge base návrháře přijmout nebo odmítnout.

Jakmile dotazy jsou navržené v portál QnA Maker, budete muset zkontrolovat a přijmout nebo odmítnout tyto návrhy. 

## <a name="upgrade-your-version-to-use-active-learning"></a>Upgrade verze pro použití aktivního učení

Aktivní učení se podporuje v modulu runtime verze 4.4.0 a vyšší. Pokud se znalostní báze vytvořil v dřívější verzi, [upgrade vašeho prostředí runtime](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates) pro použití této funkce. 

## <a name="best-practices"></a>Osvědčené postupy

Osvědčené postupy při používání active learning, naleznete v tématu [osvědčené postupy](../Concepts/best-practices.md#active-learning).

## <a name="score-proximity-between-knowledge-base-questions"></a>Skóre vzdálenost mezi dotazy znalostní báze

Při vysoce jistí, jako je například 80 % na otázku skóre jsou široké, přibližně v rámci 10 % rozsahu skóre, které jsou považovány za pro aktivní učení. Podle skóre spolehlivosti sníží, jako je například 40 % rozsahu skóre, které se také snižuje přibližně v rámci 4 %. 

Algoritmů a určit blízkých výrazů není jednoduchý výpočet. Oblasti v předchozích ukázkách nejsou určeny vyřešit ale by měla sloužit jako vodítko k pochopení dopadu pouze algoritmus.

## <a name="turn-on-active-learning"></a>Zapnout aktivní učení

Aktivní učení je vypnuto ve výchozím nastavení. Zapněte navrhované dotazy. 

1. Vyberte **publikovat** publikovat znalostní báze. Aktivní učení dotazy jsou shromážděná z GenerateAnswer předpovědi koncový bod rozhraní API pouze. Dotazy na testovací podokno portálu Qna Maker nemají vliv na aktivní učení.

1. Chcete-li aktivní učení na platformě, klikněte na vaše **název**, přejděte na stránku [ **nastavení služby** ](https://www.qnamaker.ai/UserSettings) portálu QnA Maker v pravém horním rohu.  

    ![Zapněte aktivně učit alternativy navrhované otázky ze stránky nastavení služby. Vyberte své uživatelské jméno v pravé horní nabídce a potom vyberte nastavení služby.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Najít službu QnA Maker, a potom přepnout **aktivně učit**. 

    [![Na stránce nastavení služby zapněte funkci aktivně učit. Pokud nejste schopni Přepnout funkci, budete muset upgradovat vaší služby.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    Jednou **aktivně učit** je povoleno, ve znalostní bázi navrhuje nové otázky v pravidelných intervalech podle uživatel odeslal dotazy. Můžete zakázat **aktivně učit** přepnutím nastavení znovu.

## <a name="add-active-learning-suggestion-to-knowledge-base"></a>Přidat aktivně učit návrh znalostní báze

1. Chcete-li zobrazit navržené otázky, na **upravit** znalostní báze stránce **zobrazit návrhy**. 

    [![V části upravit na portálu vyberte Zobrazit návrhy, chcete-li zobrazit aktivně učit nový dotaz alternativy.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtrovat ve znalostní bázi s páry otázek a odpovědí zobrazit pouze návrhy tak, že vyberete **filtrovat podle návrhy**.

    [![Chcete-li zobrazit pouze aktivně učit alternativy navrhované otázky, použijte filtr přepnutím návrhy.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1.  Každý oddíl otázku pomocí návrhů se zobrazí nové otázky se značkou zaškrtnutí `✔` , tak, aby přijímal otázku nebo `x` odmítnout návrhy. Vyberte zaškrtávací políčko, chcete-li přidat otázku. 

    [![Vyberte nebo odmítnout aktivně učit navrhované otázky alternativy tak, že vyberete red odstranění značky nebo zelená značka zaškrtnutí.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Můžete přidat nebo odstranit _všechny návrhy_ tak, že vyberete **přidejte všechny** nebo **odmítnout všechny**.

1. Vyberte **uložit a jejich trénování** a uložte změny do znalostní báze.

1. Vyberte **publikovat** k povolení změn bude k dispozici z rozhraní API GenerateAnswer.

    Když jsou Clusterované 5 nebo další podobné dotazy, každých 30 minut, nástroj QnA Maker navrhuje dotazy založené na uživatelích do znalostní báze knowledge base návrháře přijmout nebo odmítnout.

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

Když uživatel vybere jednu z existujících otázek, klientská aplikace odešle jako zpětné vazby pomocí rozhraní API nástroje QnA Maker trénování podle volby uživatele. Tato zpětná vazba se dokončí active learning smyčku zpětné vazby. 

Použití [ukázka Azure Bot](https://aka.ms/activelearningsamplebot) zobrazíte aktivně učit ve scénáři začátku do konce.

## <a name="train-api"></a>Rozhraní API trénování

Se požadavek POST trénovat na základě modelu rozhraní API nástroje QnA Maker zpětnou vazbu aktivně učit neposílají. Podpis rozhraní API je:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Vlastnost požadavku HTTP|Název|Type|Účel|
|--|--|--|--|
|Parametr trasa adresy URL|ID znalostní báze|string|Identifikátor GUID pro znalostní báze.|
|Host subdomain|Název prostředku QnA maker|string|Název hostitele pro váš nástroj QnA Maker ve vašem předplatném Azure. Toto je k dispozici na stránce nastavení po publikování znalostní báze. |
|Hlavička|Typ obsahu|string|Typ média textu odeslaného do rozhraní API. Výchozí hodnota je: `application/json`|
|Hlavička|Autorizace|string|Klíče vašeho koncového bodu (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Tělo POST|JSON – objekt|JSON|Zpětná vazba školení|

Text JSON má několik nastavení:

|Vlastnost text JSON|Type|Účel|
|--|--|--|--|
|`feedbackRecords`|pole|Seznam zpětnou vazbu.|
|`userId`|string|ID uživatele osoby, která přijímá navržené otázky. Formát ID uživatele je na vás. Například e-mailová adresa může být platné ID uživatele ve vaší architektuře. Volitelné.|
|`userQuestion`|string|Přesný text otázky. Povinná hodnota.|
|`qnaID`|číslo|ID otázku, součástí [GenerateAnswer odpovědi](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Příklad text JSON vypadá takto:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

Úspěšná odpověď vrátí stav 204 a bez těla odpovědi JSON. 

<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Aktivní učení se uloží exportované znalostní báze

Když se vaše aplikace může aktivně učit povolené a exportovat do aplikace `SuggestedQuestions` sloupec v souboru tsv uchovává data aktivně učit. 

`SuggestedQuestions` Sloupec je objekt JSON informací o implicitní (`autosuggested`) a explicitní (`usersuggested`) zpětnou vazbu. Příkladem tohoto objektu JSON pro jeden uživatel odeslal otázku `help` je:

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

Když tuto aplikaci znovu naimportujete, active learning i nadále shromažďovat informace a doporučení návrhy pro znalostní báze. 

## <a name="next-steps"></a>Další postup
 
> [!div class="nextstepaction"]
> [Pomocí rozhraní API GenerateAnswer metadat](metadata-generateanswer-usage.md)
