---
title: Přidání uživatelů spolupráce B2B jako informační pracovník – Azure AD
description: Spolupráce B2B umožňuje informačním pracovníkům a vlastníkům aplikací přidat uživatele typu Host do služby Azure AD pro přístup | Microsoft Docs
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 12/19/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 345cbe2efb644fe2b84d4a6021ff962337e29285
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98219908"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>Způsob, jakým uživatelé ve vaší organizaci můžou pozvat uživatele typu Host do aplikace

Po přidání uživatele typu Host do adresáře ve službě Azure AD může vlastník aplikace odeslat uživateli typu Host přímý odkaz na aplikaci, kterou chce sdílet. Správci Azure AD taky můžou v tenantovi Azure AD nastavit samoobslužnou správu pro galerie nebo aplikace založené na SAML. Vlastníci aplikace tak můžou spravovat své vlastní uživatele typu Host, i když do tohoto adresáře ještě nepřidali uživatele typu Host. Když je aplikace nakonfigurovaná pro samoobslužnou službu, vlastník aplikace použije svůj přístupový panel k pozvání uživatele typu Host k aplikaci nebo přidání uživatele typu Host do skupiny, která má přístup k aplikaci. Samoobslužná správa aplikací pro galerii a aplikace založené na SAML vyžadují od správce určité počáteční nastavení. Následuje souhrn kroků při instalaci (podrobné pokyny najdete v části [požadavky](#prerequisites) dále na této stránce):

 - Povolení samoobslužné správy skupin pro vašeho tenanta
 - Vytvořte skupinu, která se přiřadí k aplikaci a nastaví uživatele jako vlastníka.
 - Konfigurace aplikace pro samoobslužnou službu a přiřazení skupiny k aplikaci

> [!NOTE]
> * Tento článek popisuje, jak nastavit samoobslužnou správu pro galerie a aplikace založené na SAML, které jste přidali do svého tenanta Azure AD. Můžete také [nastavit samoobslužné skupiny Microsoft 365](../enterprise-users/groups-self-service-management.md) , aby uživatelé mohli spravovat přístup ke svým vlastním skupinám Microsoft 365. Další způsoby, jak můžou uživatelé sdílet soubory Office a aplikace s uživateli typu Host, najdete [v tématu přístup hosta ve skupinách Microsoft 365](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) a [sdílení souborů a složek služby SharePoint](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c).
> * Uživatelé můžou pozvat hosty jenom v případě, že mají roli **pozvat hosta** .
## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>Pozvání uživatele typu Host k aplikaci z přístupového panelu

Po nakonfigurování aplikace pro samoobslužné uživatele mohou vlastníci aplikace pomocí vlastního přístupového panelu pozvat uživatele typu Host do aplikace, kterou chtějí sdílet. Uživatel typu Host není nutně potřeba do Azure AD přidat předem. 

1. Přejděte na. otevře se přístupový panel `https://myapps.microsoft.com` .
2. Ukažte na aplikace, vyberte tři tečky (**...**) a pak vyberte **Spravovat aplikaci**.
 
   ![Snímek obrazovky s podnabídkou spravovat aplikaci pro aplikaci Salesforce](media/add-users-iw/access-panel-manage-app.png)
 
3. V horní části seznamu uživatelů vyberte **+** .
   
   ![Snímek obrazovky zobrazující symbol plus pro přidání členů do aplikace](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. Do vyhledávacího pole **přidat členy** zadejte e-mailovou adresu uživatele typu Host. Volitelně můžete zahrnout uvítací zprávu.
   
   ![Snímek obrazovky zobrazující okno Přidat členy pro přidání hosta](media/add-users-iw/access-panel-invitation.png)
   
5. Vyberte **Přidat** a odešlete pozvánku uživateli typu Host. Po odeslání e-mailové pozvánky se uživatelský účet automaticky přidá do adresáře jako uživatel typu host.

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>Pozvat někoho, aby se připojil ke skupině, která má přístup k aplikaci
Po nakonfigurování aplikace pro samoobslužné uživatele můžou vlastníci aplikací pozvat uživatele typu Host do skupin, které spravují, a získat tak přístup k aplikacím, které chtějí sdílet. Uživatelé typu host již nemusí v adresáři existovat. Vlastník aplikace podle těchto kroků vyzve uživatele typu Host ke skupině, aby k němu měli přístup.

1. Ujistěte se, že jste vlastníkem samoobslužné skupiny, která má přístup k aplikaci, kterou chcete sdílet.
2. Přejděte na. otevře se přístupový panel `https://myapps.microsoft.com` .
3. Vyberte aplikaci **skupiny** .
   
   ![Snímek obrazovky s aplikací skupiny na přístupovém panelu](media/add-users-iw/access-panel-groups.png)
   
4. V části **vlastní skupiny** vyberte skupinu, která má přístup k aplikaci, kterou chcete sdílet.
   
   ![Snímek obrazovky zobrazující, kde vybrat skupinu ve skupinách](media/add-users-iw/access-panel-groups-i-own.png)
   
5. V horní části seznamu členů skupiny vyberte **+** .
   
   ![Snímek obrazovky zobrazující symbol plus pro přidání členů do skupiny](media/add-users-iw/access-panel-groups-add-member.png)
   
6. Do vyhledávacího pole **přidat členy** zadejte e-mailovou adresu uživatele typu Host. Volitelně můžete zahrnout uvítací zprávu.
   
   ![Snímek obrazovky zobrazující okno Přidat členy pro přidání hosta](media/add-users-iw/access-panel-invitation.png)
   
7. Vyberte možnost **Přidat** a automaticky odešlete pozvánku uživateli typu Host. Po odeslání e-mailové pozvánky se uživatelský účet automaticky přidá do adresáře jako uživatel typu host.


## <a name="prerequisites"></a>Požadavky

Samoobslužná správa aplikací vyžaduje určité počáteční nastavení globálním správcem a správcem Azure AD. V rámci tohoto nastavení nakonfigurujete aplikaci pro samoobslužnou službu a přiřadíte skupinu k aplikaci, kterou může vlastník aplikace spravovat. Skupinu můžete také nakonfigurovat tak, aby umožňovala komukoli požádat o členství, ale vyžaduje schválení vlastníka skupiny. (Další informace o [správě skupin samoobslužných služeb](../enterprise-users/groups-self-service-management.md).) 

> [!NOTE]
> Uživatele typu Host nemůžete přidat do dynamické skupiny nebo do skupiny, která je synchronizovaná s místní službou Active Directory.

### <a name="enable-self-service-group-management-for-your-tenant"></a>Povolení samoobslužné správy skupin pro vašeho tenanta
1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce.
2. V navigačním panelu vyberte **Azure Active Directory**.
3. Vyberte **skupiny**.
4. V části **Nastavení** vyberte **Obecné**.
5. V části **Samoobslužná správa skupin**, vedle **vlastníků můžou spravovat žádosti o členství ve skupině na přístupovém panelu**, vyberte **Ano**.
6. Vyberte **Uložit**.

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>Vytvořte skupinu, která se přiřadí k aplikaci a nastaví uživatele jako vlastníka.
1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce Azure AD nebo globální správce.
2. V navigačním panelu vyberte **Azure Active Directory**.
3. Vyberte **skupiny**.
4. Vyberte možnost **Nová skupina**.
5. V části **typ skupiny** vyberte **zabezpečení**.
6. Zadejte **Název skupiny** a **Popis skupiny**.
7. V části **typ členství** vyberte **přiřazeno**.
8. Vyberte **vytvořit** a zavřete stránku **skupiny** .
9. Na stránce **skupiny – všechny skupiny** otevřete skupinu. 
10. V části **Spravovat** vyberte **vlastníci**  >  **Přidat vlastníky**. Vyhledejte uživatele, který by měl spravovat přístup k aplikaci. Vyberte uživatele a pak klikněte na **Vybrat**.

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>Konfigurace aplikace pro samoobslužnou službu a přiřazení skupiny k aplikaci
1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce Azure AD nebo globální správce.
2. V navigačním podokně vyberte **Azure Active Directory**.
3. V části **Spravovat** vyberte **podnikové aplikace**  >  **všechny aplikace**.
4. V seznamu aplikace vyhledejte a otevřete aplikaci.
5. V části **Spravovat** vyberte **jednotné přihlašování** a nakonfigurujte aplikaci pro jednotné přihlašování. (Podrobnosti najdete v tématu [Správa jednotného přihlašování pro podnikové aplikace](../manage-apps/add-application-portal-setup-sso.md).)
6. V části **Spravovat** vyberte **Samoobslužná služba** a nastavte Samoobslužný přístup k aplikacím. (Podrobnosti najdete v tématu [Jak používat Samoobslužný přístup k aplikacím](../manage-apps/access-panel-manage-self-service-access.md).) 

    > [!NOTE]
    > **Chcete-li přidat nastavení, které skupiny má přiřadit uživatele?** vyberte skupinu, kterou jste vytvořili v předchozí části.
7. V části **Spravovat** vyberte **Uživatelé a skupiny** a ověřte, že se v seznamu objeví samoobslužná skupina, kterou jste vytvořili.
8. Chcete-li přidat aplikaci do přístupového panelu vlastníka skupiny, vyberte možnost **Přidat** uživatele  >  **a skupiny** uživatelů. Vyhledejte vlastníka skupiny a vyberte uživatele, klikněte na **Vybrat** a potom kliknutím na **přiřadit** přidejte uživatele do aplikace.

## <a name="next-steps"></a>Další kroky

Přečtěte si následující články o spolupráci Azure AD B2B:

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Jak Azure Active Directory správci přidávají uživatele spolupráce B2B?](add-users-administrator.md)
- [Uplatnění pozvánky B2B pro spolupráci](redemption-experience.md)
- [Externí identity – ceny](external-identities-pricing.md)