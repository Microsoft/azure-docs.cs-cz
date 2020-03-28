---
title: 'Kurz: Integrace služby Azure Active Directory s přehlednou revizí | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi službou Azure Active Directory a clear review.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8264159a-11a2-4a8c-8285-4efea0adac8c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f2a0560163f9806053f49944cbec0db2b1a9de8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67105467"
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>Kurz: Integrace služby Azure Active Directory s přehlednou recenzí

V tomto kurzu se dozvíte, jak integrovat clear review s Azure Active Directory (Azure AD).
Integrace clear review s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k vymazat kontrolu.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k clear review (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí programu Clear Review, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Vymazat kontrolu předplatného s povoleným jedním přihlášením

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Clear Review podporuje **sp a IDP** inicioval sso

## <a name="adding-clear-review-from-the-gallery"></a>Přidání přehledné recenze z galerie

Chcete-li nakonfigurovat integraci clear review do Azure AD, musíte přidat Clear Review z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat přehled nouzi z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Vymazat recenzi**, vyberte **Vymazat recenzi** z panelu výsledků a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![Vymazat recenzi v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí programu Clear Review na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v přehledné kontrole.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí programu Clear Review, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace příkazu Vymazat kontrolu jednotného přihlašování](#configure-clear-review-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte jasné recenze test uživatele](#create-clear-review-test-user)** – mít protějšek Britta Simon v jasné recenzi, která je propojena s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí programu Clear Review, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce Integrace aplikace **Clear Review** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující kroky:

    ![Vymazat informace o doméně a adresách URL pro jednotlivá přihlašování](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://<customer name>.clearreview.com/sso/metadata/`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<customer name>.clearreview.com/sso/acs/`

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![Vymazat informace o doméně a adresách URL pro jednotlivá přihlašování](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<customer name>.clearreview.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL pro odpověď a přihlašovací adresou URL. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory klienta Clear Review.](https://clearreview.com/contact/) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

6. Aplikace Clear Review očekává kontrolní výrazy SAML v určitém formátu, který vyžaduje přidání vlastních mapování atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky zobrazuje seznam výchozích atributů, kde jako **nameidentifier** je mapován s **user.userprincipalname**. Aplikace Clear Review očekává, že **identifikátor nameidentifier** bude mapován pomocí **souboru user.mail**, takže je třeba upravit mapování atributů kliknutím na ikonu **Upravit** a změnit mapování atributů.

    ![image](common/edit-attribute.png)

7. V dialogovém okně **Atributy uživatele & deklarace identity** proveďte následující kroky:

    a. Vpravo od hodnoty **identifikátoru názvu**klepněte na **ikonu Upravit** .

    ![image](./media/clearreview-tutorial/attribute02.png)

    ![image](./media/clearreview-tutorial/attribute01.png)

    b. Ze seznamu **Atribut Zdroj** vyberte hodnotu atributu **user.mail** pro daný řádek.

    c. Klikněte na **Uložit**.

8. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

9. V části **Nastavit vymazat recenzi** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-clear-review-single-sign-on"></a>Konfigurace jednotného přihlašování k clear review

1. Pokud chcete nakonfigurovat jednotné přihlašování na straně **Clear Review,** otevřete portál **Clear Review** s přihlašovacími údaji správce.

2. V levém navigačním panelu vyberte **Správce.**

    ![Tlačítko Konfigurovat ukládání jednotného přihlášení](./media/clearreview-tutorial/tutorial_clearreview_app_admin1.png)

3. V části **Integrace** v dolní části stránky klikněte na tlačítko **Změnit** vpravo od **nastavení jednotného přihlašování**.

    ![Tlačítko Konfigurovat ukládání jednotného přihlášení](./media/clearreview-tutorial/tutorial_clearreview_app_admin2.png)

4. Provedení následujících kroků na stránce **Nastavení jednotného přihlášení**

    ![Tlačítko Konfigurovat ukládání jednotného přihlášení](./media/clearreview-tutorial/tutorial_clearreview_app_admin3.png)

    a. V textovém poli **Adresa URL vystavitistu** vložte hodnotu **identifikátoru Azure AD,** kterou jste zkopírovali z webu Azure Portal.

    b. Do textového pole **koncový bod SAML** vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z webu Azure Portal.  

    c. V textovém poli **SLO Endpoint** vložte hodnotu **adresy URL odhlášení,** kterou jste zkopírovali z webu Azure Portal.  

    d. Otevřete stažený certifikát v poznámkovém bloku a vložte obsah do textového pole **Certifikát X.509.**   

    e. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** zadejte **\@brittasimon vašecompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k clear review.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte Vymazat **kontrolu**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Vymazat recenzi**.

    ![Odkaz Vymazat recenzi v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-clear-review-test-user"></a>Vytvořit uživatele testu clear review

V této části vytvoříte uživatele s názvem Britta Simon v vymazat recenzi. Chcete-li přidat uživatele na platformě Clear Review, obraťte se na [tým podpory Clear Review.](https://clearreview.com/contact/)

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Vymazat recenzi na přístupovém panelu, měli byste být automaticky přihlášeni k vymazat kontrolu, pro které nastavíte přistupující ho. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

