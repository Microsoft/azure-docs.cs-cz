---
title: Spolupráce na znalostní báze – Qna Maker
titleSuffix: Azure Cognitive Services
description: Nástroj QnA Maker umožňuje více uživatelům ke spolupráci na znalostní bázi. Tato funkce je součástí řízení přístupu.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: 52cac0d73dd75f400c9777b9cc9c0b3ca101df58
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45579798"
---
# <a name="collaborate-on-your-knowledge-base"></a>Spolupráce na znalostní báze

Nástroj QnA Maker umožňuje více uživatelům ke spolupráci na znalostní bázi. Tato funkce je součástí Azure [řízení přístupu na základě Role](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure). 

Proveďte následující kroky a sdílet s někým, vaše služba QnA Maker:

1. Přihlaste se k webu Azure portal a přejděte do prostředku nástroje QnA Maker.

    ![Seznam prostředků nástroje QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. Přejděte **řízení přístupu (IAM)** kartu.

    ![Nástroj QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. Vyberte **Přidat**.

    ![Přidat QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. Vyberte **vlastníka** nebo **Přispěvatel** role.

    ![Nástroj QnA Maker IAM přidat roli](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. Zadejte e-mailu, který chcete sdílet a kliknutím na tlačítko Uložit.

    ![Nástroj QnA Maker IAM přidat e-mailu](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Nyní když osoba sdílí vaší služby QnA Maker, protokoly do [portál QnA Maker](https://qnamaker.ai) vidí všechny znalostních bází v dané služby.

Nezapomeňte, že nemůžete sdílet určité znalostní báze v služba QnA Maker. Pokud chcete podrobnější řízení přístupu, vezměte v úvahu distribuce znalostních bází napříč různými službami QnA Maker.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Testování znalostní báze](./test-knowledge-base.md)
