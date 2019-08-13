---
title: Spolupráce na znalostní bázi Knowledge Base – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker umožňuje více lidem spolupracovat na znalostní bázi. Tato funkce je k dispozici u Access Control na základě rolí Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: diberry
ms.openlocfilehash: 9c5398ff7cb31698db3d4a798b6a082f9e74b99b
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955141"
---
# <a name="collaborate-on-your-knowledge-base"></a>Spolupráce na znalostní bázi

QnA Maker umožňuje více lidem spolupracovat na znalostní bázi. Tato funkce je k dispozici u [Access Control na základě rolí](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)Azure. 

Pomocí následujících kroků můžete svoji QnA Maker službu sdílet s někým:

1. Přihlaste se k Azure Portal a Projděte si prostředek QnA Maker.

    ![Seznam prostředků nástroje QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. Přejít na kartu **Access Control (IAM)** .

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. Vyberte **Přidat**.

    ![QnA Maker IAM – přidat](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. Vyberte **vlastníka** nebo roli **Přispěvatel** . Pomocí Access Control na základě rolí nelze udělit přístup jen pro čtení. Role vlastníka a přispěvatele má oprávnění ke čtení i zápisu pro QnA Maker služby.

    ![QnA Maker přidat roli IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. Zadejte e-mail, se kterým chcete sdílet, a stiskněte Uložit.

    ![QnA Maker IAM – Přidání e-mailu](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Když teď osoba, se kterou sdílíte službu QnA Maker, přihlásíte se k [portálu QnA maker](https://qnamaker.ai) , uvidí všechny znalostní báze v této službě.

Pamatujte, že v QnA Maker službě nemůžete sdílet konkrétní znalostní bázi. Pokud chcete podrobnější řízení přístupu, zvažte distribuci znalostní báze napříč různými QnA Maker službami.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Testování znalostní báze](./test-knowledge-base.md)
