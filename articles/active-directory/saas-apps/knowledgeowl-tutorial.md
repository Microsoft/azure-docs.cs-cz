---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s KnowledgeOwl | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a KnowledgeOwl.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.openlocfilehash: fc6cb5d992d4c961e23034560a688c7090622359
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91858092"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-knowledgeowl"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s KnowledgeOwl

V tomto kurzu se dozvíte, jak integrovat KnowledgeOwl s Azure Active Directory (Azure AD). Když integrujete KnowledgeOwl s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k KnowledgeOwl.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k KnowledgeOwl svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* KnowledgeOwl odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* KnowledgeOwl podporuje jednotné přihlašování (SSO) **a IDP** .
* KnowledgeOwl podporuje zřizování uživatelů **jenom v čase** .

## <a name="adding-knowledgeowl-from-the-gallery"></a>Přidání KnowledgeOwl z Galerie

Pokud chcete nakonfigurovat integraci KnowledgeOwl do služby Azure AD, musíte přidat KnowledgeOwl z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **KnowledgeOwl** .
1. Na panelu výsledků vyberte **KnowledgeOwl** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-knowledgeowl"></a>Konfigurace a testování jednotného přihlašování Azure AD pro KnowledgeOwl

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí KnowledgeOwl pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v KnowledgeOwl.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí KnowledgeOwl, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte KNOWLEDGEOWL SSO](#configure-knowledgeowl-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte KnowledgeOwl Test User](#create-knowledgeowl-test-user)** -to, abyste měli protějšek B. Simon v KnowledgeOwl, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **KnowledgeOwl** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:
    
    ```http
    https://app.knowledgeowl.com/sp
    https://app.knowledgeowl.com/sp/id/<unique ID>
    ```

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Tuto hodnotu budete muset aktualizovat ze skutečného identifikátoru, adresy URL odpovědi a Sign-On URL, které jsou vysvětleny dále v tomto kurzu.

1. KnowledgeOwl aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace KnowledgeOwl několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Název | Zdrojový atribut | Obor názvů |
    | ------------ | -------------------- | -----|
    | ssoid | uživatel. pošta | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **certifikát (RAW)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificateraw.png)

1. V části **Nastavení KnowledgeOwl** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k KnowledgeOwl.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **KnowledgeOwl**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-knowledgeowl-sso"></a>Konfigurace jednotného přihlašování KnowledgeOwl

1. V jiném okně webového prohlížeče se přihlaste k webu KnowledgeOwl společnosti jako správce.

1. Klikněte na **Nastavení** a pak vyberte **zabezpečení**.

    ![Snímek obrazovky zobrazuje zabezpečení vybraný v nabídce nastavení.](./media/knowledgeowl-tutorial/configure1.png)

1. Přejděte do **integrace SAML SSO** a proveďte následující kroky:

    ![Snímek obrazovky ukazuje integraci S protokolem SAML S S O, kde můžete provádět změny popsané tady.](./media/knowledgeowl-tutorial/configure2.png)

    a. Vyberte možnost **Povolit jednotné přihlašování SAML**.

    b. Zkopírujte hodnotu **ID entity SP** a vložte ji do **identifikátoru (ID entity)** v části **základní konfigurace SAML** na Azure Portal.

    c. Zkopírujte hodnotu **Adresa URL pro přihlášení k SP** a vložte ji do polí **Adresa URL pro přihlášení a text adresy URL odpovědi** v části **základní konfigurace SAML** na Azure Portal.

    d. Do textového pole **IDP entityID** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    e. Do textového pole **Adresa URL pro přihlášení IDP** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    f. Do textového pole **Adresa URL pro odhlášení IDP** vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal

    například Kliknutím na **nahrát IDP certifikát**nahrajte stažený certifikát Azure Portal na formulář.

    h. Klikněte na **mapování atributů SAML** pro mapování atributů a proveďte následující kroky:

    ![Snímek obrazovky ukazuje mapy atributů SAML, kde můžete provádět změny popsané tady.](./media/knowledgeowl-tutorial/configure3.png)

    * Zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` do textového pole **ID jednotného přihlašování** .
    * `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`Do textového pole **uživatelské jméno/e-mail** zadejte.
    * `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`Do textového pole **název zadejte jméno** .
    * `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`Do textového pole **příjmení** zadejte.
    * Klikněte na **Uložit**.

    i. V dolní části stránky klikněte na **Uložit**.

    ![Snímek obrazovky ukazuje tlačítko Uložit.](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-knowledgeowl-test-user"></a>Vytvořit testovacího uživatele KnowledgeOwl

V této části se v KnowledgeOwl vytvoří uživatel s názvem B. Simon. KnowledgeOwl podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v KnowledgeOwl neexistuje, vytvoří se po ověření nový.

> [!Note]
> Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory KnowledgeOwl](mailto:support@knowledgeowl.com).

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici KnowledgeOwl, měli byste se automaticky přihlásit k KnowledgeOwl, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si KnowledgeOwl s Azure AD](https://aad.portal.azure.com/)