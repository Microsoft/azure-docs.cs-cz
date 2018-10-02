---
title: Skóre spolehlivosti – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: S vysvětlením, skóre spolehlivosti
services: cognitive-services
author: tulasim88
manager: pchoudh
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 09/27/2018
ms.author: tulasim
ms.openlocfilehash: e878da5f6741b1a4c31874af05b7a37f6dee21df
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2018
ms.locfileid: "47586219"
---
# <a name="confidence-score"></a>Skóre spolehlivosti
Když uživatelský dotaz je hledána znalostní báze, vrátí QnA Maker příslušné odpovědi, spolu s skóre spolehlivosti. Toto skóre označuje jistotu, že odpověď je doprava odpovídá dotazu daného uživatele. 

Skóre spolehlivosti je číslo mezi 0 a 100. Skóre 100 je pravděpodobně přesnou shodu, zatímco skóre 0 znamená, která nebyla nalezena žádná odpovídající odpověď. Čím vyšší skóre – větší jistotou v odpovědi na dotaz. Pro daný dotaz může být vrátil více odpovědí. V takovém případě odpovědi se vrátí v pořadí podle snižuje pravděpodobnost.

V následujícím příkladu vidíte jednu entitu QnA 2 otázek. 


![Ukázka QnA pár](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

Pro výše uvedeného příkladu – můžete očekávat, že skóre jako rozsah skóre ukázka níže – pro různé typy uživatelských dotazů:


![Klasifikátor rozsahu skóre](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


Následující tabulka uvádí typické přidruženou skóre spolehlivosti.

|Hodnota skóre|Význam skóre|Příklad dotazu|
|--|--|--|
|90 – 100|A téměř přesnou shodu uživatele dotazu a dotaz KB|"Provedené změny se neaktualizují v znalostní báze po publikování"|
|> 70|Vysokou spolehlivostí – obvykle dobrou odpověď, která úplně odpovědi na dotaz uživatele|"Jsem publikoval znalostní BÁZE, ale není prováděna"|
|50 - 70|Střední jistotou – obvykle velmi dobrou odpověď, která by měla obsahovat odpovědi hlavním záměrem uživatelský dotaz|"Měli uložit Moje aktualizace před můžu publikovat znalostní BÁZE?"|
|30 – 50|S nízkou spolehlivostí – obvykle související odpovědí, který odpovídá částečně záměru uživatele|"Co uložit a trénování dělá?"|
|< 30|Velmi nízkou spolehlivostí – obvykle neodpovídá dotaz uživatele, ale má některé odpovídající slova nebo fráze |"Kde si můžu přidat synonyma znalostní BÁZE"|
|0|Žádná shoda, proto se vrátí odpověď.|"Kolik službu stojí"|

## <a name="choose-a-score-threshold"></a>Zvolte prahové hodnoty skóre
V tabulce výše najdete skóre, které se očekává, že na většině znalostní báze. Ale protože každých KB se liší a má různé druhy slova, záměry a cíle, které doporučujeme test a vyberte prahovou hodnotu, které bude nejlépe vyhovuje. Je výchozí a doporučenou prahovou hodnotu, která by měla fungovat pro většinu znalostní báze **50**.

Pokud zvolíte, že vaše mezní hodnota, mějte na paměti rovnováhu mezi přesnost a pokrytí a upravit vaše mezní hodnota, na základě vašich požadavků.

- Pokud **přesnost** (nebo přesnosti) je důležité pro váš scénář, pak zvýšit vaše mezní hodnota. Tímto způsobem pokaždé, když se vrátí odpověď, bude mnohem více důvěrné velikosti písmen a mnohem vyšší pravděpodobnost, že hledá odpověď uživatele. V takovém případě může skončit opuštění další nezodpovězené dotazy. *Příklad:* Pokud provedete prahovou hodnotu **70**, můžete přijít o některé příklady nejednoznačný lajků "co je uložte a trénování?".

- Pokud **pokrytí** (nebo odvolání) je více důležité – a vy chcete odpovězte na tolik otázek, kolik nejvíce, i v případě částečné vztahu uživatele otázku – pak nižší prahová hodnota. To znamená, že může být více případů, kdy odpověď neodpovídá skutečné dotaz uživatele, ale poskytuje některé poněkud související odpovědí. *Příklad:* Pokud provedete prahovou hodnotu **30**, dáte velmi související odpovědi jako odpovídá na výše uvedeném příkladu, například dotazy pro "kde je možné upravovat znalostní BÁZE?"


## <a name="improve-confidence-scores"></a>Zvýšení skóre spolehlivosti
Pro zvýšení skóre spolehlivosti konkrétní odpovědi na dotaz uživatele, můžete přidat uživatelský dotaz ve znalostní bázi jako alternativní dotaz na odpověď.


## <a name="similar-confidence-scores"></a>Podobně jako skóre spolehlivosti
Když ukládat více odpovědí mají podobné skóre spolehlivosti, je pravděpodobné, že dotaz byl příliš obecný a proto shodné s stejnou pravděpodobnost, že s více odpovědí. Vystavět strukturu vaší maximálně lépe tak, aby každá entita QnA obsahuje různé záměr.


## <a name="confidence-score-differences"></a>Skóre rozdíly spolehlivosti
Skóre spolehlivosti odpověď může změnit zanedbatelně mezi testu a publikovanou verzi znalostní báze i v případě, obsah je stejný. Je to proto, že obsah testu a publikované znalostní báze jsou umístěné v různých indexů Azure Search.
Tady uvidíte jak [publikovat](../How-To/publish-knowledge-base.md) operace funguje.


## <a name="no-match-found"></a>Nebyla nalezena žádná odpovídající
Když dobré shoda nenajde ve klasifikátor, vrátí se skóre spolehlivosti 0,0 nebo "None" a výchozí odpověď je "Dobrá nebyla nalezena žádná odpovídající v KB". Můžete přepsat toto výchozí odpověď v kódu bot nebo aplikaci volání koncového bodu. Alternativně přepsání odpovědi můžete také nastavit v Azure a tím se změní na výchozí hodnoty pro všechny znalostních bází nasazených v konkrétní služba QnA Maker.

1. Přejděte [webu Azure portal](http://portal.azure.com) a přejděte do skupiny prostředků, který představuje službu QnA Maker, kterou jste vytvořili.

2. Kliknutím otevřete **služby App Service**.

    ![Přístup ke službě App service](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Klikněte na **nastavení aplikace** a upravit **DefaultAnswer** do požadovaného výchozí odpověď. Klikněte na **Uložit**.

    ![Změnit výchozí odpověď](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Restartujte službu App service

    ![Nástroj QnA Maker restartování služby App Service](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Podporované zdroje dat](./data-sources-supported.md)
## <a name="see-also"></a>Další informace najdete v tématech 
[Přehled služby QnA Maker](../Overview/overview.md)
