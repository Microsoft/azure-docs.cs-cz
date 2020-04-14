---
title: Jak spravovat uživatelské účty ve správě rozhraní Azure API | Dokumenty společnosti Microsoft
description: Přečtěte si, jak ve správě rozhraní Azure API vytvořit nebo pozvat uživatele
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
ms.openlocfilehash: 672f145b638fa6b85f7139f2edb4fc41bb2855d9
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260968"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Správa uživatelských účtů ve službě Azure API Management

Ve správě rozhraní API jsou vývojáři uživateli rozhraní API, která zveřejňujete pomocí správy rozhraní API. Tato příručka ukazuje, jak vytvořit a pozvat vývojáře k použití rozhraní API a produkty, které jim zpřístupnit s vaší instance správy rozhraní API. Informace o programové správě uživatelských účtů naleznete v dokumentaci k [entitě uživatele](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/user) v odkazu [SPRÁVA ROZHRANÍ API REST.](/rest/api/apimanagement/)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Požadavky

Dokončení úkolů v tomto článku: [Vytvoření instance Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-new-developer"></a><a name="create-developer"> </a>Vytvoření nového vývojáře

Chcete-li přidat nového uživatele, postupujte podle pokynů v této části:

1. Vyberte kartu **Uživatelé** nalevo od obrazovky.
2. Stiskněte **klávesu +Přidat**.
3. Zadejte příslušné informace pro uživatele.
4. Stiskněte **Přidat**.

    ![Přidání nového uživatele](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

Ve výchozím nastavení jsou nově vytvořené vývojářské účty **aktivní**a jsou přidruženy ke skupině **Vývojáři.** Vývojářské účty, které jsou v **aktivním** stavu, lze použít pro přístup ke všem výhodám API, pro které mají předplatná. Pokud chcete nově vytvořeného vývojáře přidružit k dalším skupinám, přečtěte si informace o přidružení [skupin k vývojářům][How to associate groups with developers].

## <a name="invite-a-developer"></a><a name="invite-developer"> </a>Pozvání vývojáře
Pokud chcete pozvat vývojáře, postupujte podle pokynů v této části:

1. Vyberte kartu **Uživatelé** nalevo od obrazovky.
2. Stiskněte **klávesu +Pozvat**.

Zobrazí se potvrzovací zpráva, ale nově pozvaný vývojář se v seznamu zobrazí až po přijetí pozvánky. 

Když je vývojář pozván, je vývojáři odeslán e-mail. Tento e-mail je generován pomocí šablony a je přizpůsobitelný. Další informace naleznete v [tématu Konfigurace e-mailových šablon][Configure email templates].

Jakmile je pozvánka přijata, účet se stane aktivním.

## <a name="deactivate-or-reactivate-a-developer-account"></a><a name="block-developer"> </a> Deaktivace nebo opětovná aktivace vývojářského účtu

Ve výchozím nastavení jsou nově vytvořené nebo pozvané vývojářské účty **aktivní**. Chcete-li deaktivovat vývojářský účet, klepněte na tlačítko **Blokovat**. Chcete-li znovu aktivovat blokovaný vývojářský účet, klepněte na tlačítko **Aktivovat**. Blokovaný vývojářský účet nemá přístup k portálu pro vývojáře ani nevolá žádná uživatelská prostředí API. Chcete-li odstranit uživatelský účet, klepněte na tlačítko **Odstranit**.

Chcete-li blokovat uživatele, postupujte podle následujících kroků.

1. Vyberte kartu **Uživatelé** nalevo od obrazovky.
2. Klikněte na uživatele, kterého chcete blokovat.
3. Stiskněte **klávesu Block**.

## <a name="reset-a-user-password"></a>Resetování hesla uživatele

Programově pracovat s uživatelskými účty najdete v dokumentaci k entitě uživatele v odkazu rozhraní [REST API pro správu rozhraní API rozhraní API rozhraní API](/rest/api/apimanagement/) rozhraní API. Chcete-li obnovit heslo uživatelského účtu na určitou hodnotu, můžete použít operaci [Aktualizovat uživatele](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity#UpdateUser) a zadat požadované heslo.

## <a name="next-steps"></a><a name="next-steps"> </a>Další kroky
Po vytvoření vývojářského účtu ho můžete přidružit k rolím a přihlásit se k odběru produktů a rozhraní API. Další informace naleznete v tématu [Jak vytvořit a používat skupiny][How to create and use groups].

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
