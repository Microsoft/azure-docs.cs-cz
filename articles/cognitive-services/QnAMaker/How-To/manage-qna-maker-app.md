---
title: Správa aplikace QnA Maker App-QnA Maker
description: QnA Maker umožňuje více lidem spolupracovat na znalostní bázi. QnA Maker nabízí možnost zlepšit kvalitu znalostní báze s aktivním učením. Jednu z nich můžete zkontrolovat, přijmout nebo odmítnout a přidat, aniž byste museli odebírat nebo měnit stávající otázky.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 652489e8de68d61707fd419843f0dbb2ffd83754
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "99987936"
---
# <a name="manage-qna-maker-app"></a>Správa aplikace QnA Maker

QnA Maker umožňuje spolupracovat s různými autory a editory obsahu tím, že nabízí možnost omezit přístup spolupracovníka na základě role spolupracovníka.
Přečtěte si další informace o [QnA maker konceptech ověřování spolupracovníka](../Concepts/role-based-access-control.md).

## <a name="add-azure-role-based-access-control-azure-rbac"></a>Přidání řízení přístupu na základě role Azure (Azure RBAC)

QnA Maker umožňuje více lidem spolupracovat na všech základech znalostní báze ve stejném prostředku QnA Maker. Tato funkce je k dispozici s [řízením přístupu na základě role Azure (Azure RBAC)](../../../role-based-access-control/role-assignments-portal.md).

## <a name="access-at-the-qna-maker-resource-level"></a>Přístup na úrovni prostředků QnA Maker

V QnA Maker službě nemůžete sdílet konkrétní znalostní bázi. Pokud chcete podrobnější řízení přístupu, zvažte distribuci znalostní báze mezi různými QnA Maker prostředky a pak přidejte role do každého prostředku.

## <a name="add-a-role-to-a-resource"></a>Přidání role k prostředku

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Přidat uživatelský účet do prostředku QnA Maker

V následujících krocích se používá role spolupracovníka, ale kterákoli z [rolí](../reference-role-based-access-control.md) se dá přidat pomocí těchto kroků.

1. Přihlaste se k webu [Azure](https://portal.azure.com/) Portal a Projděte si prostředek QnA maker.

    ![Seznam prostředků QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. Přejít na kartu **Access Control (IAM)** .

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. Vyberte **Přidat**.

    ![QnA Maker IAM – přidat](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. Vyberte roli z následujícího seznamu:

    |Role|
    |--|
    |Vlastník|
    |Přispěvatel|
    |Čtecí modul Cognitive Services QnA Maker|
    |Editor QnA Maker Cognitive Services|
    |Cognitive Services uživatel|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker přidat roli IAM.":::

1. Zadejte e-mailovou adresu uživatele a stiskněte **Uložit**.

    ![QnA Maker IAM – Přidání e-mailu](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>Zobrazit QnA Maker základy znalostní báze

Když někdo nasdílel službu QnA Maker pomocí protokolů na [portál QnA maker](https://qnamaker.ai), uvidí všechny databáze znalostí v této službě na základě jejich role.

Když vyberou znalostní bázi, jejich aktuální role na tomto QnA Maker prostředku se zobrazí vedle názvu znalostní báze.

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="Snímek znalostní báze v režimu úprav s názvem role v závorkách vedle názvu znalostní báze v levém horním rohu webové stránky.":::

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření znalostní báze](./manage-knowledge-bases.md)
