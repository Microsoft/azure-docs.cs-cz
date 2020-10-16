---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s SKYSITE | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SKYSITE.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2019
ms.author: jeedes
ms.openlocfilehash: c522ae70314a5f9bf691e935f1f171cd66c938b0
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122377"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-skysite"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s SKYSITE

V tomto kurzu se dozvíte, jak integrovat SKYSITE s Azure Active Directory (Azure AD). Když integrujete SKYSITE s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k SKYSITE.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k SKYSITE svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* SKYSITE odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SKYSITE podporuje jednotné přihlašování **IDP** .

* SKYSITE podporuje zřizování uživatelů **jenom v čase** .

## <a name="adding-skysite-from-the-gallery"></a>Přidání SKYSITE z Galerie

Pokud chcete nakonfigurovat integraci SKYSITE do služby Azure AD, musíte přidat SKYSITE z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **SKYSITE** .
1. Na panelu výsledků vyberte **SKYSITE** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-skysite"></a>Konfigurace a testování jednotného přihlašování Azure AD pro SKYSITE

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí SKYSITE pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v SKYSITE.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí SKYSITE, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE SKYSITE SSO](#configure-skysite-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte SKYSITE Test User](#create-skysite-test-user)** -to, abyste měli protějšek B. Simon v SKYSITE, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **SKYSITE** klikněte na **kartu vlastnosti** a proveďte následující krok: 

    ![Vlastnosti jednotného přihlašování](./media/skysite-tutorial/config05.png)

    * Zkopírujte **adresu URL přístupu uživatele** a musíte ji vložit v **části Konfigurace SKYSITE jednotného přihlašování**, která je vysvětlena dále v tomto kurzu.

1. Na stránce integrace aplikací **SKYSITE** přejděte do **jednotného přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V **základním oddílu konfigurace SAML** je aplikace předem nakonfigurovaná v režimu iniciované **IDP** a nezbytné adresy URL už jsou předem naplněné pomocí Azure. Uživatel musí konfiguraci uložit kliknutím na tlačítko **Uložit** .

1. SKYSITE aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na tlačítko **Upravit** ikonu otevřete dialogové okno atributy uživatele.

    ![Snímek obrazovky zobrazuje atributy uživatele s vybranou ikonou pro úpravy.](common/edit-attribute.png)

1. Kromě výše očekává aplikace SKYSITE několik dalších atributů, které se vrátí zpátky v odpovědi SAML. V dialogovém okně deklarace identity v části **atributy uživatele & deklarace** v dialogu **deklarace skupiny (Preview)** proveďte následující kroky:

    a. Klikněte na **pero** vedle **skupin vrácených v deklaraci identity**.

    ![Snímek obrazovky zobrazuje deklarace identity uživatelů s možností přidání nové deklarace identity.](./media/skysite-tutorial/config01.png)

    ![Snímek obrazovky se zobrazí dialogové okno Spravovat deklarace identity uživatelů, kde můžete zadat hodnoty, které jsou popsány.](./media/skysite-tutorial/config02.png)

    b. V seznamu přepínačů vyberte **všechny skupiny** .

    c. Vyberte **zdrojový atribut** **ID skupiny**.

    d. Klikněte na **Uložit**.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení SKYSITE** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k SKYSITE.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **SKYSITE**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="configure-skysite-sso"></a>Konfigurace jednotného přihlašování SKYSITE

1. Otevřete nové okno webového prohlížeče a přihlaste se k webu SKYSITE společnosti jako správce a proveďte následující kroky:

4. V pravém horním rohu stránky klikněte na **Nastavení** a potom přejděte na **Nastavení účtu**.

    ![Snímek obrazovky se zobrazí nastavení účtu vybrané z nastavení.](./media/skysite-tutorial/config03.png)

5. Přepněte na kartu **jednotného přihlašování (SSO)** , proveďte následující kroky:

    ![Snímek obrazovky se zobrazí na kartě jednotného přihlašování, kde můžete zadat hodnoty, které jsou popsány.](./media/skysite-tutorial/config04.png)

    a. Do textového pole **Adresa URL pro přihlášení zprostředkovatele identity** vložte hodnotu **Adresa URL přístupu uživatele**, kterou jste zkopírovali z karty **vlastnosti** v Azure Portal.

    b. Klikněte na **nahrát certifikát**a nahrajte certifikát kódovaný v kódování Base64, který jste stáhli z Azure Portal.

    c. Klikněte na **Uložit**.

### <a name="create-skysite-test-user"></a>Vytvořit testovacího uživatele SKYSITE

V této části se v SKYSITE vytvoří uživatel s názvem Britta Simon. SKYSITE podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v SKYSITE neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici SKYSITE, měli byste se automaticky přihlásit k SKYSITE, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si SKYSITE s Azure AD](https://aad.portal.azure.com/)

