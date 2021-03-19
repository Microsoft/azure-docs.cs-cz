---
title: Správa uživatelských účtů v Azure API Management | Microsoft Docs
description: Naučte se vytvářet nebo zvát uživatele v Azure API Management. Zobrazit další prostředky, které se použijí po vytvoření vývojářského účtu.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 92e032eb104835788f515cc7800fe5dacfa8adaa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88566127"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Správa uživatelských účtů ve službě Azure API Management

V API Management jsou vývojáři uživateli rozhraní API, které vystavíte pomocí API Management. V této příručce se dozvíte, jak vytvořit a pozvat vývojáře k používání rozhraní API a produktů, které jim zpřístupníte s vaší instancí API Management. Informace o tom, jak spravovat uživatelské účty prostřednictvím kódu programu, najdete v dokumentaci k [entitě uživatele](/rest/api/apimanagement/2019-12-01/user) v odkazu [API Management REST](/rest/api/apimanagement/) .

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Předpoklady

Dokončení úloh v tomto článku: [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-new-developer"></a><a name="create-developer"> </a>Vytvořit nového vývojáře

Chcete-li přidat nového uživatele, postupujte podle kroků v této části:

1. Vyberte kartu **Uživatelé** vlevo na obrazovce.
2. Stiskněte **+ Přidat**.
3. Zadejte příslušné informace pro uživatele.
4. Stiskněte **Přidat**.

    ![Přidání nového uživatele](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

Ve výchozím nastavení jsou nově vytvořené účty pro vývojáře **aktivní** a přidruženy ke skupině **Developers** . Účty pro vývojáře, které jsou v **aktivním** stavu, se dají použít pro přístup ke všem rozhraním API, pro která mají předplatná. Chcete-li přidružit nově vytvořeného vývojáře k dalším skupinám, přečtěte si téma [jak přidružit skupiny k vývojářům][How to associate groups with developers].

## <a name="invite-a-developer"></a><a name="invite-developer"> </a>Pozvání vývojáře
Pokud chcete pozvat vývojáře, postupujte podle kroků v této části:

1. Vyberte kartu **Uživatelé** vlevo na obrazovce.
2. Stiskněte klávesu **+ Pozvánka**.

Zobrazí se potvrzovací zpráva, ale nově pozvaný vývojář se v seznamu nezobrazí, dokud nepřijme pozvánku. 

Po pozvání vývojáře se vývojářovi pošle e-mail. Tento e-mail je vygenerován pomocí šablony a je přizpůsobitelný. Další informace najdete v tématu [Konfigurace e-mailových šablon][Configure email templates].

Po přijetí pozvánky se účet aktivuje.

## <a name="deactivate-or-reactivate-a-developer-account"></a><a name="block-developer"></a> Deaktivuje nebo znovu aktivuje vývojářský účet.

Ve výchozím nastavení jsou **aktivní** nově vytvořené nebo pozvaní účty pro vývojáře. Chcete-li deaktivovat vývojářský účet, klikněte na tlačítko **blokovat**. Chcete-li znovu aktivovat blokovaný vývojářský účet, klikněte na tlačítko **aktivovat**. Blokovaný vývojářský účet nemá přístup k portálu pro vývojáře ani volání libovolných rozhraní API. Pokud chcete odstranit uživatelský účet, klikněte na **Odstranit**.

Pokud chcete uživatele zablokovat, postupujte podle následujících kroků.

1. Vyberte kartu **Uživatelé** vlevo na obrazovce.
2. Klikněte na uživatele, kterého chcete zablokovat.
3. Stiskněte **blokovat**.

## <a name="reset-a-user-password"></a>Resetování hesla uživatele

Chcete-li programově pracovat s uživatelskými účty, přečtěte si dokumentaci k entitě uživatele v tématu [API Management REST API](/rest/api/apimanagement/) reference. Chcete-li obnovit heslo uživatelského účtu na určitou hodnotu, můžete použít operaci [aktualizovat uživatele](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity#UpdateUser) a zadat požadované heslo.

## <a name="next-steps"></a><a name="next-steps"> </a>Další kroky
Jakmile se vytvoří účet pro vývojáře, můžete ho přidružit k rolím a přihlásit se k odběru produktů a rozhraní API. Další informace najdete v tématu [Vytvoření a používání skupin][How to create and use groups].

[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png


[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates
