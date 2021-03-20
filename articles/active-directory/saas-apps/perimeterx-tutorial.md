---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s PerimeterX | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a PerimeterX.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/27/2020
ms.author: jeedes
ms.openlocfilehash: 29ffaaa1b1b6efbcd5523a76018c92645e13d187
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181788"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-perimeterx"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s PerimeterX

V tomto kurzu se dozvíte, jak integrovat PerimeterX s Azure Active Directory (Azure AD). Když integrujete PerimeterX s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k PerimeterX.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k PerimeterX svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* PerimeterX odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.


* PerimeterX podporuje jednotné přihlašování **IDP** .

## <a name="adding-perimeterx-from-the-gallery"></a>Přidání PerimeterX z Galerie

Pokud chcete nakonfigurovat integraci PerimeterX do služby Azure AD, musíte přidat PerimeterX z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **PerimeterX** .
1. Na panelu výsledků vyberte **PerimeterX** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-perimeterx"></a>Konfigurace a testování jednotného přihlašování Azure AD pro PerimeterX

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí PerimeterX pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v PerimeterX.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí PerimeterX postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte PERIMETERX SSO](#configure-perimeterx-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte PerimeterX Test User](#create-perimeterx-test-user)** -to, abyste měli protějšek B. Simon v PerimeterX, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **PerimeterX** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V **základní sekci konfigurace SAML** je aplikace předem nakonfigurovaná a potřebné adresy URL už jsou předem naplněné pomocí Azure. Uživatel musí konfiguraci uložit kliknutím na tlačítko **Uložit** .


1. PerimeterX aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace PerimeterX několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.
    
    | Name | Zdrojový atribut|
    | ------------ | --------- |
    | firstName | User. křestní jméno |
    | lastName  | User. příjmení |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení PerimeterX** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k PerimeterX.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **PerimeterX**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-perimeterx-sso"></a>Konfigurace jednotného přihlašování PerimeterX

1. Přihlaste se ke konzole PerimeterX s oprávněními správce.

1. Přejít na **účet správce > účty** 

    ![Jednotné přihlašování PerimeterX](./media/perimeterx-tutorial/accounts.png)

1. Klikněte na **Upravit** .

4.  V dialogovém okně Upravit účet proveďte následující kroky.

    ![Účet pro úpravu jednotného přihlašování PerimeterX](./media/perimeterx-tutorial/saml-sso.png)

    a.  Zaškrtněte **Povolit jednu Sign-On (SSO)** .

    b.  Vyberte **Azure SAML**.

    c.  Do textového pole **koncového bodu SAML** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali Azure Portal.

    d. Do textového pole **vystavitele** vložte hodnotu identifikátoru Azure AD zkopírovanou z Azure Portal.

    e. Otevřete stažený **certifikát (Base64)** z Azure Portal do programu Poznámkový blok a vložte obsah do textového pole **certifikátu X. 509** .

    f. Klikněte na **Uložit změny** .

### <a name="create-perimeterx-test-user"></a>Vytvořit testovacího uživatele PerimeterX

Pokyny, jak vytvořit testovacího uživatele PerimeterX, najdete v [příručce PerimeterX Manage users](https://docs.perimeterx.com/pxconsole/docs/managing-users) .

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

1. Klikněte na testovat tuto aplikaci v Azure Portal a měli byste se automaticky přihlášeni k PerimeterX, pro které jste nastavili jednotné přihlašování.

1. Můžete použít panel Microsoft Access. Když na přístupovém panelu kliknete na dlaždici PerimeterX, měli byste se automaticky přihlásit k PerimeterX, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování PerimeterX můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).