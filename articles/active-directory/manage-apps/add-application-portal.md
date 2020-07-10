---
title: 'Rychlý Start: Přidání aplikace do tenanta Azure Active Directory (Azure AD)'
description: V tomto rychlém startu se pomocí webu Azure Portal přidá aplikace z galerie do tenanta Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: f93a25854bc8b93e9bd75ea71037f6363d143914
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145725"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-azure-ad-tenant"></a>Rychlý Start: Přidání aplikace do tenanta Azure Active Directory (Azure AD)

Azure Active Directory (Azure AD) nabízí galerii obsahující tisíce předem integrovaných aplikací. Mnohé z aplikací, které vaše organizace používá, jsou pravděpodobně již v galerii.

Po přidání aplikace do tenanta Azure AD můžete:

- Nakonfigurujte vlastnosti aplikace.
- Umožňuje spravovat přístup uživatelů k aplikaci pomocí zásad podmíněného přístupu.
- Nakonfigurujte jednotné přihlašování, aby se uživatelé mohli přihlašovat do aplikace pomocí svých přihlašovacích údajů Azure AD.

## <a name="prerequisites"></a>Požadavky

Pokud chcete přidat aplikaci do tenanta Azure AD, budete potřebovat:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Jedna z následujících rolí: globální správce, správce cloudové aplikace, správce aplikace nebo vlastník instančního objektu.
- (Volitelné: dokončování [zobrazení vašich aplikací](view-applications-portal.md))

>[!IMPORTANT]
>K otestování kroků v tomto rychlém startu doporučujeme použít jiné než produkční prostředí.

## <a name="add-an-app-to-your-azure-ad-tenant"></a>Přidání aplikace do tenanta Azure AD

Přidání aplikace do tenanta služby Azure AD:

1. V [Azure Portal](https://portal.azure.com)v levém navigačním panelu vyberte možnost **Azure Active Directory**.
2. V podokně **Azure Active Directory** vyberte možnost **podnikové aplikace**. Otevře se podokno **všechny aplikace** a zobrazí se náhodná ukázka aplikací v TENANTOVI Azure AD.
3. V podokně **podnikové aplikace** vyberte **Nová aplikace**. 
    ![Vyberte Nová aplikace a přidejte do svého tenanta aplikaci Galerie.](media/add-application-portal/new-application.png)
4. Přepněte na nové prostředí verze Preview: v banneru v horní části **stránky přidat aplikaci**vyberte odkaz, který uvádí, jestli **chcete vyzkoušet novou a vylepšenou galerii aplikací kliknutím sem**.
5. Otevře se podokno **Procházet Azure AD Gallery (Preview)** a zobrazí dlaždice pro cloudové platformy, místní aplikace a doporučené aplikace. Aplikace uvedené v části **vybrané aplikace** obsahují ikony, které označují, jestli podporují federované jednotné přihlašování (SSO) a zřizování.
    ![Hledání aplikace podle názvu nebo kategorie](media/add-application-portal/browse-gallery.png)
6. Můžete procházet galerii pro aplikaci, kterou chcete přidat, nebo vyhledat aplikaci zadáním jejího názvu do vyhledávacího pole. Pak vyberte aplikaci z výsledků. Ve formuláři můžete upravit název aplikace tak, aby odpovídala potřebám vaší organizace. V tomto příkladu jsme vybrali GitHub a změnili jsme název na **GitHub-test**.
    ![Ukazuje, jak přidat aplikaci z galerie.](media/add-application-portal/create-application.png)
    >[!TIP]
    >Pokud aplikace, kterou hledáte, není v galerii, pak můžete kliknout na odkaz **vytvořit vlastní aplikaci**. Společnost Microsoft již spolupracuje s řadou vývojářů aplikací, aby je bylo možné předem nakonfigurovat pro práci s Azure AD. Toto jsou aplikace, které se zobrazují v galerii. Pokud ale aplikace, kterou chcete přidat, není uvedená v seznamu, můžete vytvořit novou, obecnou, aplikaci a nakonfigurovat ji sami nebo s pokyny pro vývojáře, který ho vytvořil.
7. Vyberte **Vytvořit**. Zobrazí se stránka Začínáme s možnostmi konfigurace aplikace pro vaši organizaci.

Dokončili jste přidávání aplikace. V dalším rychlém startu se dozvíte, jak změnit logo a upravit další vlastnosti aplikace.

## <a name="next-steps"></a>Další kroky

- [Konfigurace aplikace](add-application-portal-configure.md)
- [Nastavení jednotného přihlašování](add-application-portal-setup-sso.md)
- [Odstranění aplikace](delete-application-portal.md)