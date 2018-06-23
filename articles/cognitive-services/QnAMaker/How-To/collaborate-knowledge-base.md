---
title: Spolupráce na znalostní báze - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Jak spolupracovat na QnA Maker znalostní báze
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: e18d656236276595fc5186a6656349bf28974ead
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343221"
---
# <a name="collaborate-on-your-knowledge-base"></a>Spolupracujte na znalostní báze

QnA Maker umožňuje více uživatelů ke spolupráci na bázi knowledge base. Tato funkce je k dispozici s Azure [řízení přístupu na základě Role](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure). 

Proveďte následující postup služby QnA Maker můžete sdílet s někým:

1. Přihlaste se k portálu Azure a přejděte do programu QnA Maker zdroje.

    ![Seznam zdrojů QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. Přejděte na **řízení přístupu (IAM)** kartě.

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. Vyberte **Přidat**.

    ![Přidat QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. Vyberte **vlastníka** nebo **Přispěvatel** role.

    ![Přidání role QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. Zadejte e-mailu, kterou chcete sdílet a kliknutím na tlačítko Uložit.

    ![QnA Maker IAM přidat e-mailu](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Nyní když osoba, která jste sdíleli služby QnA Maker prostřednictvím, zaznamená do [QnA Maker portál](https://qnamaker.ai) uvidí znalostních bází v dané služby.

Pamatujte si, že se konkrétní znalostní bázi knowledge base v službě QnA Maker nelze sdílet. Pokud chcete podrobnějšímu řízení přístupu, vezměte v úvahu distribucí znalostních bází mezi různými QnA Maker službami.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Testování znalostní bázi knowledge base](./test-knowledge-base.md)
