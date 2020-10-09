---
title: 'Kurz: Azure Active Directory integrace s přenosem MOVEit – integrace služby Azure AD | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a přenosem MOVEit – integraci Azure AD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 4ec0a3082b5978e0c540a2e0a70843081201a29b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88544120"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Kurz: Azure Active Directory integrace s přenosem MOVEit – Integrace Azure AD

V tomto kurzu se dozvíte, jak integrovat MOVEit přenos – Azure AD Integration with Azure Active Directory (Azure AD).
Integrace MOVEit transferu – integrace služby Azure AD s Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k MOVEit přenosu – Azure AD Integration.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k MOVEit přenosu – integraci Azure AD (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Pokud chcete nakonfigurovat integraci Azure AD s MOVEit přenosem Azure AD, budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* MOVEit Transfer – odběr služby Azure AD Integration Single Signing

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* MOVEit Transfer – integrace služby Azure AD podporuje jednotné přihlašování se spuštěnou službou **SP**

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Přidání přenosu MOVEit – Integrace Azure AD z Galerie

Pokud chcete nakonfigurovat integraci MOVEit přenosu – Integrace Azure AD do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat MOVEit přenos – Azure AD Integration z galerie.

**Pokud chcete přidat MOVEit přenos – integraci Azure AD z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **MOVEit Transfer – Azure AD Integration**, vyberte **MOVEit Transfer – Integrace Azure AD** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![MOVEit Transfer – Integrace Azure AD v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s využitím služby MOVEit Transfer – Integrace Azure AD na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v MOVEit přenosu – integrace služby Azure AD.

Pokud chcete konfigurovat a testovat jednotné přihlašování Azure AD pomocí služby MOVEit Transfer – Azure AD Integration, je potřeba, abyste dokončili tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte MOVEit Transfer – jednotné přihlašování Azure AD Integration](#configure-moveit-transfer---azure-ad-integration-single-sign-on)** – pro konfiguraci nastavení jednoho Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření MOVEit transferu – testovací uživatel pro integraci se službou Azure AD](#create-moveit-transfer---azure-ad-integration-test-user)** – Pokud má protějšek Britta Simon v přenosu MOVEit – integrace služby Azure AD, která je propojená s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD s integrací služby MOVEit Transfer – Azure AD, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikace **MOVEit Transfer – Azure AD** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud máte **soubor metadat poskytovatele služeb**v **základní části Konfigurace SAML** , proveďte následující kroky:

    a. Klikněte na **nahrát soubor metadat**.

    ![Nahrát soubor metadat](common/upload-metadata.png)

    b. Kliknutím na **logo složky** vyberte soubor metadat a klikněte na **nahrát**.

    ![zvolit soubor metadat](common/browse-upload-metadata.png)

    c. Po úspěšném nahrání souboru metadat se hodnota **Adresa URL** pro **identifikátor** a odpověď automaticky naplní v **základní části Konfigurace SAML** :

    ![MOVEit Transfer – informace o jednotném přihlašování v doméně Integrace Azure AD a adresách URL](common/sp-identifier-reply.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://contoso.com`

    > [!NOTE]
    > Hodnota **přihlašovací adresy URL** není reálné číslo. Aktualizujte hodnotu skutečnou adresou Sign-On. Kontaktujte [MOVEit Transfer – tým podpory klienta Azure AD Integration](https://community.ipswitch.com/s/support) pro získání hodnoty. **Soubor metadat poskytovatele služby** si můžete stáhnout z **adresy URL metadat poskytovatele služby** , která je vysvětlená dále v části **Konfigurace MOVEit přenosu – jednotné přihlašování Azure AD** v kurzu. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **nastavení přenosu MOVEit – integrace služby Azure AD** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-moveit-transfer---azure-ad-integration-single-sign-on"></a>Konfigurace přenosu MOVEit – jedna Sign-On integrace služby Azure AD

1. Přihlaste se ke klientovi MOVEit Transfer jako správce.

2. V levém navigačním podokně klikněte na **Nastavení**.

    ![Oddíl nastavení na straně aplikace](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

3. Klikněte na odkaz na **jeden jednotného** , který se nachází v **zásadách zabezpečení – > ověření uživatele**.

    ![Zásady zabezpečení na straně aplikace](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

4. Pro stažení dokumentu metadat klikněte na odkaz adresa URL metadat.

    ![Adresa URL metadat poskytovatele služby](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
   * Ověřte, že **entityID** odpovídá **identifikátoru** v části **základní konfigurace SAML** .
   * V části **základní konfigurace SAML** ověřte, že adresa URL umístění **AssertionConsumerService** odpovídá **adrese URL odpovědi** .
    
     ![Konfigurovat jednu Sign-On na straně aplikace](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

5. Klikněte na tlačítko **Přidat zprostředkovatele identity** a přidejte nového federovaného zprostředkovatele identity.

    ![Přidat zprostředkovatele identity](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

6. Klikněte na **Procházet...** a vyberte soubor metadat, který jste stáhli z Azure Portal, a pak kliknutím na **Přidat zprostředkovatele identity** nahrajte stažený soubor.

    ![Zprostředkovatel identity SAML](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

7. Vyberte možnost**Ano**, pokud je **povolená** na stránce **Upravit nastavení zprostředkovatele federovaných identit...** a klikněte na **Uložit**.

    ![Nastavení zprostředkovatele federovaných identit](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

8. Na stránce **Upravit uživatelská nastavení zprostředkovatele federovaných identit** proveďte následující akce:
    
    ![Upravit nastavení federovaného zprostředkovatele identity](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Jako **přihlašovací jméno**vyberte **SAML NameId** .
    
    b. Vyberte možnost **jiný** jako **úplný název** a do textového pole **název atributu** zadejte hodnotu: `http://schemas.microsoft.com/identity/claims/displayname` .
    
    c. Vyberte možnost **jiné** jako **e-mail** a v textovém poli **název atributu** zadejte hodnotu: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .
    
    d. V jednotného vyberte **Ano** jako **automaticky vytvořit účet**.
    
    e. Klikněte na tlačítko **Uložit** .

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon \@ yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k integraci MOVEit – Azure AD.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Moveit přenos – Integrace Azure AD**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **MOVEit přenos – integrace služby Azure AD**.

    ![Odkaz MOVEit Transfer – Azure AD Integration v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Vytvoření MOVEit transferu – testovací uživatel pro integraci Azure AD

Cílem této části je vytvořit uživatele s názvem Britta Simon v článku věnovaném přenosu MOVEit – Integrace Azure AD. MOVEit Transfer – integrace služby Azure AD podporuje zřizování za běhu, které jste povolili. V této části není žádná položka akce. Při pokusu o přístup k MOVEit přenosu – Azure AD Pokud ještě neexistuje, vytvoří se nový uživatel.

>[!NOTE]
>Pokud potřebujete ručně vytvořit uživatele, musíte se obrátit na [tým podpory služby MOVEit Transfer – Azure AD Integration Client](https://community.ipswitch.com/s/support).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici MOVEit Transfer – integrace služby Azure AD na přístupovém panelu byste měli být automaticky přihlášeni k integraci MOVEit přenosu – Azure AD, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

