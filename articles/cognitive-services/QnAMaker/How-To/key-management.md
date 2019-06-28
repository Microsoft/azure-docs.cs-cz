---
title: Správa prostředků a klíč – QnA Maker
titleSuffix: Azure Cognitive Services
description: Vaše služba QnA Maker se zabývá dvěma druhy klíče, klíče předplatného a klíče koncového bodu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b9be1db9be1d4dd57994e101c07ed430425a5912
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447423"
---
# <a name="how-to-manage-keys-in-qna-maker"></a>Správa klíčů v nástroje QnA Maker

Vaše služba QnA Maker se zabývá dvěma druhy klíče, **klíče předplatného** a **klíče koncového bodu**.

![Správa klíčů](../media/qnamaker-how-to-key-management/key-management.png)

1. **Klíče předplatného**: Tyto klíče se používají pro přístup [rozhraní API nástroje QnA Maker správy služeb](https://go.microsoft.com/fwlink/?linkid=2092179). Tato rozhraní API umožňují provádět úpravy znalostní báze.  

2. **Koncový bod klíče**: Tyto klíče se používají pro přístup ke koncovému bodu znalostní báze získat odpověď pro otázku uživatele. Obvykle použijete tento koncový bod v chatovací robot nebo kódem klientské aplikace, která využívá službu QnA Maker.
 
## <a name="subscription-keys"></a>Klíčů předplatného
Můžete zobrazit a obnovit klíče předplatného z portálu Azure, ve které jste vytvořili prostředek QnA Maker. 
1. Přejdete k prostředku QnA Maker na webu Azure Portal.

    ![Seznam prostředků nástroje QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Přejděte na **klíče**.

    ![Klíč předplatného](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Klíče koncového bodu

Klíče koncového bodu je možné spravovat z [portál QnA Maker](https://qnamaker.ai).

1. Přihlaste se k [portál QnA Maker](https://qnamaker.ai), přejděte na svůj profil a potom klikněte na tlačítko **nastavení služby**.

    ![Klíč koncového bodu](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Zobrazení nebo resetovat vaše klíče.

    ![klíče správce koncového bodu](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Pokud se domníváte, že jsou ohrožené, aktualizujte svoje klíče. To může vyžadovat odpovídající změny vaše klientská aplikace nebo kódu robota.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření znalostní báze v jiném jazyce](./language-knowledge-base.md)
