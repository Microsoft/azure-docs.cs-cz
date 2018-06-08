---
title: Jak spravovat uživatelské účty v Azure API Management | Microsoft Docs
description: Naučte se vytvořit nebo pozvat uživatele ve službě Azure API Management
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
ms.openlocfilehash: 53ed914a3890aab1dd49536856feeb8975986e00
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "33934804"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Správa uživatelských účtů ve službě Azure API Management
Vývojáři ve službě API Management, jsou uživatelé rozhraní API, která vystavit použití služby API Management. Tato příručka obsahuje k vytváření a zvaní vývojářů používat rozhraní API a produkty, abyste vytvořili pro ně k dispozici s vaší instance služby API Management. Informace o správě uživatelských účtů prostřednictvím kódu programu, najdete v článku [entitu uživatele](https://msdn.microsoft.com/library/azure/dn776330.aspx) dokumentaci v [rozhraní API služby REST pro správu](https://msdn.microsoft.com/library/azure/dn776326.aspx) odkaz.

## <a name="prerequisites"></a>Požadavky

Dokončení úkolů v tomto článku: [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-developer"> </a>Vytvoření nové vývojáře

Pokud chcete přidat nového uživatele, postupujte podle kroků v této části:

1. Vyberte **uživatelé** karty na levé straně obrazovky.
2. Stiskněte klávesu **+ přidat**.
3. Zadejte příslušné informace pro uživatele.
4. Stiskněte **Přidat**.

    ![Přidání nového uživatele](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

Ve výchozím nastavení, jsou nově vytvořený vývojářským účtům **Active**a přidružené **vývojáři** skupiny. Vývojářským účtům, které jsou v **active** stavu lze použít pro přístup k veškerému rozhraní API, k němuž mají odběry. Nově vytvořený vývojáře přidružit další skupiny, najdete v tématu [postup přidružení skupin k vývojářům][How to associate groups with developers].

## <a name="invite-developer"> </a>Pozvěte vývojář
Pozvaným vývojář, postupujte podle kroků v této části:

1. Vyberte **uživatelé** karty na levé straně obrazovky.
2. Stiskněte klávesu **+ pozvat**.

Zobrazí se zpráva s potvrzením, ale nově pozvané vývojáře nejsou uvedené v seznamu až po jejich přijetí pozvánky. 

Když je pozvat vývojář, e-mail je odeslán pro vývojáře. Tento e-mail je generována pomocí šablony a přizpůsobit. Další informace najdete v tématu [konfigurovat e-mailových šablon][Configure email templates].

Po přijetí pozvání se stane aktivní účet.

## <a name="block-developer"> </a> Deaktivovat ani znovu aktivovat účet pro vývojáře

Ve výchozím nastavení, jsou nově vytvořené nebo pozvané vývojářským účtům **Active**. Chcete-li deaktivovat účet pro vývojáře, klikněte na tlačítko **bloku**. Chcete-li znovu aktivovat účet zablokovaný vývojáře, klikněte na tlačítko **aktivovat**. Blokované vývojářského účtu nelze přístup k portálu pro vývojáře nebo volání všechny rozhraní API. Chcete-li odstranit uživatelský účet, klikněte na tlačítko **odstranit**.

Blokování uživatele, postupujte podle následujících kroků.

1. Vyberte **uživatelé** karty na levé straně obrazovky.
2. Klikněte na uživatele, který chcete blokovat.
3. Stiskněte klávesu **bloku**.

## <a name="reset-a-user-password"></a>Resetování hesla uživatele

Prostřednictvím kódu programu pracovat s uživatelskými účty, najdete v článku [entitu uživatele](https://msdn.microsoft.com/library/azure/dn776330.aspx) dokumentaci v [rozhraní API služby REST pro správu](https://msdn.microsoft.com/library/azure/dn776326.aspx) odkaz. Pokud chcete resetovat heslo uživatelského účtu na určitou hodnotu, můžete použít [aktualizaci uživatele](https://msdn.microsoft.com/library/azure/dn776330.aspx#UpdateUser) operaci a zadejte požadované heslo.

## <a name="next-steps"></a>Další kroky
Jakmile se vytvoří účet pro vývojáře, můžete přiřadit k rolím a přihlášení k odběru produktů a rozhraní API. Další informace najdete v tématu [postup vytvoření a používání skupin][How to create and use groups].

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
