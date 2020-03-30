---
title: Přidání uživatelů spolupráce B2B jako informačního pracovníka – Azure AD
description: Spolupráce B2B umožňuje informačním pracovníkům a vlastníkům aplikací přidávat uživatele typu Host do služby Azure AD pro přístup | Dokumenty společnosti Microsoft
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: abb5c6939d8c88db35a776aa8f2c075a4bdcc609
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565413"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>Jak můžou uživatelé ve vaší organizaci pozvat uživatele typu Host do aplikace

Poté, co byl uživatel typu Host přidán do adresáře ve službě Azure AD, vlastník aplikace může odeslat uživateli typu Host přímý odkaz na aplikaci, kterou chce sdílet. Správci Azure AD můžou taky nastavit samoobslužnou správu pro aplikace založené na galeriích nebo SAML ve svém tenantovi Azure AD. Tímto způsobem mohou vlastníci aplikací spravovat své vlastní uživatele typu Host, a to i v případě, že uživatelé typu Host ještě nebyli přidáni do adresáře. Když je aplikace nakonfigurovaná pro samoobslužné služby, vlastník aplikace použije svůj přístupový panel k pozvání uživatele typu Host do aplikace nebo k přidání uživatele typu Host do skupiny, která má přístup k aplikaci. Samoobslužná správa aplikací pro aplikace založené na galerii a SAML vyžaduje určité počáteční nastavení správcem. Následuje souhrn kroků nastavení (podrobnější pokyny naleznete [v tématu Požadavky](#prerequisites) dále na této stránce):

 - Povolení samoobslužné správy skupin pro vašeho tenanta
 - Vytvoření skupiny, která se přiřadí k aplikaci a učiní uživatele vlastníkem
 - Konfigurace aplikace pro samoobslužné služby a přiřazení skupiny k aplikaci

> [!NOTE]
> Tento článek popisuje, jak nastavit samoobslužnou správu pro galerijní a saml-založené aplikace, které jste přidali do svého klienta Azure AD. Můžete taky [nastavit samoobslužné skupiny Office 365,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) aby uživatelé mohli spravovat přístup ke svým vlastním skupinám Office 365. Další způsoby, jak můžou uživatelé sdílet soubory a aplikace Office s uživateli typu Host, najdete [v tématu Přístup pro hosty ve skupinách Office 365](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) a [SharePointu soubory nebo složky](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c).

## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>Pozvání uživatele typu Host do aplikace z přístupového panelu

Po nakonfigurování aplikace pro samoobslužné služby mohou vlastníci aplikací použít vlastní přístupový panel k pozvání uživatele typu Host do aplikace, kterou chtějí sdílet. Uživatel typu Host nemusí být nutně přidán do Azure AD předem. 

1. Otevřete přístupový panel `https://myapps.microsoft.com`tak, že přejdete na .
2. Přejděte na aplikaci, vyberte tři tečky (**...**) a pak vyberte **Spravovat aplikaci**.
 
   ![Snímek obrazovky s podnabídkou Spravovat aplikaci pro aplikaci Salesforce](media/add-users-iw/access-panel-manage-app.png)
 
3. V horní části seznamu uživatelů **+** vyberte .
   
   ![Snímek obrazovky se symbolem plus pro přidání členů do aplikace](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. Do vyhledávacího pole **Přidat členy** zadejte e-mailovou adresu uživatele typu Host. Volitelně můžete zahrnout uvítací zprávu.
   
   ![Snímek obrazovky s oknem Přidat členy pro přidání hosta](media/add-users-iw/access-panel-invitation.png)
   
5. Vyberte **Přidat,** chcete-li odeslat pozvánku uživateli hosta. Po odeslání e-mailové pozvánky se uživatelský účet automaticky přidá do adresáře jako uživatel typu host.

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>Pozvání uživatele, aby se připojil ke skupině, která má přístup k aplikaci
Po nakonfigurované aplikaci pro samoobslužné služby mohou vlastníci aplikací pozvat uživatele typu Host do skupin, které spravují a které mají přístup k aplikacím, které chtějí sdílet. Uživatelé typu Host nemusí v adresáři již existovat. Vlastník aplikace postupujte podle těchto kroků pozvat uživatele typu Host do skupiny, aby měli přístup k aplikaci.

1. Ujistěte se, že jste vlastníkem samoobslužné skupiny, která má přístup k aplikaci, kterou chcete sdílet.
2. Otevřete přístupový panel `https://myapps.microsoft.com`tak, že přejdete na .
3. Vyberte aplikaci **Skupiny.**
   
   ![Snímek obrazovky s aplikací Skupiny na přístupovém panelu](media/add-users-iw/access-panel-groups.png)
   
4. V části **Skupiny, které vlastním**, vyberte skupinu, která má přístup k aplikaci, kterou chcete sdílet.
   
   ![Snímek obrazovky s výběrem skupiny v části Skupiny, které vlastním](media/add-users-iw/access-panel-groups-i-own.png)
   
5. V horní části seznamu členů **+** skupiny vyberte .
   
   ![Snímek obrazovky se symbolem plus pro přidání členů do skupiny](media/add-users-iw/access-panel-groups-add-member.png)
   
6. Do vyhledávacího pole **Přidat členy** zadejte e-mailovou adresu uživatele typu Host. Volitelně můžete zahrnout uvítací zprávu.
   
   ![Snímek obrazovky s oknem Přidat členy pro přidání hosta](media/add-users-iw/access-panel-invitation.png)
   
7. Vyberte **Přidat,** chcete-li automaticky odeslat pozvánku uživateli hosta. Po odeslání e-mailové pozvánky se uživatelský účet automaticky přidá do adresáře jako uživatel typu host.


## <a name="prerequisites"></a>Požadavky

Samoobslužná správa aplikací vyžaduje určité počáteční nastavení globálním správcem a správcem Azure AD. V rámci tohoto nastavení nakonfigurujete aplikaci pro samoobslužné služby a přiřadíte skupině aplikaci, kterou může spravovat vlastník aplikace. Skupinu můžete také nakonfigurovat tak, aby kdokoli mohl požádat o členství, ale vyžadoval schválení vlastníka skupiny. (Další informace o [samoobslužné správě skupin](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management).) 

> [!NOTE]
> Uživatele typu Host nelze přidat do dynamické skupiny nebo do skupiny, která je synchronizována s místní službou Active Directory.

### <a name="enable-self-service-group-management-for-your-tenant"></a>Povolení samoobslužné správy skupin pro vašeho tenanta
1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako globální správce.
2. Na navigačním panelu vyberte **Azure Active Directory**.
3. Vyberte **skupiny**.
4. V části **Nastavení**vyberte **Obecné**.
5. V části **Self Service Group Management**můžete vedle **položky Vlastníci spravovat žádosti o členství ve skupinách na přístupovém panelu vyberte** **Ano**.
6. Vyberte **Uložit**.

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>Vytvoření skupiny, která se přiřadí k aplikaci a učiní uživatele vlastníkem
1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako správce Azure AD nebo globální správce.
2. Na navigačním panelu vyberte **Azure Active Directory**.
3. Vyberte **skupiny**.
4. Vyberte **nová skupina**.
5. V části **Typ skupiny**vyberte **Zabezpečení**.
6. Zadejte **Název skupiny** a **Popis skupiny**.
7. V části **Typ členství**vyberte **Přiřazeno**.
8. Vyberte **Vytvořit**a zavřete stránku **Skupiny.**
9. Na stránce **Skupiny – všechny skupiny** otevřete skupinu. 
10. V části **Spravovat**vyberte **Vlastníci** > **Přidat vlastníky**. Vyhledejte uživatele, který by měl spravovat přístup k aplikaci. Vyberte uživatele a klepněte na tlačítko **Vybrat**.

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>Konfigurace aplikace pro samoobslužné služby a přiřazení skupiny k aplikaci
1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako správce Azure AD nebo globální správce.
2. V navigačním podokně vyberte **Azure Active Directory**.
3. V části **Správa**vyberte **možnost Podnikové aplikace** > **Všechny aplikace**.
4. V seznamu aplikací vyhledejte a otevřete aplikaci.
5. V části **Manage**vyberte **Možnost Jednotné přihlašování**a nakonfigurujte aplikaci pro jednotné přihlašování. (Podrobnosti najdete v tématu [správa jednotného přihlašování pro podnikové aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications).)
6. V části **Správa**vyberte **Samoobslužné**a nastavte samoobslužný přístup k aplikacím. (Podrobnosti najdete v tématu [použití samoobslužného přístupu k aplikacím](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to).) 

    > [!NOTE]
    > Chcete-li **nastavit, ke které skupině mají být přiřazeni uživatelé?**
7. V části **Manage**vyberte **Uživatelé a skupiny a**ověřte, zda se vytvořená samoobslužná skupina zobrazí v seznamu.
8. Pokud chcete aplikaci přidat na přístupový panel vlastníka skupiny, vyberte **Přidat** > **uživatele uživatelé a skupiny**. Vyhledejte vlastníka skupiny a vyberte uživatele, klikněte na **Vybrat**a potom klikněte na **Přiřadit** a přidejte uživatele do aplikace.

## <a name="next-steps"></a>Další kroky

Podívejte se na následující články o spolupráci Azure AD B2B:

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Jak správci Služby Azure AD přidávají uživatele spolupráce B2B?](add-users-administrator.md)
- [Uplatnění pozvánky na spolupráci B2B](redemption-experience.md)
- [Licencování spolupráce B2B ve službě Azure AD](licensing-guidance.md)
