---
title: Jak spravovat uživatelské účty ve službě Azure API Management | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit nebo pozvat uživatele ve službě Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 827f8eb7491c6f64b275dee83345c29d51c0933d
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52442475"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Správa uživatelských účtů ve službě Azure API Management

Vývojáři ve službě API Management, jsou uživatelé rozhraní API, která zveřejníte pomocí služby API Management. Tato příručka ukazuje na tom, jak vytvořit a pozvat vývojáři používat rozhraní API a produktů, že jste jim zpřístupnili s vaší instancí služby API Management. Informace o správě uživatelských účtů prostřednictvím kódu programu, najdete v článku [entitu uživatele](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity) v dokumentaci [rozhraní API služby REST pro správu](https://msdn.microsoft.com/library/azure/dn776326.aspx) odkaz.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Požadavky

Dokončení úkolů v tomto článku: [vytvoření instance Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-developer"> </a>Vytvoření nového pro vývojáře

Pokud chcete přidat nového uživatele, postupujte podle kroků v této části:

1. Vyberte **uživatelé** karty na levé straně obrazovky.
2. Stisknutím klávesy **+ přidat**.
3. Zadejte příslušné informace pro uživatele.
4. Stiskněte **Přidat**.

    ![Přidání nového uživatele](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

Ve výchozím nastavení, jsou nově vytvořený vývojářských účtů **aktivní**a přidružené **vývojáři** skupiny. Vývojářské účty, které jsou v **aktivní** stavu je možné přistupovat ke všem rozhraním API, ke kterým má předplatná. Nově vytvořená pro vývojáře přidružit další skupiny, najdete v článku [přidružení skupin k vývojářům][How to associate groups with developers].

## <a name="invite-developer"> </a>Pozvat vývojář
Pozvat vývojář, postupujte podle kroků v této části:

1. Vyberte **uživatelé** karty na levé straně obrazovky.
2. Stisknutím klávesy **+ pozvat**.

Zobrazí se potvrzovací zpráva, ale nově pozvaný pro vývojáře se nezobrazí v seznamu až po přijetí e-mailové pozvánce. 

Když vývojář pozvání, odešle e-mail pro vývojáře. Tento e-mail je generována pomocí šablony a přizpůsobit. Další informace najdete v tématu [konfigurovat e-mailové šablony][Configure email templates].

Po přijetí e-mailové pozvánce se účet stane aktivním.

## <a name="block-developer"> </a> Deaktivovat ani znovu aktivovat účet pro vývojáře

Ve výchozím nastavení, jsou nově vytvořený nebo pozvaný vývojářských účtů **aktivní**. Deaktivovat účet pro vývojáře, klikněte na tlačítko **bloku**. Pokud chcete znovu aktivovat účet blokované pro vývojáře, klikněte na tlačítko **aktivovat**. Blokované vývojářského účtu nelze přístup k portálu pro vývojáře nebo volejte libovolné rozhraní API. Pokud chcete odstranit uživatelský účet, klikněte na tlačítko **odstranit**.

Zablokujete určitému uživateli, postupujte podle následujících kroků.

1. Vyberte **uživatelé** karty na levé straně obrazovky.
2. Kliknutím na uživatele, který chcete blokovat.
3. Stisknutím klávesy **bloku**.

## <a name="reset-a-user-password"></a>Resetování hesla uživatele

Programová práce s uživatelskými účty, najdete v článku [entitu uživatele](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity) v dokumentaci [rozhraní API služby REST pro správu](https://msdn.microsoft.com/library/azure/dn776326.aspx) odkaz. Pokud chcete resetovat heslo uživatelského účtu na určitou hodnotu, můžete použít [aktualizaci uživatele](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity#UpdateUser) operaci a zadejte požadované heslo.

## <a name="next-steps"></a>Další kroky
Po vytvoření účtu pro vývojáře, můžete ho přidružit k rolím a jeho přihlášení k odběru produktů a rozhraní API. Další informace najdete v tématu [postup vytvoření a používání skupin][How to create and use groups].

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
