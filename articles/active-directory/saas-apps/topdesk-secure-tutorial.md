---
title: 'Kurz: Azure Active Directory integrace s TOPdesk – Secure | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TOPdesk-Secure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.openlocfilehash: db7f2afa7993c8f8d2fe9fe30e5ecd4f2388b44b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88524251"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Kurz: Azure Active Directory integrace s TOPdesk – Secure

V tomto kurzu se dozvíte, jak integrovat TOPdesk-Secure s Azure Active Directory (Azure AD).
Integrace TOPdesk-Secure s Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, kteří mají přístup k TOPdesk-Secure.
* Můžete uživatelům povolit, aby se automaticky přihlásili k TOPdesk (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s TOPdesk-Secure budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* TOPdesk – odběr zabezpečeného jednotného přihlašování

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* TOPdesk – zabezpečená podpora pro jednotné přihlašování s podporou **SP**

## <a name="adding-topdesk---secure-from-the-gallery"></a>Přidání TOPdesk-Secure z Galerie

Pokud chcete nakonfigurovat integraci TOPdesk-Secure do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat TOPdesk – zabezpečený z galerie.

**Chcete-li přidat TOPdesk-Secure z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **TopDesk-Secure**, vyberte **TopDesk-Secure** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![TOPdesk – zabezpečení v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí TOPdesk – Secure na základě testovacího uživatele s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v TOPdesk.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí TOPdesk-Secure, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Konfigurace TopDesk – jednotné přihlašování](#configure-topdesk---secure-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte TopDesk – zabezpečený testový uživatel](#create-topdesk---secure-test-user)** – pro Britta Simon v TopDesk-Secure, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí TOPdesk – Secure, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **TopDesk – zabezpečení** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![TOPdesk – informace jednotného přihlašování v doméně a adresách URL](common/sp-identifier-reply.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.topdesk.net`

    b. V poli **Adresa URL identifikátoru** zadejte adresu URL metadat TopDesk, kterou můžete načíst z konfigurace TopDesk. Měl by používat následující vzor: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným Sign-On URL, identifikátor a adresu URL odpovědi. Pokud chcete získat tyto hodnoty, kontaktujte [TopDesk – tým podpory pro zabezpečené klienty](https://www.topdesk.com/us/support/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení TopDesk-Secure** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-topdesk---secure-single-sign-on"></a>Konfigurace TOPdesk – zabezpečení jedné Sign-On

1. Přihlaste se k vašemu **TopDesk** firemnímu webu jako správce.

2. V nabídce **TopDesk** klikněte na **Nastavení**.

    ![Nastavení](./media/topdesk-secure-tutorial/ic790598.png "Nastavení")

3. Klikněte na tlačítko **Nastavení přihlášení**.

    ![Nastavení přihlášení](./media/topdesk-secure-tutorial/ic790599.png "Nastavení přihlášení")

4. Rozbalte nabídku **Nastavení přihlášení** a pak klikněte na **Obecné**.

    ![Obecné](./media/topdesk-secure-tutorial/ic790600.png "Obecné")

5. V části **zabezpečení** v části konfigurace **přihlášení SAML** proveďte následující kroky:

    ![Technické nastavení](./media/topdesk-secure-tutorial/ic790855.png "Technické nastavení")

    a. Klikněte na tlačítko **Stáhnout** a Stáhněte si soubor veřejné metadat a uložte ho místně na svém počítači.

    b. Otevřete soubor metadat a vyhledejte uzel **AssertionConsumerService** .

    ![Služba příjemce kontrolního výrazu](./media/topdesk-secure-tutorial/ic790856.png "Služba příjemce kontrolního výrazu")

    c. Zkopírujte hodnotu **AssertionConsumerService** a vložte tuto hodnotu do textového pole Adresa URL odpovědi v části **TopDesk – zabezpečení domény a adresy URL** .

6. Chcete-li vytvořit soubor certifikátu, proveďte následující kroky:

    ![Certifikát](./media/topdesk-secure-tutorial/ic790606.png "Certifikát")

    a. Otevřete stažený soubor metadat z Azure Portal.

    b. Rozbalte uzel **RoleDescriptor** , který má **atribut xsi: Type** dodaný **: ApplicationServiceType**.

    c. Zkopírujte hodnotu uzlu **certifikátu x509** .

    d. Uložte zkopírovanou hodnotu **certifikátu x509** lokálně na svém počítači do souboru.

7. V části **Public** klikněte na **Přidat**.

    ![Přidat](./media/topdesk-secure-tutorial/ic790607.png "Přidat")

8. Na stránce **Průvodce konfigurací SAML** proveďte následující kroky:

    ![Pomocník pro konfiguraci SAML](./media/topdesk-secure-tutorial/ic790608.png "Pomocník pro konfiguraci SAML")

    a. Pokud chcete nahrát stažený soubor metadat z Azure Portal, klikněte v části **federační metadata**na **Procházet**.

    b. Pokud chcete nahrát soubor certifikátu, klikněte v části **certifikát (RSA)** na **Procházet**.

    c. U **privátního klíče (RSA, PKCS8, der)** můžete nahrát svůj vlastní privátní klíč, nebo můžete kontaktovat [tým podpory TopDesk – zabezpečeného klienta](https://www.topdesk.com/us/support) , abyste získali privátní klíč.

    d. Pokud chcete nahrát soubor loga, který jste dostali z týmu podpory TOPdesk, klikněte v části **ikona loga**na **Procházet**.

    e. Do textového pole **atributu uživatelského jména** zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    f. Do textového pole **Zobrazovaný název** zadejte název konfigurace.

    například Klikněte na **Uložit**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k TOPdesk-Secure.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte **TopDesk-Secure**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **TopDesk-Secure**.

    ![Odkaz TOPdesk-Secure v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-topdesk---secure-test-user"></a>Vytvořit TOPdesk – zabezpečený testovací uživatel

Aby se uživatelé Azure AD mohli přihlašovat k TOPdesk-Secure, musí se zřídit v TOPdesk-Secure.  
V případě TOPdesk-Secure je zřizování ručním úkolem.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Při konfiguraci zřizování uživatelů proveďte následující kroky:

1. Přihlaste se k webu společnosti **TopDesk** jako správce.

2. V nabídce v horní části klikněte na **TopDesk \> New \> support Files \> Operator**.

    ![Podnikatel](./media/topdesk-secure-tutorial/ic790610.png "Operátor")

3. V dialogovém okně **Nový operátor** proveďte následující kroky:

    ![New – operátor](./media/topdesk-secure-tutorial/ic790611.png "Operátor new")

    a. Klikněte na kartu **Obecné**.

    b. Do textového pole **příjmení** zadejte příjmení uživatele, jako je **Simon**.

    c. V části **umístění** vyberte **lokalitu** pro účet.

    d. Do textového pole **přihlašovací jméno** v části **přihlášení TopDesk** zadejte přihlašovací jméno uživatele.

    e. Klikněte na **Uložit**.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jakýkoli jiný TOPdesk nástrojů pro vytváření uživatelských účtů nebo rozhraní API, které poskytuje TOPdesk – Secure.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici TOPdesk-Secure na přístupovém panelu byste se měli automaticky přihlášeni k TOPdesk – zabezpečení, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

