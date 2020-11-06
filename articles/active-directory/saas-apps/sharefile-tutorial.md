---
title: 'Kurz: Azure Active Directory integrace s Citrix ShareFile | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Citrix ShareFile.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/04/2020
ms.author: jeedes
ms.openlocfilehash: ac7382f60d5c49ac8c784566af4d9db8ea78956b
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331282"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Kurz: Azure Active Directory integrace s Citrix ShareFile

V tomto kurzu se dozvíte, jak integrovat Citrix ShareFile s Azure Active Directory (Azure AD).
Integrace Citrix ShareFile s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Citrix ShareFile.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Citrix ShareFile (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Citrix ShareFile potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným jedním přihlašováním Citrix ShareFile

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Citrix ShareFile podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-citrix-sharefile-from-the-gallery"></a>Přidání Citrix ShareFile z Galerie

Pokud chcete nakonfigurovat integraci Citrix ShareFile do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Citrix ShareFile z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Citrix ShareFile** .
1. Z panelu výsledků vyberte **Citrix ShareFile** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Citrix ShareFile na základě testovacího uživatele s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v systému Citrix ShareFile.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Citrix ShareFile, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
2. **[Nakonfigurujte Citrix SHAREFILE SSO](#configure-citrix-sharefile-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
    * **[Vytvořte uživatele Citrix ShareFile Test User](#create-citrix-sharefile-test-user)** – abyste měli protějšek Britta Simon v Citrix ShareFile, který je propojený s reprezentací uživatele v Azure AD.
3. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Citrix ShareFile** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<tenant-name>.sharefile.com/saml/login`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:

    - `https://<tenant-name>.sharefile.com`
    - `https://<tenant-name>.sharefile.com/saml/info`
    - `https://<tenant-name>.sharefile1.com/saml/info`
    - `https://<tenant-name>.sharefile1.eu/saml/info`
    - `https://<tenant-name>.sharefile.eu/saml/info`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:
    
    - `https://<tenant-name>.sharefile.com/saml/acs`
    - `https://<tenant-name>.sharefile.eu/saml/<URL path>`
    - `https://<tenant-name>.sharefile.com/saml/<URL path>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným Sign-On URL, identifikátor a adresu URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klientů Citrix ShareFile](https://www.citrix.co.in/products/citrix-content-collaboration/support.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení Citrix ShareFile** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory** , vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Citrix ShareFile.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Citrix ShareFile**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-citrix-sharefile-sso"></a>Konfigurace Citrix ShareFile SSO

1. V jiném okně webového prohlížeče se přihlaste k webu **Citrix ShareFile** společnosti jako správce.

1. Na **řídicím panelu** klikněte na **Nastavení** a vyberte **Nastavení správce** .

    ![Správa](./media/sharefile-tutorial/settings.png)

1. V nastavení pro správu přejdete **do části zabezpečení**  ->  **přihlášení & zásady zabezpečení**.
   
    ![Správa účtu](./media/sharefile-tutorial/settings-security.png "Správa účtu")

1. Na stránce s **jednotným přihlašováním/konfigurací SAML 2,0** v části **základní nastavení** proveďte následující kroky:
   
    ![Jednotné přihlašování](./media/sharefile-tutorial/saml-configuration.png "Jednotné přihlašování")
   
    a. V části **Povolit SAML** vyberte **Ano** .

    b. Zkopírujte hodnotu **ID vystavitele nebo ID entity ShareFile** a vložte ji do pole **Adresa URL identifikátoru** v DIALOGOVÉM okně **základní konfigurace SAML** v Azure Portal.
    
    c. Do textového pole **IDP vystavitele/ID entity** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    d. Klikněte na **změnit** vedle pole **certifikát X. 509** a pak Nahrajte certifikát, který jste stáhli z Azure Portal.
    
    e. Do textového pole **Adresa URL pro přihlášení** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.
    
    f. Do textového pole **Adresa URL pro odhlášení** vložte hodnotu **adresy URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

5. Na portálu pro správu Citrix ShareFile klikněte na **Uložit** .

## <a name="create-citrix-sharefile-test-user"></a>Vytvoření testovacího uživatele Citrix ShareFile

1. Přihlaste se k tenantovi **Citrix ShareFile** .

2. Klikněte na **lidé**  ->  **Spravovat uživatele domů**  ->  **vytvořit nový uživatel**  ->  **vytvořit zaměstnance**.
   
    ![Vytvořit zaměstnance](./media/sharefile-tutorial/create-user.png "Vytvořit zaměstnance")

3. V části **základní informace** proveďte následující kroky:
   
    ![Základní informace](./media/sharefile-tutorial/user-form.png "Základní informace")
   
    a. Do textového pole **název** **Zadejte jméno** uživatele jako **Britta**.
   
    b.  Do textového pole **příjmení** zadejte příjmení User as **Simon** ( **jméno** uživatele).
   
    c. Do textového pole **e-mailová adresa** zadejte e-mailovou adresu Britta Simon jako **brittasimon \@ contoso.com**.

4. Klikněte na **Add User** (Přidat uživatele).
  
    >[!NOTE]
    >Držitel účtu Azure AD obdrží e-mail a provede odkaz k potvrzení jeho účtu předtím, než se aktivuje. K zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytvoření uživatelského účtu Citrix ShareFile nebo rozhraní API od společnosti Citrix ShareFile.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

1. Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k Citrix ShareFile, kde můžete spustit tok přihlášení. 

2. Přejít na adresu URL pro přihlášení k Citrix ShareFile přímo a zahájit tok přihlášení.

3. Můžete použít panel Microsoft Access. Po kliknutí na dlaždici Citrix ShareFile na přístupovém panelu se tato akce přesměruje na adresu URL pro přihlášení k Citrix ShareFile. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete Citrix ShareFile, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).