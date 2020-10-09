---
title: 'Kurz: Azure Active Directory integrace s HighGear | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a HighGear.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.openlocfilehash: a48772c4325717a64bd36873675ff19c6a332de0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91817154"
---
# <a name="tutorial-azure-active-directory-integration-with-highgear"></a>Kurz: Azure Active Directory integrace s HighGear

V tomto kurzu se dozvíte, jak integrovat HighGear s Azure Active Directory (Azure AD).
Integrace HighGear s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k HighGear.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k HighGear (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s HighGear potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete si ho stáhnout [sem](https://azure.microsoft.com/pricing/free-trial/) v měsíční zkušební verzi.
* HighGear systém s licencí Enterprise nebo Unlimited

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu se dozvíte, jak nakonfigurovat a testovat jednotné přihlašování Azure AD v testovacím prostředí.

* HighGear podporuje jednotné přihlašování (SSO) **a IDP** .

## <a name="adding-highgear-from-the-gallery"></a>Přidání HighGear z Galerie

Pokud chcete nakonfigurovat integraci HighGear do služby Azure AD, musíte přidat HighGear z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat HighGear z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **HighGear**, vyberte **HighGear** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![HighGear v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části se dozvíte, jak nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí HighGear systému založeného na testovacím uživateli s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v systému HighGear.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí systému HighGear, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování HighGear](#configure-highgear-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace HighGear.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření HighGear testovacího uživatele](#create-highgear-test-user)** – pro Britta Simon v HighGear, který je propojený s zastoupením uživatele v Azure AD. 
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části se dozvíte, jak povolit jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí systému HighGear, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **HighGear** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On se** stránkou SAML kliknutím na ikonu **Upravit** otevřete základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Snímek obrazovky ukazuje základní konfiguraci SAML, kde můžete zadat identifikátor, odpovědět U R L a vybrat Uložit.](common/idp-intiated.png)

    a. Do textového pole **identifikátor** vložte hodnotu pole **ID entity poskytovatele služby** , která se nachází na stránce nastavení jednoho Sign-On v systému HighGear.

    ![Pole ID entity poskytovatele služby](media/highgear-tutorial/service-provider-entity-id-field.png)
    
    > [!NOTE]
    > K přístupu na stránku nastavení jedné Sign-On se budete muset přihlásit k systému HighGear. Až budete přihlášeni, přesuňte ukazatel myši na kartu Správa v HighGear a klikněte na položku nabídky nastavení jednoho Sign-On.
    
    ![Položka nabídky nastavení pro jednu Sign-On](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

    b. Do textového pole **Adresa URL odpovědi** vložte hodnotu **adresy URL služby assertion Consumer Service (ACS)** ze stránky nastavení jednoho Sign-On v systému HighGear.

    ![Pole adresy URL služby assertion Consumer Service (ACS)](media/highgear-tutorial/assertion-consumer-service-url-field.png)

    c. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

     ![Snímek obrazovky s nastavením další U R ls, kde můžete zadat přihlášení U R L.](common/metadata-upload-additional-signon.png)

     Do textového pole **Adresa URL pro přihlášení** vložte hodnotu pole **ID entity poskytovatele služby** , která se nachází na stránce nastavení jednoho Sign-On v systému HighGear. (Toto ID entity je také základní adresou URL systému HighGear, který se má použít pro přihlášení iniciované v rámci služby SP.)

    ![Pole ID entity poskytovatele služby](media/highgear-tutorial/service-provider-entity-id-field.png)

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL ze stránky **nastavení s jedním Sign-On** v systému HighGear. Pokud potřebujete pomoc, obraťte se prosím na [tým podpory HighGear](mailto:support@highgear.com).

4. Na stránce **nastavit jednu Sign-On s SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a stáhněte **certifikát (Base64)** a uložte ho do svého počítače. Budete ho potřebovat v pozdějším kroku konfigurace s jednou Sign-On.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení HighGear** si poznamenejte umístění následujících adres URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL Tuto hodnotu budete potřebovat v kroku #2 v části **Konfigurace jednotného přihlašování HighGear** .

    b. Identifikátor Azure AD. Tuto hodnotu budete potřebovat v kroku #3 v části **Konfigurace jednotného přihlašování HighGear** .

    c. Odhlašovací adresa URL Tuto hodnotu budete potřebovat v kroku #4 v části **Konfigurace jednotného přihlašování HighGear** .

### <a name="configure-highgear-single-sign-on"></a>Nakonfigurovat HighGear jednu Sign-On

Pokud chcete nakonfigurovat HighGear pro jednotné přihlašování, přihlaste se prosím k systému HighGear. Až budete přihlášeni, přesuňte ukazatel myši na kartu Správa v HighGear a klikněte na položku nabídky nastavení jednoho Sign-On.

![Položka nabídky nastavení pro jednu Sign-On](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

1. Do pole **název zprostředkovatele identity**zadejte krátký popis, který se zobrazí v HighGearm tlačítku pro jedno Sign-On na přihlašovací stránce. Příklad: Azure AD

2. Do pole **Adresa URL jednotného Sign-On (SSO)** v HighGear vložte hodnotu z pole **Adresa URL pro přihlášení** , které se nachází v části **Nastavení HighGear** v Azure.

3. Do pole **ID entity poskytovatele identity** v HighGear vložte hodnotu z pole **identifikátor Azure AD** , které je uvedené v části **Nastavení HighGear** v Azure.

4. Do pole **Adresa URL jednotného odhlašování (SLO)** v HighGear vložte hodnotu z pole **Adresa URL pro odhlášení** , které je uvedené v části **Nastavení HighGear** v Azure.

5. Pomocí poznámkového bloku otevřete certifikát, který jste stáhli z části **podpisový certifikát SAML** v Azure. Měli byste stáhnout formát **certifikátu (Base64)** . Zkopírujte obsah certifikátu z poznámkového bloku a vložte ho do pole **certifikát poskytovatele identity** v HighGear.

6. Odešlete [týmu podpory HighGear](mailto:support@highgear.com) e-mail pro vyžádání certifikátu HighGear. Podle pokynů, které z nich obdržíte, vyplňte pole **HighGear certifikát** a **heslo certifikátu HighGear** .

7. Kliknutím na tlačítko **Uložit** uložte konfiguraci HighGear s jedním Sign-On.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k HighGear.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **HighGear**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **HighGear**.

    ![Odkaz HighGear v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-highgear-test-user"></a>Vytvořit testovacího uživatele HighGear

Pokud chcete vytvořit HighGear testovacího uživatele pro otestování konfigurace pro jednu Sign-On, přihlaste se prosím k systému HighGear.

1. Klikněte na tlačítko **vytvořit nový kontakt** .

    ![Tlačítko vytvořit nový kontakt](media/highgear-tutorial/create-new-contact-button.png)

    Zobrazí se nabídka, která umožňuje zvolit druh kontaktu, který chcete vytvořit.

2. Kliknutím na položku **jednotlivé** nabídky vytvoříte uživatele HighGear.

    Podokno bude na pravé straně, takže můžete zadat informace pro nového uživatele.  
    ![Formulář nového kontaktu](media/highgear-tutorial/new-contact-form.png)

3. Do pole **název** zadejte název kontaktu. Příklad: Britta Simon

4. Klikněte na nabídku **Další možnosti** a vyberte položku nabídky **informace o účtu** .

    ![Kliknutí na položku nabídky informace o účtu](media/highgear-tutorial/account-info-menu-item.png)

5. Nastavte pole **může přihlásit** na Ano.

    Pole **Povolit jednotné přihlašování** bude automaticky nastaveno na Ano.

6. Do pole **ID uživatele s jedním Sign-On** zadejte ID uživatele. Příklad: BrittaSimon@contoso.com

    Oddíl informace o účtu by teď měl vypadat nějak takto:  
    ![Část s informacemi o dokončeném účtu](media/highgear-tutorial/finished-account-info-section.png)

7. Kontakt uložíte kliknutím na tlačítko **Uložit** v dolní části podokna.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici HighGear, měli byste se automaticky přihlásit k HighGear, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

