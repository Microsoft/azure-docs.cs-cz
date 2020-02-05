---
title: 'Kurz: Azure Active Directory integrace s Wdesk | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Wdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: a4cfcf20fc7a6a3532f65c3e797da6c876844d2c
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985505"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Wdesk

V tomto kurzu se dozvíte, jak integrovat Wdesk s Azure Active Directory (Azure AD). Když integrujete Wdesk s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Wdesk.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Wdesk svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Wdesk odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Wdesk podporuje **jednotné** přihlašování (SSO) a **IDP** .
* Po nakonfigurování Wdesk můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-wdesk-from-the-gallery"></a>Přidání Wdesk z Galerie

Pokud chcete nakonfigurovat integraci Wdesk do služby Azure AD, musíte přidat Wdesk z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Wdesk** .
1. Na panelu výsledků vyberte **Wdesk** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Wdesk na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Wdesk.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Wdesk, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte WDESK SSO](#configure-wdesk-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Wdesk Test User](#create-wdesk-test-user)** -to, abyste měli protějšek B. Simon v Wdesk, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Wdesk, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Wdesk** vyberte **jednotné přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Informace o jednotném přihlašování v doméně Wdesk a adresách URL](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Informace o jednotném přihlašování v doméně Wdesk a adresách URL](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Tyto hodnoty získáte z portálu WDesk při konfiguraci jednotného přihlašování.

4. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení Wdesk** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte brittasimon@yourcompanydomain.extension. Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Wdesk.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Wdesk**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Wdesk**.

    ![Odkaz Wdesk v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-wdesk-sso"></a>Konfigurace jednotného přihlašování Wdesk

1. V jiném okně webového prohlížeče se přihlaste k Wdesk jako správce zabezpečení.

2. V levém dolním rohu klikněte na **správce** a vyberte **správce účtu**:
 
     ![Konfigurace jednotného přihlašování](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. V Wdesk správce přejděte na **zabezpečení** **a potom na nastavení SAML > ** **SAML**:

    ![Konfigurace jednotného přihlašování](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

4. V části **Obecné nastavení**zaškrtněte **možnost Povolit jednotné přihlašování SAML**:

    ![Konfigurace jednotného přihlašování](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. V části **Podrobnosti o poskytovateli služeb**proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Zkopírujte **adresu URL pro přihlášení** a vložte ji do textového pole **Adresa URL pro přihlášení** na Azure Portal.
   
      b. Zkopírujte **adresu URL metadat** a vložte ji do textového pole **identifikátoru** v Azure Portal.
       
      c. Zkopírujte **adresu URL příjemce** a vložte ji do textového pole **adresa url odpovědi** na Azure Portal.
   
      d. Uložte změny kliknutím na **Uložit** na Azure Portal.      

6. Kliknutím na **Konfigurovat nastavení IDP** otevřete dialogové okno **Upravit nastavení IDP** . Klikněte na **zvolit soubor** a vyhledejte soubor **metadata. XML** , který jste uložili z Azure Portal a pak ho nahrajte.
    
    ![Konfigurace jednotného přihlašování](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
7. Klikněte na tlačítko **uložit změny**.

    ![Konfigurace jednotného přihlašování](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

### <a name="create-wdesk-test-user"></a>Vytvořit testovacího uživatele Wdesk

Aby se uživatelé Azure AD mohli přihlašovat k Wdesk, musí se zřídit v Wdesk. V Wdesk je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k Wdesk jako správce zabezpečení.

2. Přejděte na správce účtu **správce** **účtu** > .

     ![Konfigurace jednotného přihlašování](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Klikněte na **Členové** v části **lidé**.

4. Nyní klikněte na tlačítko **Přidat člena** a otevřete dialogové okno **Přidat člena** . 
   
    ![Vytváří se testovací uživatele služby Azure AD](./media/wdesk-tutorial/createuser1.png)  

5. Do textového pole **uživatel** zadejte uživatelské jméno, například brittasimon@contoso.com, a klikněte na tlačítko **pokračovat** .

    ![Vytváří se testovací uživatele služby Azure AD](./media/wdesk-tutorial/createuser3.png)

6.  Zadejte podrobnosti, jak je znázorněno níže:
  
    ![Vytváří se testovací uživatele služby Azure AD](./media/wdesk-tutorial/createuser4.png)
 
    a. Do textového pole **e-mailu** zadejte e-maily uživatele, jako je brittasimon@contoso.com.

    b. Do textového pole **jméno a příjmení** zadejte jméno uživatele jako například **Britta**.

    c. Do textového pole **příjmení** zadejte příjmení uživatele, jako je **Simon**.

7. Klikněte na tlačítko **Uložit člen** .  

    ![Vytváří se testovací uživatele služby Azure AD](./media/wdesk-tutorial/createuser5.png)

### <a name="test-sso"></a>Test SSO 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Wdesk, měli byste se automaticky přihlásit k Wdesk, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)