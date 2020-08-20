---
title: 'Rychlý Start: Konfigurace vlastností aplikace ve vašem tenantovi Azure Active Directory (Azure AD)'
description: V tomto rychlém startu se používá Azure Portal ke konfiguraci aplikace, která je zaregistrovaná u svého tenanta Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.openlocfilehash: 1873cd14883e8b1a5174b8761e4a0bc9ba1ef872
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88641853"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Rychlý Start: Konfigurace vlastností aplikace ve vašem tenantovi Azure Active Directory (Azure AD)

V předchozím rychlém startu jste přidali aplikaci do tenanta Azure Active Directory (Azure AD). Když přidáváte aplikaci, umožníte tenantovi Azure AD zjistit, že se jedná o poskytovatele identity pro danou aplikaci. Teď nakonfigurujete některé vlastnosti pro aplikaci.
 
## <a name="prerequisites"></a>Předpoklady

Pokud chcete nakonfigurovat vlastnosti aplikace v tenantovi Azure AD, budete potřebovat:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Jedna z následujících rolí: globální správce, správce cloudové aplikace, správce aplikace nebo vlastník instančního objektu.
- Volitelné: dokončování [zobrazení vašich aplikací](view-applications-portal.md)
- Volitelné: dokončení [Přidání aplikace](add-application-portal.md)

>[!IMPORTANT]
>K otestování kroků v tomto rychlém startu použijte nevýrobní prostředí.

## <a name="configure-app-properties"></a>Konfigurace vlastností aplikace

Po dokončení přidávání aplikace do tenanta služby Azure AD se zobrazí stránka přehled. Pokud konfigurujete aplikaci, která již byla přidána, podívejte se do prvního rychlého startu. Provede vás zobrazením aplikací přidaných do tenanta. 

Úprava vlastností aplikace:

1. Na portálu Azure AD vyberte **podnikové aplikace**. Pak vyhledejte a vyberte aplikaci, kterou chcete nakonfigurovat.
2. V části **Spravovat** vyberte **vlastnosti** a otevřete tak podokno **vlastnosti** pro úpravy.

    ![Snímek obrazovky s vlastnostmi, který zobrazuje vlastnosti upravitelné aplikace](media/add-application-portal/edit-properties.png)

3. Poznáte možnosti, které je možné nakonfigurovat:
    - **Povolit uživatelům přihlášení?** Určuje, zda se může přihlásit uživatel přiřazený k aplikaci.
    - Je **vyžadováno přiřazení uživatele?** Určuje, jestli se uživatelé, kteří nejsou přiřazeni k aplikaci, můžou přihlásit.
    - **Viditelná pro uživatele?** Určuje, jestli se uživatelé přiřazení k aplikaci uvidí v [okně moje aplikace](https://myapps.microsoft.com) a spouštěč aplikací Office 365. (Další informace naleznete v nabídce dlaždice v levém horním rohu webu Office 365 nebo webu Microsoft 365.)
    
    > [!TIP]
    > K přiřazování uživatelů dojde v části navigace **Uživatelé a skupiny** .

    Tři možnosti lze přepínat nezávisle na sobě a výsledné chování není vždy zřejmé. Tady je tabulka, která může pomáhat:
    
    | Povolit uživatelům přihlášení? | Je přiřazení uživatelů povinné? | Uvidí ji uživatelé? | Chování pro uživatele, kteří mají buď přiřazenou aplikaci, nebo ne. |
    |---|---|---|---|
    | Yes | Yes | Yes | Přiřazení uživatelé uvidí aplikaci a přihlásí se.<br>Nepřiřazení uživatelé aplikaci uvidí a nelze se přihlásit. |
    | Yes | Ano | No  | Přiřazená použití nemůže aplikaci zobrazit, ale může se přihlásit.<br>Nepřiřazení uživatelé aplikaci uvidí a nelze se přihlásit. |
    | Ano | No  | Yes | Přiřazení uživatelé uvidí aplikaci a přihlásí se.<br>Nepřiřazení uživatelé aplikaci uvidí, ale můžou se přihlásit. |
    | Ano | No  | No  | Přiřazení uživatelé nemůžou aplikaci zobrazit, ale můžou se přihlásit.<br>Nepřiřazení uživatelé aplikaci uvidí, ale můžou se přihlásit. |
    | No  | Yes | Yes | Přiřazení uživatelé nemohou aplikaci zobrazit a nemohou se přihlásit.<br>Nepřiřazení uživatelé aplikaci uvidí a nelze se přihlásit. |
    | No  | Ano | No  | Přiřazení uživatelé nemohou aplikaci zobrazit a nemohou se přihlásit.<br>Nepřiřazení uživatelé aplikaci uvidí a nelze se přihlásit. |
    | No  | No  | Yes | Přiřazení uživatelé nemohou aplikaci zobrazit a nemohou se přihlásit.<br>Nepřiřazení uživatelé aplikaci uvidí a nelze se přihlásit. |
    | No  | No  | No  | Přiřazení uživatelé nemohou aplikaci zobrazit a nemohou se přihlásit.<br>Nepřiřazení uživatelé aplikaci uvidí a nelze se přihlásit. |

4. Až budete hotovi, vyberte **Uložit**.

## <a name="use-a-custom-logo"></a>Použití vlastního loga

Použití vlastního loga:

1. Vytvořte logo 215 × 215 pixelů a uložte ho ve formátu. png.
2. Na portálu Azure AD vyberte **podnikové aplikace**. Pak vyhledejte a vyberte aplikaci, kterou chcete nakonfigurovat.
3. V části **Spravovat** vyberte **vlastnosti** a otevřete tak podokno **vlastnosti** pro úpravy. 
4. Vyberte ikonu pro nahrání loga.
5. Až budete hotovi, vyberte **Uložit**.

    ![Snímek obrazovky s vlastnostmi, který ukazuje, jak změnit logo](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > Miniatura zobrazená v tomto podokně **vlastností** není hned aktualizována. Můžete zavřít a znovu otevřít podokno **vlastnosti** , aby se zobrazila aktualizovaná ikona.


> [!TIP]
> Správu aplikací můžete automatizovat pomocí Graph API, přečtěte si téma [Automatizace správy aplikací pomocí rozhraní Microsoft Graph API](https://docs.microsoft.com/graph/application-saml-sso-configure-api).


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete pokračovat v sérii rychlých startů, zvažte odstranění aplikace a vyčistěte testovacího tenanta. Odstranění aplikace se zabývá posledním rychlým startem v této sérii, najdete v tématu [odstranění aplikace](delete-application-portal.md).

## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte, jak nastavit jednotné přihlašování pro aplikaci.
> [!div class="nextstepaction"]
> [Nastavení jednotného přihlašování](add-application-portal-setup-sso.md)
