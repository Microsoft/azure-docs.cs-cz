---
title: Skóre spolehlivosti – QnA Maker
titleSuffix: Azure Cognitive Services
description: Skóre spolehlivosti určuje míru shody mezi uživatele otázky a odpovědi vrácené.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 33da5cf5724b8314ce813f12eb077d9a15ec1b2a
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041519"
---
# <a name="confidence-score"></a>Skóre spolehlivosti

Skóre spolehlivosti určuje míru shody mezi uživatele otázky a odpovědi vrácené.

Pokud uživatelský dotaz je hledána obsah znalostní báze, může být více než jeden odpovědi vrácené. Odpovědi se vrátí seřazený podle sestupný skóre spolehlivosti.

Je skóre spolehlivosti mezi 0 a 100.

|Hodnota skóre|Spolehlivost|
|--|--|
|100|Přesnou shodu uživatele dotazu a dotaz KB|
|90|Shodovat s vysokou spolehlivostí - většinu slova|
|40-60|Veletrh spolehlivosti - odpovídat důležité slova|
|10|S nízkou spolehlivostí - důležité slova se neshodují|
|0|Žádná shoda aplikace word|


## <a name="similar-confidence-scores"></a>Podobně jako skóre spolehlivosti
Když ukládat více odpovědí mají podobné skóre spolehlivosti, je pravděpodobné, že dotaz byl příliš obecný a proto shodné s stejnou pravděpodobnost, že s více odpovědí. Vystavět strukturu vaší maximálně lépe tak, aby každá entita QnA obsahuje různé záměr.


## <a name="improving-confidence-scores"></a>Zvýšení skóre spolehlivosti
Pro zvýšení skóre spolehlivosti konkrétní odpovědi na dotaz uživatele, můžete přidat uživatelský dotaz ve znalostní bázi jako alternativní dotaz na odpověď.
   
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
