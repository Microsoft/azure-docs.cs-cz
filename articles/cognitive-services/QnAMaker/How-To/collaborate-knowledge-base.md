---
title: Spolupráce na znalostní bázi Knowledge Base – QnA Maker
description: QnA Maker umožňuje více lidem spolupracovat na znalostní bázi. Tato funkce je k dispozici u Access Control na základě rolí Azure.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 1a2908b4b65017f427682627ce5d83b186956a58
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650784"
---
# <a name="collaboration-with-authors-and-editors"></a>Spolupráce s autory a editory

Spolupráce je poskytována na úrovni prostředků QnA Maker, která umožňuje omezit přístup spolupracovníka na základě role spolupracovníka. Přečtěte si další informace o QnA Maker [konceptech](../Concepts/role-based-access-control.md)ověřování spolupracovníka.

## <a name="add-role-based-access-rbac-to-your-qna-maker-resource"></a>Přidání přístupu na základě role (RBAC) do prostředku QnA Maker

QnA Maker umožňuje více lidem spolupracovat na všech základech znalostní báze ve stejném prostředku QnA Maker. Tato funkce je k dispozici u [Access Control na základě rolí](../../../active-directory/role-based-access-control-configure.md)Azure.

## <a name="access-at-the-qna-maker-resource-level"></a>Přístup na úrovni prostředků QnA Maker

V QnA Maker službě nemůžete sdílet konkrétní znalostní bázi. Pokud chcete podrobnější řízení přístupu, zvažte distribuci znalostní báze mezi různými QnA Maker prostředky a pak přidejte role do každého prostředku.

## <a name="add-role-to-resource"></a>Přidat roli do prostředku

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Přidat uživatelský účet do prostředku QnA Maker

V následujících krocích se používá role spolupracovníka, ale kterákoli z [rolí](../reference-role-based-access-control.md) se dá přidat pomocí těchto kroků.

1. Přihlaste se k webu [Azure](https://portal.azure.com/) Portal a Projděte si prostředek QnA maker.

    ![Seznam prostředků QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Přejít na kartu **Access Control (IAM)** .

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Vyberte **Přidat**.

    ![QnA Maker IAM – přidat](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Vyberte roli z následujícího seznamu:

    |Role|
    |--|
    |Vlastník|
    |Přispěvatel|
    |Čtecí modul QnA Maker|
    |Editor QnA Maker|
    |Cognitive Services uživatel|

    ![QnA Maker přidat roli IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Zadejte e-mailovou adresu uživatele a stiskněte **Uložit**.

    ![QnA Maker IAM – Přidání e-mailu](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Když někdo nasdílel službu QnA Maker pomocí protokolů na [portál QnA maker](https://qnamaker.ai), uvidí všechny databáze znalostí v této službě na základě jejich role.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Testování znalostní báze](./test-knowledge-base.md)

Další informace o spolupráci:
* Řízení přístupu na základě role v [Azure](../../../active-directory/role-based-access-control-configure.md)
* QnA Maker [koncepce](../Concepts/role-based-access-control.md) řízení přístupu na základě rolí
