---
title: Správa - kognitivní služby Microsoft klíčů | Microsoft Docs
titleSuffix: Azure
description: Jak spravovat klíče QnA Maker
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: b402187f4949dac34fa476648c81b980ba3efc96
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343263"
---
# <a name="key-management"></a>Správa klíčů

Služby QnA Maker, na které se zabývá dva druhy klíče, **předplatné klíče** a **koncový bod klíče**.

![Správa klíčů](../media/qnamaker-how-to-key-management/key-management.png)

1. **Předplatné klíče**: tyto klíče se používají pro přístup k [QnA Maker služba správy rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff). Tato rozhraní API umožňují provádět různé operace CRUD znalostní báze.  

2. **Koncový bod klíče**: tyto klíče se používají pro přístup ke koncovému bodu znalostní báze knowledge base získat odpověď pro otázku uživatele. V kódu robota/aplikace chat využívajícího službu QnA Maker obvykle využije tento koncový bod.
 
## <a name="subscription-keys"></a>Předplatné klíče
Můžete zobrazit a resetovat vaše předplatné klíče z portálu Azure, kde jste vytvořili QnA Maker prostředků. 
1. Přejděte k prostředku QnA Maker na portálu Azure.

    ![Seznam zdrojů QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Přejděte na **klíče**.

    ![klíč předplatného](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Koncový bod klíče

Koncový bod klíče se dají spravovat z [QnA Maker portál](https://qnamaker.ai).

1. Přihlaste se k [QnA Maker portál](https://qnamaker.ai)a přejděte na **Správa klíčů**.

    ![klíč koncového bodu](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Zobrazení nebo resetovat vaše klíče.

    ![koncový bod Správce klíčů](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Pokud si myslíte, že byly prozrazeny, aktualizujte klíče. Může to vyžadovat odpovídající změny kódu aplikace nebo robota.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření znalostní báze v jiném jazyce](./language-knowledge-base.md)
