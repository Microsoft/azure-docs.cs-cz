---
title: Správa prostředků a klíč – QnA Maker
titleSuffix: Azure Cognitive Services
description: Vaše služba QnA Maker se zabývá dvěma druhy klíče, klíče předplatného a klíče koncového bodu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b0b82f37b76a6bca6d84a05bc48b7ae9986fb76b
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967681"
---
# <a name="how-to-manage-keys-in-qna-maker"></a>Správa klíčů v nástroje QnA Maker

Vaše služba QnA Maker se zabývá dvěma druhy klíče, **klíče předplatného** a **klíče koncového bodu**.

![Správa klíčů](../media/qnamaker-how-to-key-management/key-management.png)

1. **Klíče**předplatného: Tyto klíče se používají pro přístup k [rozhraním API služby QnA maker Management](https://go.microsoft.com/fwlink/?linkid=2092179). Tato rozhraní API umožňují provádět úpravy znalostní báze.  

2. **Klíče koncového bodu**: Tyto klíče se používají pro přístup ke koncovému bodu znalostní báze, aby se zobrazila odpověď pro otázku uživatele. Tento koncový bod obvykle použijete ve vaší robotice nebo v kódu klientské aplikace, který využívá službu QnA Maker.
 
## <a name="subscription-keys"></a>Klíčů předplatného
Můžete zobrazit a obnovit klíče předplatného z portálu Azure, ve které jste vytvořili prostředek QnA Maker. 
1. Přejdete k prostředku QnA Maker na webu Azure Portal.

    ![Seznam prostředků nástroje QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Přejděte na **klíče**.

    ![Klíč předplatného](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Klíče koncového bodu

Klíče koncového bodu je možné spravovat z [portál QnA Maker](https://qnamaker.ai).

1. Přihlaste se k [portálu QnA maker](https://qnamaker.ai), přejděte do svého profilu a klikněte na **nastavení služby**.

    ![Klíč koncového bodu](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Zobrazení nebo resetovat vaše klíče.

    ![klíče správce koncového bodu](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Pokud se domníváte, že jsou ohrožené, aktualizujte svoje klíče. To může vyžadovat odpovídající změny vaší klientské aplikace nebo kódu bot.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření znalostní báze v jiném jazyce](./language-knowledge-base.md)
