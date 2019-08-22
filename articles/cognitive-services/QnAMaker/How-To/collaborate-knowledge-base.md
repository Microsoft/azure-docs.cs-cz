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
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: d9c91d54fb357807682cd57f46b04454e4e2cfec
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876653"
---
# <a name="collaborate-on-your-knowledge-base"></a>Spolupráce na znalostní bázi

QnA Maker umožňuje více lidem spolupracovat na znalostní bázi. Tato funkce je k dispozici u [Access Control na základě rolí](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)Azure. 

Pomocí následujících kroků můžete svoji QnA Maker službu sdílet s někým:

1. Přihlaste se k Azure Portal a Projděte si prostředek QnA Maker.

    ![Seznam prostředků nástroje QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Přejít na kartu **Access Control (IAM)** .

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Vyberte **Přidat**.

    ![QnA Maker IAM – přidat](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Vyberte **vlastníka** nebo roli **Přispěvatel** . Pomocí Access Control na základě rolí nelze udělit přístup jen pro čtení. Role vlastníka a přispěvatele mají oprávnění ke čtení i zápisu pro službu QnA Maker.

    ![QnA Maker přidat roli IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Zadejte e-mailovou adresu uživatele a stiskněte **Uložit**.

    ![QnA Maker IAM – Přidání e-mailu](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Když někdo sdílí službu QnA Maker s nástrojem, přihlásí se k [portálu QnA maker](https://qnamaker.ai) , kde se zobrazí všechna znalostní báze v této službě.

Pamatujte, že v QnA Maker službě nemůžete sdílet konkrétní znalostní bázi. Pokud chcete podrobnější řízení přístupu, zvažte distribuci znalostní báze napříč různými QnA Maker službami.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Testování znalostní báze](./test-knowledge-base.md)
