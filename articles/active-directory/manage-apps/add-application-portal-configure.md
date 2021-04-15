---
title: 'Rychlý Start: Konfigurace vlastností aplikace ve vašem tenantovi Azure Active Directory (Azure AD)'
description: V tomto rychlém startu se používá Azure Portal ke konfiguraci aplikace, která je zaregistrovaná u svého tenanta Azure Active Directory (Azure AD).
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: iangithinji
ms.openlocfilehash: 3b7a5d88aa40422dc46c6ca2c1681447cb030ea4
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379515"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Rychlý Start: Konfigurace vlastností aplikace ve vašem tenantovi Azure Active Directory (Azure AD)

V předchozím rychlém startu jste přidali aplikaci do tenanta Azure Active Directory (Azure AD). Když přidáváte aplikaci, umožníte tenantovi Azure AD zjistit, že se jedná o poskytovatele identity pro danou aplikaci. Teď nakonfigurujete některé vlastnosti pro aplikaci.
 
## <a name="prerequisites"></a>Požadavky

Pokud chcete nakonfigurovat vlastnosti aplikace v tenantovi Azure AD, budete potřebovat:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Jedna z následujících rolí: globální správce, správce cloudové aplikace, správce aplikace nebo vlastník instančního objektu.
- Volitelné: dokončování [zobrazení vašich aplikací](view-applications-portal.md)
- Volitelné: dokončení [Přidání aplikace](add-application-portal.md)

>[!IMPORTANT]
>K otestování kroků v tomto rychlém startu použijte neprodukční prostředí.

## <a name="configure-app-properties"></a>Konfigurace vlastností aplikace

Po dokončení přidávání aplikace do tenanta služby Azure AD se zobrazí stránka přehled. Pokud konfigurujete aplikaci, která již byla přidána, podívejte se do prvního rychlého startu. Provede vás zobrazením aplikací přidaných do tenanta. 

Úprava vlastností aplikace:

1. Na portálu Azure AD vyberte **podnikové aplikace**. Pak vyhledejte a vyberte aplikaci, kterou chcete nakonfigurovat.
2. V části **Spravovat** vyberte **vlastnosti** a otevřete tak podokno **vlastnosti** pro úpravy.
3. Věnujte prosím chvilku pochopení dostupných možností. Dostupné možnosti budou záviset na tom, jak je aplikace integrovaná se službou Azure AD. Například aplikace, která používá jednotné přihlašování založené na SAML, bude obsahovat pole, jako je *Adresa URL přístupu uživatele* , zatímco aplikace, která používá jednotné přihlašování založené na OIDC, nebude. Všimněte si také, že aplikace přidané prostřednictvím **Azure Active Directory > registrace aplikací** jsou ve výchozím nastavení aplikace založené na OIDC. Vzhledem k tomu, že aplikace přidané prostřednictvím **Azure Active Directory > podnikových aplikací** můžou používat libovolný počet standardů jednotného přihlašování. Všechny aplikace budou mít pole pro konfiguraci, kdy se aplikace zobrazí a dá se použít. Tato pole jsou:
    - **Povolit uživatelům přihlášení?** Určuje, zda se může přihlásit uživatel přiřazený k aplikaci.
    - Je **vyžadováno přiřazení uživatele?** Určuje, jestli se uživatelé, kteří nejsou přiřazeni k aplikaci, můžou přihlásit.
    - **Viditelná pro uživatele?** Určuje, jestli se uživatelé přiřazení k aplikaci uvidí v [okně moje aplikace](https://myapps.microsoft.com) a ve spouštěči aplikace Microsoft 365. (Další informace naleznete v nabídce dlaždice v levém horním rohu webu Microsoft 365.)
    
    > [!TIP]
    > K přiřazování uživatelů dojde v části navigace **Uživatelé a skupiny** .

    Tři možnosti lze přepínat nezávisle na sobě a výsledné chování není vždy zřejmé. Tady je tabulka, která může pomáhat:
    
    | Povolit uživatelům přihlášení? | Je přiřazení uživatelů povinné? | Uvidí ji uživatelé? | Chování pro uživatele, kteří mají buď přiřazenou aplikaci, nebo ne. |
    |---|---|---|---|
    | Yes | Yes | Yes | Přiřazení uživatelé uvidí aplikaci a přihlásí se.<br>Nepřiřazení uživatelé aplikaci uvidí a nelze se přihlásit. |
    | Yes | Yes | No  | Přiřazená použití nemůže aplikaci zobrazit, ale může se přihlásit.<br>Nepřiřazení uživatelé aplikaci uvidí a nelze se přihlásit. |
    | Yes | No  | Yes | Přiřazení uživatelé uvidí aplikaci a přihlásí se.<br>Nepřiřazení uživatelé aplikaci uvidí, ale můžou se přihlásit. |
    | Yes | No  | No  | Přiřazení uživatelé nemůžou aplikaci zobrazit, ale můžou se přihlásit.<br>Nepřiřazení uživatelé aplikaci uvidí, ale můžou se přihlásit. |
    | No  | Yes | Yes | Přiřazení uživatelé nemohou aplikaci zobrazit a nemohou se přihlásit.<br>Nepřiřazení uživatelé aplikaci uvidí a nelze se přihlásit. |
    | No  | Yes | No  | Přiřazení uživatelé nemohou aplikaci zobrazit a nemohou se přihlásit.<br>Nepřiřazení uživatelé aplikaci uvidí a nelze se přihlásit. |
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
> Správu aplikací můžete automatizovat pomocí Graph API, přečtěte si téma [Automatizace správy aplikací pomocí rozhraní Microsoft Graph API](/graph/application-saml-sso-configure-api).

## <a name="add-notes"></a>Přidání poznámek

Pomocí pole poznámky můžete přidat všechny informace, které jsou relevantní pro správu aplikace ve službě Azure AD. Poznámky jsou volné textové pole s maximální velikostí 1024 znaků.

1. Na portálu Azure AD vyberte **podnikové aplikace**. Pak vyhledejte a vyberte aplikaci, kterou chcete nakonfigurovat.
2. V části **Spravovat** vyberte **vlastnosti** a otevřete tak podokno **vlastnosti** pro úpravy.
3. Aktualizujte pole poznámky a vyberte **Uložit**.

    ![Snímek obrazovky s vlastnostmi, který ukazuje, jak změnit poznámky](media/add-application-portal/notes-application.png)

    
## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete pokračovat v sérii rychlých startů, zvažte odstranění aplikace a vyčistěte testovacího tenanta. Odstranění aplikace se zabývá posledním rychlým startem v této sérii, najdete v tématu [odstranění aplikace](delete-application-portal.md).

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku, kde se dozvíte, jak přiřadit uživatele k aplikaci.
> [!div class="nextstepaction"]
> [Přiřazení uživatelů k aplikaci](add-application-portal-assign-users.md)