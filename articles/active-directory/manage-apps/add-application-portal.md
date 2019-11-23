---
title: Quickstart - Add an app to your Azure Active Directory tenant
description: V tomto rychlém startu se pomocí webu Azure Portal přidá aplikace z galerie do tenanta Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 650a4f643f170fc64dd0e0643d645a7dcbffdfbc
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420477"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-tenant"></a>Rychlý start: Přidání aplikace do tenanta Azure Active Directory

Azure Active Directory (Azure AD) nabízí galerii obsahující tisíce předem integrovaných aplikací. Galerie pravděpodobně obsahuje i některé aplikace, které využívá vaše organizace. V tomto rychlém startu se pomocí webu Azure Portal přidá aplikace z galerie do tenanta Azure Active Directory (Azure AD).

Po přidání aplikace do tenanta Azure AD můžete:

- Manage user access to the application with a Conditional Access policy.
- Konfigurovat jednotné přihlašování uživatelů k aplikaci pomocí jejich účtů Azure AD.

## <a name="before-you-begin"></a>Než začnete

K přidání aplikace do tenanta budete potřebovat:

- Předplatné Azure AD
- Předplatné s povoleným jednotným přihlašováním pro vaši aplikaci

Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce vašeho tenanta Azure AD, správce cloudové aplikace nebo správce aplikace.

K testování postupů v tomto kurzu doporučujeme použít neprodukční prostředí. Pokud nemáte neprodukční prostředí Azure AD, můžete [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-an-application-to-your-azure-ad-tenant"></a>Přidání aplikace do tenanta Azure AD

Přidání aplikace z galerie do tenanta Azure AD:

1. In the [Azure portal](https://portal.azure.com), on the left navigation panel, select **Azure Active Directory**.

2. In the **Azure Active Directory** pane, select **Enterprise applications**. The **All applications** pane opens and displays a random sample of the applications in your Azure AD tenant.

3. To add a gallery app to your tenant, select **New application**. 

    ![Select New application to add a gallery app to your tenant](media/add-application-portal/new-application.png)

 4. Switch to the new gallery preview experience: In the banner at the top of the **Add an application page**, select the link that says **Click here to try out the new and improved app gallery**.

5. The **Browse Azure AD Gallery (Preview)** pane opens and displays tiles for cloud platforms, on-premises applications, and featured applications. Note that the applications listed in the **Featured applications** section have icons indicating whether they support federated single sign-on (SSO) and provisioning.

    ![Search for an app by name or category](media/add-application-portal/browse-gallery.png)

6. You can browse the gallery for the application you want to add, or search for the application by entering its name in the search box. Then select the application from the results. In the form, you can edit the name of the application to match the needs of your organization. In this example we've changed the name to **GitHub-test**.

    ![Shows how to add an application from the gallery](media/add-application-portal/create-application.png)

7. Vyberte **Create** (Vytvořit). Zobrazí se stránka Začínáme s možnostmi konfigurace aplikace pro vaši organizaci.

You've finished adding your application. V následujících částech se dozvíte, jak změnit logo a upravit další vlastnosti aplikace.

## <a name="find-your-azure-ad-tenant-application"></a>Vyhledání aplikace tenanta Azure AD

Předpokládejme, že jste se museli vzdálit a teď se vracíte dokončit konfiguraci aplikace. The first thing to do is find your application.

1. In the **[Azure portal](https://portal.azure.com)** , on the left navigation panel, select **Azure Active Directory**.
1. In the **Azure Active Directory** pane, select **Enterprise applications**.
1. From the **Application Type** drop-down menu, select **All Applications**, and then select **Apply**. Další informace o možnostech zobrazení najdete v tématu [Zobrazení aplikací tenanta](view-applications-portal.md).
1. Zobrazí se seznam všech aplikací ve vašem tenantovi Azure AD. Tento seznam představuje náhodný vzorek. To see more applications, select **Show more** one or more times.
1. To quickly find an application in your tenant, enter the application name in the search box and select **Apply**. This example finds the GitHub-test application added previously.

    ![Shows how to find an application using the search box](media/add-application-portal/find-application.png)

## <a name="configure-user-sign-in-properties"></a>Konfigurace vlastností přihlašování uživatelů

Now that you've found the application, you can open it and configure application properties.

To edit the application properties:

1. Select the application to open it.
2. Select **Properties** to open the properties pane for editing.

    ![Shows the Properties screen and editable app properties](media/add-application-portal/edit-properties.png)

3. Seznamte se s možnostmi přihlašování. The options determine how users who are assigned or unassigned to the application can sign into the application. And, the options also determine if a user can see the application in the access panel.

    - **Enabled for users to sign-in?** determines whether users assigned to the application can sign in.
    - **User assignment required?** determines whether users who aren't assigned to the application can sign in.
    - **Visible to users?** determines whether users assigned to an app can see it in the access panel and O365 launcher.

4. Use the following tables to help you choose the best options for your needs.

   - Chování pro **přiřazené** uživatele:

       | Nastavení vlastností aplikace | | | Prostředí pro přiřazené uživatele | |
       |---|---|---|---|---|
       | Mohou se uživatelé přihlásit? | Je přiřazení uživatelů povinné? | Uvidí ji uživatelé? | Mohou se přiřazení uživatelé přihlásit? | Zobrazí se aplikace přiřazeným uživatelům?* |
       | ano | ano | ano | ano | ano  |
       | ano | ano | ne  | ano | ne   |
       | ano | ne  | ano | ano | ano  |
       | ano | ne  | ne  | ano | ne   |
       | ne  | ano | ano | ne  | ne   |
       | ne  | ano | ne  | ne  | ne   |
       | ne  | ne  | ano | ne  | ne   |
       | ne  | ne  | ne  | ne  | ne   |

   - Chování pro **nepřiřazené** uživatele:

       | Nastavení vlastností aplikace | | | Prostředí pro nepřiřazené uživatele | |
       |---|---|---|---|---|
       | Enabled for users to sign in? | Je přiřazení uživatelů povinné? | Uvidí ji uživatelé? | Mohou se nepřiřazení uživatelé přihlásit? | Zobrazí se aplikace nepřiřazeným uživatelům?* |
       | ano | ano | ano | ne  | ne   |
       | ano | ano | ne  | ne  | ne   |
       | ano | ne  | ano | ano | ne   |
       | ano | ne  | ne  | ano | ne   |
       | ne  | ano | ano | ne  | ne   |
       | ne  | ano | ne  | ne  | ne   |
       | ne  | ne  | ano | ne  | ne   |
       | ne  | ne  | ne  | ne  | ne   |

     \* Zobrazí se aplikace uživateli na přístupovém panelu a ve spouštěči aplikací Office 365?

## <a name="use-a-custom-logo"></a>Použití vlastního loga

Použití vlastního loga:

1. Vytvořte logo o rozměrech 215 × 215 pixelů a uložte ho ve formátu PNG.
1. Since you've already found your application, select the application.
1. In the left pane, select **Properties**.
1. Nahrajte logo.
1. When you're finished, select **Save**. 

    ![Shows how to change the logo from the app's Properties page](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > The thumbnail displayed on this **Properties** pane doesn't update right away. You can close and reopen the properties to see the updated icon.

## <a name="next-steps"></a>Další kroky

Now that you've added the application to your Azure AD organization, [choose a single sign-on method](what-is-single-sign-on.md#choosing-a-single-sign-on-method) you want to use and refer to the appropriate article below:

- [Configure SAML-based single sign-on](configure-single-sign-on-non-gallery-applications.md)
- [Configure password single sign-on](configure-password-single-sign-on-non-gallery-applications.md)
- [Configure linked sign-on](configure-linked-sign-on.md)
