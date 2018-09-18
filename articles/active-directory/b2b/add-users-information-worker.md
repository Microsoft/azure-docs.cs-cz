---
title: Přidat uživatele spolupráce B2B jako pracovník s informacemi – Azure Active Directory | Dokumentace Microsoftu
description: Spolupráce B2B ve službě umožňuje pracovníkům a vlastníci aplikace přidat uživatele typu Host do služby Azure AD pro přístup k | Dokumentace Microsoftu
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: mal
ms.openlocfilehash: e590500dd622988226c592352b0b86f16d54a9d4
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983059"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>Jak uživatelé ve vaší organizaci můžete pozvat uživatele typu Host do aplikace

Po Host uživatel přidaný do adresáře ve službě Azure AD, vlastníka aplikace můžete odeslat uživatele typu Host přímý odkaz na aplikaci, kterou chce sdílet. Správci služby Azure AD můžete také nastavit samoobslužné správy tak, aby aplikace vlastníci můžou spravovat své vlastní uživatele typu Host i v případě, že uživatelé typu Host se nepřidaly do adresáře zatím. Pokud aplikace je nakonfigurovaná pro samoobslužné služby, vlastník aplikace používá svůj přístupový Panel pozvat uživatele typu Host do aplikace nebo přidat uživatele typu Host do skupiny, který má přístup k aplikaci. Správa samoobslužných služeb aplikací vyžaduje některé počáteční nastavení správce. Tady je přehled kroků instalace (podrobnější pokyny najdete v článku [požadavky](#prerequisites) později na této stránce):

 - Povolit samoobslužnou správu skupin pro vašeho tenanta
 - Vytvoření skupiny pro přiřazení k aplikaci a nastavit uživatele jako vlastníka
 - Konfigurace aplikace pro samoobslužné funkce a přiřaďte skupiny k aplikaci

## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>Pozvat uživatele typu Host do aplikace na přístupovém panelu

Po dokončení konfigurace aplikace pro samoobslužné funkce, můžete počet vlastníků aplikace použít přístupovém panelu se pozvat uživatele typu Host do aplikace, které chtějí. Uživatel typu Host nemusí nutně být přidány do služby Azure AD předem. 

1. Otevřete tak, že přejdete na přístupovém panelu `https://myapps.microsoft.com`.
2. Přejděte na aplikaci, vyberte symbol tří teček (**...** ) a pak vyberte **spravovat aplikaci**.
 
   ![Správa přístupových panelech aplikace](media/add-users-iw/access-panel-manage-app.png)
 
3. V horní části seznamu uživatelů vyberte **+**.
   
   ![Přístupový Panel – Přidat uživatele](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. V **přidat členy** vyhledávací pole, zadejte e-mailovou adresu pro uživatele typu Host. Volitelně můžete zahrnout zobrazení uvítací zprávy.
   
   ![Přístup k panelu pozvánky](media/add-users-iw/access-panel-invitation.png)
   
5. Vyberte **přidat** odeslat pozvánku uživateli typu Host. Po odeslání e-mailové pozvánce, uživatelský účet automaticky přidán do adresáře v roli hosta.

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>Pozvat další připojení ke skupině, která má přístup k aplikaci
Po dokončení konfigurace aplikace pro samoobslužné funkce, počet vlastníků aplikace můžete pozvat uživatele typu Host do skupiny, které spravují, ke kterým mají přístup k aplikacím, které chtějí. Uživatelé typu Host, nemusíte v adresáři už existují. Vlastník aplikace postupujte podle těchto kroků se pozvat uživatele typu Host do skupiny tak, aby se přístup k aplikaci.

1. Ujistěte se, že jste vlastníkem skupiny samoobslužné služby, který má přístup k aplikaci, kterou chcete sdílet.
2. Otevřete tak, že přejdete na přístupovém panelu `https://myapps.microsoft.com`.
3. Vyberte **skupiny** aplikace.
   
   ![Přístup k aplikaci skupiny panelů](media/add-users-iw/access-panel-groups.png)
   
4. V části **skupiny moje**, vyberte skupinu, která má přístup k aplikaci, kterou chcete sdílet.
   
   ![Přístupový Panel – skupiny moje](media/add-users-iw/access-panel-groups-i-own.png)
   
5. V horní části seznamu členů skupiny, vyberte **+**.
   
   ![Přístupový Panel – skupiny přidat člena](media/add-users-iw/access-panel-groups-add-member.png)
   
6. V **přidat členy** vyhledávací pole, zadejte e-mailovou adresu pro uživatele typu Host. Volitelně můžete zahrnout zobrazení uvítací zprávy.
   
   ![Přístup k panelu skupiny pozvánky](media/add-users-iw/access-panel-invitation.png)
   
7. Vyberte **přidat** automaticky odeslat pozvánku uživateli typu Host. Po odeslání e-mailové pozvánce, uživatelský účet automaticky přidán do adresáře v roli hosta.


## <a name="prerequisites"></a>Požadavky

Správa samoobslužných služeb aplikací vyžaduje některé počáteční nastavení tak, že globální správce a správce Azure AD. Jako součást této instalace a nastavení bude konfigurace aplikace pro samoobslužné funkce a přiřadit skupiny k aplikaci, která může spravovat vlastníka aplikace. Můžete také nakonfigurovat skupinu, kterou chcete povolit všem uživatelům žádat o členství, ale vyžadují schválení vlastníka skupiny. (Další informace o [Samoobslužná správa skupin](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management).) 

> [!NOTE]
> Nelze přidat uživatele typu Host do dynamické skupiny nebo skupiny, která je synchronizovaná s místní služby Active Directory.

### <a name="enable-self-service-group-management-for-your-tenant"></a>Povolit samoobslužnou správu skupin pro vašeho tenanta
1. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako globální správce.
2. V navigačním panelu vyberte **Azure Active Directory**.
3. Vyberte **skupiny**.
4. V části **nastavení**vyberte **Obecné**.
5. V části **Self Service skupiny správy**vedle možnosti **vlastníci můžou spravovat žádosti o členství ve skupině na přístupovém panelu**vyberte **Ano**.
6. Vyberte **Uložit**.

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>Vytvoření skupiny pro přiřazení k aplikaci a nastavit uživatele jako vlastníka
1. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako globální správce nebo správce Azure AD.
2. V navigačním panelu vyberte **Azure Active Directory**.
3. Vyberte **skupiny**.
4. Vyberte **novou skupinu**.
5. V části **typ skupiny**vyberte **zabezpečení**.
6. Zadejte **název skupiny** a **popis skupiny**.
7. V části **typ členství**vyberte **přiřazeno**.
8. Vyberte **vytvořit**a ukončete **skupiny** stránky.
9. Na **skupiny – všechny skupiny** stránce, otevřete skupinu. 
10. V části **spravovat**vyberte **vlastníky** > **přidat vlastníky**. Vyhledejte uživatele, který má spravovat přístup k aplikaci. Vyberte uživatele a potom klikněte na tlačítko **vyberte**.

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>Konfigurace aplikace pro samoobslužné funkce a přiřaďte skupiny k aplikaci
1. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako globální správce nebo správce Azure AD.
2. V navigačním podokně vyberte **Azure Active Directory**.
3. V části **spravovat**vyberte **podnikové aplikace** > **všechny aplikace**.
4. V seznamu aplikací najít a otevřít aplikaci.
5. V části **spravovat**vyberte **jednotného přihlašování**a nakonfigurovat aplikaci pro jednotné přihlašování. (Podrobnosti najdete v tématu [Správa jednotného přihlašování pro podnikové aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal).)
6. V části **spravovat**vyberte **samoobslužné**a nastavit přístup k aplikaci Samoobslužné služby. (Podrobnosti najdete v tématu [použití přístup k aplikaci Samoobslužné](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to).) 
    > [!NOTE]
    > Pro nastavení **do které skupiny by měl přiřazení uživatelé měli přidat?** vyberte skupinu, kterou jste vytvořili v předchozí části.
7. V části **spravovat**vyberte **uživatelů a skupin**a ověřte, že se v seznamu zobrazí samoobslužné skupinu, kterou jste vytvořili.
8. Postup přidání aplikace na přístupovém panelu vlastník skupiny, vyberte **přidat uživatele** > **uživatelů a skupin**. Vyhledejte vlastníka skupiny a vyberte uživatele, klikněte na tlačítko **vyberte**a potom klikněte na tlačítko **přiřadit** přidejte uživatele k aplikaci.

## <a name="next-steps"></a>Další postup

Na spolupráce B2B ve službě Azure AD najdete v následujících článcích:

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Jak správci služby Azure Active Directory přidat uživatele spolupráce B2B?](add-users-administrator.md)
- [Uplatnění pozvání spolupráce B2B](redemption-experience.md)
- [Licencování spolupráce v Azure AD B2B](licensing-guidance.md)
