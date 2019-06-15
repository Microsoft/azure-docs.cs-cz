---
title: Přidání aplikace do tenanta Azure Active Directory | Microsoft Docs
description: V tomto rychlém startu se pomocí webu Azure Portal přidá aplikace z galerie do tenanta Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 04/09/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1688cc9317306e7f524536754854b6147ad3dd5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108768"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-tenant"></a>Rychlý start: Přidání aplikace do svého tenanta Azure Active Directory

Azure Active Directory (Azure AD) nabízí galerii obsahující tisíce předem integrovaných aplikací. Galerie pravděpodobně obsahuje i některé aplikace, které využívá vaše organizace. V tomto rychlém startu se pomocí webu Azure Portal přidá aplikace z galerie do tenanta Azure Active Directory (Azure AD).

Po přidání aplikace do tenanta Azure AD můžete:

- Správa přístupu uživatelů k aplikaci pomocí zásad podmíněného přístupu.
- Konfigurovat jednotné přihlašování uživatelů k aplikaci pomocí jejich účtů Azure AD.

## <a name="before-you-begin"></a>Než začnete

K přidání aplikace do tenanta budete potřebovat:

- Předplatné Azure AD
- Předplatné s povoleným jednotným přihlašováním pro vaši aplikaci

Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce vašeho tenanta Azure AD, správce cloudové aplikace nebo správce aplikace.

K testování postupů v tomto kurzu doporučujeme použít neprodukční prostředí. Pokud nemáte neprodukční prostředí Azure AD, můžete [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-an-application-to-your-azure-ad-tenant"></a>Přidání aplikace do tenanta Azure AD

Přidání aplikace z galerie do tenanta Azure AD:

1. V [webu Azure portal](https://portal.azure.com), v levém navigačním panelu vyberte **Azure Active Directory**.

1. V **Azure Active Directory** vyberte **podnikové aplikace**.

    ![Otevřené podnikové aplikace](media/add-application-portal/open-enterprise-apps.png)

1. **Všechny aplikace** se otevře podokno zobrazíte náhodného vzorku aplikací ve vašem tenantovi Azure AD. Vyberte **novou aplikaci** v horní části **všechny aplikace** podokně.

    ![Nová aplikace](media/add-application-portal/new-application.png)

1. V **kategorie** podokně, zobrazí se ikony v rámci **vybrané aplikace** oblast, která jsou náhodného vzorku Galerie aplikací.  Pokud chcete zobrazit další aplikace, můžete vybrat **zobrazit více**. Ale nedoporučujeme hledání tímto způsobem, protože existují nepřeberným množstvím aplikací v galerii.

    ![Hledání podle názvu nebo kategorie](media/add-application-portal/categories.png)

1. K vyhledání aplikace, v části **přidat z Galerie**, zadejte název aplikace, které chcete přidat. Vyberte aplikaci z výsledků a vyberte **přidat**. Následující příklad ukazuje **přidat aplikaci** formulář, který se zobrazí po hledání webu github.com.

    ![Přidání aplikace](media/add-application-portal/add-an-application.png)

1. Ve formuláři pro konkrétní aplikaci můžete změnit informace o vlastnostech. Například můžete upravit název aplikace, aby odpovídal potřebám vaší organizace. V tomto příkladu se používá název **GitHub-test**.

1. Po dokončení změn vlastností, vyberte **přidat**.

1. Zobrazí se stránka Začínáme s možnostmi konfigurace aplikace pro vaši organizaci.

Dokončení přidání aplikace. Můžete si udělat pauzu. V následujících částech se dozvíte, jak změnit logo a upravit další vlastnosti aplikace.

## <a name="find-your-azure-ad-tenant-application"></a>Vyhledání aplikace tenanta Azure AD

Předpokládejme, že jste se museli vzdálit a teď se vracíte dokončit konfiguraci aplikace. Prvním krokem je najít aplikace.

1. V  **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu vyberte **Azure Active Directory**.

1. V **Azure Active Directory** vyberte **podnikové aplikace**.

1. Z **typ aplikace** rozevírací nabídky vyberte **všechny aplikace**a pak vyberte **použít**. Další informace o možnostech zobrazení najdete v tématu [Zobrazení aplikací tenanta](view-applications-portal.md).

1. Zobrazí se seznam všech aplikací ve vašem tenantovi Azure AD. Tento seznam představuje náhodný vzorek. Pokud chcete zobrazit další aplikace, vyberte **zobrazit více** jednou nebo vícekrát.

1. K rychlému vyhledání aplikace ve vašem tenantovi, do vyhledávacího pole zadejte název aplikace a vyberte **použít**. Tento příklad vyhledá Githubu a testování aplikací, přidali dříve.

    ![Vyhledání aplikace](media/add-application-portal/find-application.png)


## <a name="configure-user-sign-in-properties"></a>Konfigurace vlastností přihlašování uživatelů

Teď, když jste nalezené aplikace, můžete ji otevřete a nakonfigurujte vlastnosti aplikace.

Postup úpravy vlastností aplikace:

1. Vyberte aplikaci, otevřete ho.
1. Vyberte **vlastnosti** a otevřete tak podokno vlastností pro úpravy.

    ![Upravit vlastnosti podokno](media/add-application-portal/edit-properties.png)

1. Seznamte se s možnostmi přihlašování. Možnosti určují, jak můžete uživatelé, kteří jsou přiřazeny nebo Nepřiřazení k aplikaci přihlásit do aplikace. A možnosti, také určují, pokud uživatel můžete zobrazit aplikace na přístupovém panelu.

    - Možnost **Mohou se uživatelé přihlásit?** určuje, jestli se můžou přihlásit uživatelé přiřazení k aplikaci.
    - **Přiřazení uživatelů povinné** Určuje, jestli uživatelé, kteří nemají přiřazenou aplikaci mohou přihlásit.
    - Možnost **Uvidí ji uživatelé?** určuje, jestli se uživatelům přiřazeným k aplikaci zobrazí aplikace na přístupovém panelu a ve spouštěči O365.

1. Pomocí následující tabulky vám pomůžou vybrat nejlepší možnosti pro vaše potřeby.

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
       | Mohou se uživatelé přihlásit? | Je přiřazení uživatelů povinné? | Uvidí ji uživatelé? | Mohou se nepřiřazení uživatelé přihlásit? | Zobrazí se aplikace nepřiřazeným uživatelům?* |
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
1. Protože jste již nenachází vaší aplikace, vyberte aplikaci.
1. V levém podokně vyberte **vlastnosti**.
1. Nahrajte logo.
1. Jakmile budete hotovi, vyberte **Uložit**.

    ![Změna loga](media/add-application-portal/change-logo.png)


## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste se dozvěděli, jak do tenanta Azure AD přidat aplikaci z galerie. Zjistili jste, jak upravit vlastnosti aplikace.

Teď jste připraveni nakonfigurovat pro aplikaci jednotné přihlašování.

> [!div class="nextstepaction"]
> [Konfigurace jednotného přihlašování](configure-single-sign-on-portal.md)


