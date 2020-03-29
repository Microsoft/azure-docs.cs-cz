---
title: Spolupráce na znalostní bázi - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker umožňuje více lidem spolupracovat na znalostní bázi. Tato funkce je k dispozici s řízením přístupu na základě rolí Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: diberry
ms.openlocfilehash: b5adc7ebacde056a141ca3b361b9eb2ea7900a39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75660745"
---
# <a name="collaborate-on-your-knowledge-base"></a>Spolupracujte na své znalostní bázi

QnA Maker umožňuje více lidem spolupracovat na všech znalostních bází ve stejném prostředku QnA Maker. Tato funkce je k dispozici s [řízením přístupu na základě rolí](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)Azure .

Chcete-li službu QnA Maker s někým sdílet, proveďte následující kroky:

1. Přihlaste se k portálu Azure a přejděte na prostředek QnA Maker.

    ![Seznam zdrojů qnA makeru](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Přejděte na kartu **Řízení přístupu (IAM).**

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Vyberte **Přidat**.

    ![QnA Maker IAM přidat](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Vyberte **roli Vlastník** nebo **Přispěvatel.** Přístup jen pro čtení nelze udělit prostřednictvím řízení přístupu na základě rolí. Role vlastníka a přispěvatele mají přístupová oprávnění pro čtení a zápis ke službě QnA Maker.

    ![QnA Maker IAM přidat roli](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Zadejte e-mailovou adresu uživatele a stiskněte **klávesu Save**.

    ![QnA Maker IAM přidat e-mail](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Když osoba, se kterou jste sdíleli službu QnA Maker, přihlásí se k [portálu QnA Maker,](https://qnamaker.ai) může zobrazit všechny znalostní báze v této službě.

Nezapomeňte, že určitou znalostní bázi nelze sdílet ve službě QnA Maker. Pokud chcete podrobnější řízení přístupu, zvažte distribuci znalostních bází napříč různými službami QnA Maker.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Testování znalostní báze](./test-knowledge-base.md)
