---
title: Skóre spolehlivosti - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Vysvětlení spolehlivosti skóre
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: c97bdb7e57275ebd1893bc28248c4ecc6b35c153
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343257"
---
# <a name="confidence-score"></a>Skóre spolehlivosti

Skóre spolehlivosti označuje stupeň shody mezi uživatele otázku a odpověď vrácenou.

Pokud dotaz uživatele je nalezena shoda obsahu znalostní báze knowledge base, může být více než jeden odpovědi vrácené. Odpovědi se vrátí seřazený podle sestupný spolehlivosti skóre.

Skóre spolehlivosti je od 0 do 100.

|Hodnota skóre|Spolehlivost|
|--|--|
|100|Přesná shoda dotaz uživatele a dotaz KB|
|90|Shodovat s vysoce důvěryhodných - většinu slova|
|40-60|Odpovídat správného spolehlivosti - důležité slova|
|10|Nízká spolehlivosti - důležité: slova se neshodují.|
|0|Žádná shoda aplikace word|


## <a name="similar-confidence-scores"></a>Podobně jako spolehlivosti skóre
Pokud více odpovědí podobné skóre jistotu, je pravděpodobné, že dotaz byl příliš obecný a proto odpovídající pomocí stejnou pravděpodobnost, že s více odpovědi. Pokuste se struktury vaší QnAs lépe tak, aby každý QnA entita má odlišné záměr.


## <a name="improving-confidence-scores"></a>Vylepšení spolehlivosti skóre
Pokud chcete zvýšit důvěru skóre konkrétní odpověď na dotaz uživatele, můžete přidat dotaz uživatele do znalostní báze jako alternativní otázku na této odpovědi.
   
## <a name="confidence-score-differences"></a>Rozdíly skóre spolehlivosti
Skóre spolehlivosti odpověď může změnit zanedbatelně mezi test a publikovanou verzi znalostní báze i v případě, že obsah je stejný. Je to proto, že obsah test a publikovaná znalostní báze se nacházejí v různých indexů Azure Search.

Tady vidíte jak [publikování](../How-To/publish-knowledge-base.md) operace funguje.


## <a name="no-match-found"></a>Nebyla nalezena
Když pomocí ranker není nalezena žádná shoda funkční, je vrácen spolehlivosti skóre 0,0 nebo "Žádný" a "Žádné dobrý nalezena shoda v KB" je odpověď na výchozí. Můžete přepsat toto výchozí odpovědi v kódu robota nebo aplikace, volání koncový bod. Alternativně odpovědi přepsání lze nastavit i v Azure a tato operace změní na výchozí hodnoty pro všechny znalostních bází, které jsou nasazené v určité službě QnA Maker.

1. Přejděte na [portál Azure](http://portal.azure.com) a přejděte do skupiny prostředků, který představuje QnA Maker službu, kterou jste vytvořili.

2. Kliknutím otevřete **služby App Service**.

    ![Přístup k aplikaci služby](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Klikněte na **nastavení aplikace** a upravit **DefaultAnswer** pole požadované výchozí odpovědi. Klikněte na **Uložit**.

    ![Změňte výchozí odpověď](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Restartovat App service

    ![Restartování služby App Service QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Podporované zdroje dat](./data-sources-supported.md)

## <a name="see-also"></a>Další informace najdete v tématech 

[Přehled QnA Maker](../Overview/overview.md)
