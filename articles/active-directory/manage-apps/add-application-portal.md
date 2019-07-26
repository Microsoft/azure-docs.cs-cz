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
ms.openlocfilehash: 466660a1e064ef41eb330b36107dbdcb1d097498
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477304"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-tenant"></a>Rychlý start: Přidání aplikace do tenanta Azure Active Directory

Azure Active Directory (Azure AD) nabízí galerii obsahující tisíce předem integrovaných aplikací. Galerie pravděpodobně obsahuje i některé aplikace, které využívá vaše organizace. V tomto rychlém startu se pomocí webu Azure Portal přidá aplikace z galerie do tenanta Azure Active Directory (Azure AD).

Po přidání aplikace do tenanta Azure AD můžete:

- Umožňuje spravovat přístup uživatelů k aplikaci pomocí zásad podmíněného přístupu.
- Konfigurovat jednotné přihlašování uživatelů k aplikaci pomocí jejich účtů Azure AD.

## <a name="before-you-begin"></a>Před zahájením

K přidání aplikace do tenanta budete potřebovat:

- Předplatné Azure AD
- Předplatné s povoleným jednotným přihlašováním pro vaši aplikaci

Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce vašeho tenanta Azure AD, správce cloudové aplikace nebo správce aplikace.

K testování postupů v tomto kurzu doporučujeme použít neprodukční prostředí. Pokud nemáte neprodukční prostředí Azure AD, můžete [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-an-application-to-your-azure-ad-tenant"></a>Přidání aplikace do tenanta Azure AD

Přidání aplikace z galerie do tenanta Azure AD:

1. V [Azure Portal](https://portal.azure.com)v levém navigačním panelu vyberte možnost **Azure Active Directory**.
1. V podokně **Azure Active Directory** vyberte možnost **podnikové aplikace**.
1. Otevře se podokno **všechny aplikace** , ve kterém se zobrazí náhodná ukázka aplikací v TENANTOVI Azure AD. V horní části podokna **všechny aplikace** vyberte **Nová aplikace** a přidejte do svého tenanta aplikaci Galerie.

    ![Vyberte Nová aplikace a přidejte do svého tenanta aplikaci Galerie.](media/add-application-portal/new-application.png)

1. V podokně **kategorie** uvidíte ikony v oblasti **vybrané aplikace** , které jsou náhodnou ukázkou aplikací v galerii. Pokud chcete zobrazit více aplikací, můžete vybrat **Zobrazit více** , ale nedoporučujeme si tento způsob prohledávat, protože galerie obsahuje tisíce aplikací.

    ![Hledání aplikace podle názvu nebo kategorie](media/add-application-portal/categories.png)

1. Chcete-li vyhledat aplikaci, zadejte v části **Přidat z Galerie**název aplikace, kterou chcete přidat. Vyberte aplikaci z výsledků a vyberte **Přidat**. Následující příklad ukazuje formulář **Přidat aplikaci** , který se zobrazí po hledání GitHub.com.

    ![Ukazuje, jak přidat aplikaci z galerie.](media/add-application-portal/add-an-application.png)

1. Ve formuláři pro konkrétní aplikaci můžete změnit informace o vlastnostech. Například můžete upravit název aplikace, aby odpovídal potřebám vaší organizace. V tomto příkladu se používá název **GitHub-test**.
1. Až dokončíte provádění změn vlastností, vyberte **Přidat**.
1. Zobrazí se stránka Začínáme s možnostmi konfigurace aplikace pro vaši organizaci.

Dokončili jste přidávání aplikace. Můžete si udělat pauzu. V následujících částech se dozvíte, jak změnit logo a upravit další vlastnosti aplikace.

## <a name="find-your-azure-ad-tenant-application"></a>Vyhledání aplikace tenanta Azure AD

Předpokládejme, že jste se museli vzdálit a teď se vracíte dokončit konfiguraci aplikace. První věc, kterou je potřeba udělat, je vyhledání vaší aplikace.

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.
1. V podokně **Azure Active Directory** vyberte možnost **podnikové aplikace**.
1. Z rozevírací nabídky **Typ aplikace** vyberte **všechny aplikace**a pak vyberte **použít**. Další informace o možnostech zobrazení najdete v tématu [Zobrazení aplikací tenanta](view-applications-portal.md).
1. Zobrazí se seznam všech aplikací ve vašem tenantovi Azure AD. Tento seznam představuje náhodný vzorek. Pokud chcete zobrazit více aplikací, vyberte **Zobrazit více** než jednou nebo vícekrát.
1. Pokud chcete rychle najít aplikaci ve vašem tenantovi, zadejte název aplikace do vyhledávacího pole a vyberte **použít**. Tento příklad najde aplikaci GitHub-test, kterou jste přidali dříve.

    ![Ukazuje, jak najít aplikaci pomocí vyhledávacího pole.](media/add-application-portal/find-application.png)

## <a name="configure-user-sign-in-properties"></a>Konfigurace vlastností přihlašování uživatelů

Teď, když jste našli aplikaci, ji můžete otevřít a nakonfigurovat její vlastnosti.

Úprava vlastností aplikace:

1. Vyberte aplikaci a otevřete ji.
1. Výběrem **vlastnosti** otevřete podokno vlastnosti pro úpravy.

    ![Zobrazí vlastnosti obrazovky a upravitelné vlastnosti aplikace.](media/add-application-portal/edit-properties.png)

1. Seznamte se s možnostmi přihlašování. Možnosti určují, jak se uživatelé, kteří jsou přiřazeni nebo nepřiřazeni k aplikaci, mohou přihlásit do aplikace. A také možnosti určují, jestli uživatel uvidí aplikaci na přístupovém panelu.

    - Možnost **Mohou se uživatelé přihlásit?** určuje, jestli se můžou přihlásit uživatelé přiřazení k aplikaci.
    - **Vyžadováno přiřazení uživatele** určuje, jestli se uživatelé, kteří nejsou přiřazeni k aplikaci, můžou přihlásit.
    - Možnost **Uvidí ji uživatelé?** určuje, jestli se uživatelům přiřazeným k aplikaci zobrazí aplikace na přístupovém panelu a ve spouštěči O365.

1. Následující tabulky vám pomůžou zvolit nejvhodnější možnosti pro vaše potřeby.

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
       | Povolit uživatelům přihlášení? | Je přiřazení uživatelů povinné? | Uvidí ji uživatelé? | Mohou se nepřiřazení uživatelé přihlásit? | Zobrazí se aplikace nepřiřazeným uživatelům?* |
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
1. Vzhledem k tomu, že jste aplikaci již našli, vyberte ji.
1. V levém podokně vyberte možnost **vlastnosti**.
1. Nahrajte logo.
1. Až budete hotovi, vyberte **Uložit**.

    ![Ukazuje, jak změnit logo na stránce vlastností aplikace.](media/add-application-portal/change-logo.png)

## <a name="next-steps"></a>Další kroky

Teď, když jste přidali aplikaci do vaší organizace Azure AD, [Vyberte metodu jednotného přihlašování](what-is-single-sign-on.md#choosing-a-single-sign-on-method) , kterou chcete použít, a podívejte se na příslušný článek níže:

- [Konfigurace jednotného přihlašování založeného na SAML](configure-single-sign-on-non-gallery-applications.md)
- [Konfigurace jednotného přihlašování k heslům](configure-password-single-sign-on-non-gallery-applications.md)
- [Konfigurace propojeného přihlašování](configure-linked-sign-on.md)
