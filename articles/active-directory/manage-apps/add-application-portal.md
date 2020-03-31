---
title: Úvodní příručka – přidání aplikace do klienta Služby Azure Active Directory
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240489"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-tenant"></a>Rychlý start: Přidání aplikace do tenanta Azure Active Directory

Azure Active Directory (Azure AD) nabízí galerii obsahující tisíce předem integrovaných aplikací. Galerie pravděpodobně obsahuje i některé aplikace, které využívá vaše organizace. V tomto rychlém startu se pomocí webu Azure Portal přidá aplikace z galerie do tenanta Azure Active Directory (Azure AD).

Po přidání aplikace do tenanta Azure AD můžete:

- Spravujte přístup uživatelů k aplikaci pomocí zásad podmíněného přístupu.
- Konfigurovat jednotné přihlašování uživatelů k aplikaci pomocí jejich účtů Azure AD.

## <a name="before-you-begin"></a>Než začnete

K přidání aplikace do tenanta budete potřebovat:

- Předplatné Azure AD
- Předplatné s povoleným jednotným přihlašováním pro vaši aplikaci

Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce vašeho tenanta Azure AD, správce cloudové aplikace nebo správce aplikace.

K testování postupů v tomto kurzu doporučujeme použít neprodukční prostředí. Pokud nemáte neprodukční prostředí Azure AD, můžete [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-an-application-to-your-azure-ad-tenant"></a>Přidání aplikace do tenanta Azure AD

Přidání aplikace z galerie do tenanta Azure AD:

1. Na [webu Azure Portal](https://portal.azure.com)na levém navigačním panelu vyberte **Azure Active Directory**.

2. V podokně **Služby Azure Active Directory** vyberte **podnikové aplikace**. Otevře se podokno **Všechny aplikace** a zobrazí náhodný vzorek aplikací v tenantovi Azure AD.

3. Pokud chcete do tenanta přidat aplikaci galerie, vyberte **Nová aplikace**. 

    ![Vyberte Nová aplikace a přidejte do tenanta aplikaci galerie.](media/add-application-portal/new-application.png)

 4. Přepněte na nové prostředí náhledu galerie: V banneru v horní části **stránky Přidat aplikaci**vyberte odkaz s textem **Kliknutím sem vyzkoušíte novou a vylepšenou galerii aplikací**.

5. Otevře se podokno **Galerie azure ad (Preview)** a zobrazí dlaždice pro cloudové platformy, místní aplikace a doporučené aplikace. Všimněte si, že aplikace uvedené v části **Doporučené aplikace** mají ikony označující, zda podporují federované jednotné přihlašování (SSO) a zřizování.

    ![Hledání aplikace podle názvu nebo kategorie](media/add-application-portal/browse-gallery.png)

6. Můžete procházet galerii aplikace, kterou chcete přidat, nebo vyhledat aplikaci zadáním jejího názvu do vyhledávacího pole. Pak vyberte aplikaci z výsledků. Ve formuláři můžete upravit název aplikace tak, aby odpovídal potřebám vaší organizace. V tomto příkladu jsme změnili název na **GitHub-test**.

    ![Ukazuje, jak přidat aplikaci z galerie](media/add-application-portal/create-application.png)

7. Vyberte **Vytvořit**. Zobrazí se stránka Začínáme s možnostmi konfigurace aplikace pro vaši organizaci.

Dokončili jste přidání aplikace. V následujících částech se dozvíte, jak změnit logo a upravit další vlastnosti aplikace.

## <a name="find-your-azure-ad-tenant-application"></a>Vyhledání aplikace tenanta Azure AD

Předpokládejme, že jste se museli vzdálit a teď se vracíte dokončit konfiguraci aplikace. První věc, kterou udělat, je najít vaši žádost.

1. Na **[webu Azure Portal](https://portal.azure.com)** na levém navigačním panelu vyberte **Azure Active Directory**.
1. V podokně **Služby Azure Active Directory** vyberte **podnikové aplikace**.
1. V rozevírací nabídce **Typ aplikace** vyberte **Všechny aplikace**a pak vyberte **Použít**. Další informace o možnostech zobrazení najdete v tématu [Zobrazení aplikací tenanta](view-applications-portal.md).
1. Zobrazí se seznam všech aplikací ve vašem tenantovi Azure AD. Tento seznam představuje náhodný vzorek. Pokud chcete zobrazit víc aplikací, vyberte **Zobrazit víc** krát.
1. Pokud chcete rychle najít aplikaci v tenantovi, zadejte název aplikace do vyhledávacího pole a vyberte **Použít**. Tento příklad najde dříve přidanou aplikaci github-test.

    ![Ukazuje, jak najít aplikaci pomocí vyhledávacího pole](media/add-application-portal/find-application.png)

## <a name="configure-user-sign-in-properties"></a>Konfigurace vlastností přihlašování uživatelů

Nyní, když jste našli aplikaci, můžete ji otevřít a nakonfigurovat vlastnosti aplikace.

Úprava vlastností aplikace:

1. Vyberte aplikaci, kterou chcete otevřít.
2. Výběrem **možnosti Vlastnosti** otevřete podokno vlastností pro úpravy.

    ![Zobrazuje obrazovku Vlastnosti a upravitelné vlastnosti aplikace.](media/add-application-portal/edit-properties.png)

3. Seznamte se s možnostmi přihlašování. Možnosti určují, jak se uživatelé, kteří jsou přiřazeni nebo nepřiřazeni k aplikaci, mohou přihlásit do aplikace. A možnosti také určují, zda uživatel může vidět aplikaci na přístupovém panelu.

    - **Povoleno pro uživatele k přihlášení?** určuje, zda se uživatelé přiřazení k aplikaci mohou přihlásit.
    - **Je vyžadováno přiřazení uživatele?** určuje, zda se uživatelé, kteří nejsou přiřazeni k aplikaci, mohou přihlásit.
    - **Viditelné pro uživatele?** určuje, zda ji uživatelé přiřazení k aplikaci uvidí na přístupovém panelu a ve spouštěči O365.

4. Následující tabulky vám pomohou vybrat ty nejlepší možnosti pro vaše potřeby.

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
       | Povoleno pro uživatele k přihlášení? | Je přiřazení uživatelů povinné? | Uvidí ji uživatelé? | Mohou se nepřiřazení uživatelé přihlásit? | Zobrazí se aplikace nepřiřazeným uživatelům?* |
       | ano | ano | ano | ne  | ne   |
       | ano | ano | ne  | ne  | ne   |
       | ano | ne  | ano | ano | ne   |
       | ano | ne  | ne  | ano | ne   |
       | ne  | ano | ano | ne  | ne   |
       | ne  | ano | ne  | ne  | ne   |
       | ne  | ne  | ano | ne  | ne   |
       | ne  | ne  | ne  | ne  | ne   |

     * Zobrazí se aplikace uživateli na přístupovém panelu a ve spouštěči aplikací Office 365?

## <a name="use-a-custom-logo"></a>Použití vlastního loga

Použití vlastního loga:

1. Vytvořte logo o rozměrech 215 × 215 pixelů a uložte ho ve formátu PNG.
1. Vzhledem k tomu, že jste již našli aplikaci, vyberte ji.
1. V levém podokně vyberte **Vlastnosti**.
1. Nahrajte logo.
1. Až budete hotovi, vyberte **Uložit**. 

    ![Ukazuje, jak změnit logo na stránce Vlastnosti aplikace.](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > Miniatura zobrazená v tomto podokně **Vlastnosti** se neaktualizuje hned. Chcete-li zobrazit aktualizovanou ikonu, můžete vlastnosti zavřít a znovu otevřít.

## <a name="next-steps"></a>Další kroky

Teď, když jste přidali aplikaci do organizace Azure AD, [zvolte metodu jednotného přihlášení,](what-is-single-sign-on.md#choosing-a-single-sign-on-method) kterou chcete použít, a podívejte se na příslušný článek níže:

- [Konfigurace jednotného přihlašování pomocí SAML](configure-single-sign-on-non-gallery-applications.md)
- [Konfigurace jednotného přihlašování hesla](configure-password-single-sign-on-non-gallery-applications.md)
- [Konfigurace propojeného přihlašování](configure-linked-sign-on.md)
