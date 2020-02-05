---
title: 'Kurz: Azure Active Directory integrace s Egnyte | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Egnyte.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f8eb0f0d566d656436da11141ea7f6c521b7b82
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983665"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Egnyte

V tomto kurzu se dozvíte, jak integrovat Egnyte s Azure Active Directory (Azure AD). Když integrujete Egnyte s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Egnyte.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Egnyte svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Egnyte odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Egnyte podporuje jednotné přihlašování iniciované v **SP**
* Po nakonfigurování Egnyte můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-egnyte-from-the-gallery"></a>Přidání Egnyte z Galerie

Pokud chcete nakonfigurovat integraci Egnyte do služby Azure AD, musíte přidat Egnyte z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Egnyte** .
1. Na panelu výsledků vyberte **Egnyte** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Egnyte na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Egnyte.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Egnyte, musíte dokončit tyto stavební bloky:

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Egnyte, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte EGNYTE SSO](#configure-egnyte-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Egnyte Test User](#create-egnyte-test-user)** -to, abyste měli protějšek B. Simon v Egnyte, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Egnyte, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Egnyte** vyberte **jednotné přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně Egnyte a adresách URL](common/sp-signonurl.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.egnyte.com`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta Egnyte](https://www.egnyte.com/corp/contact_egnyte.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

5. V části **Nastavení Egnyte** zkopírujte příslušné adresy URL podle vašich požadavků.

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
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon\@yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Egnyte.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Egnyte**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Egnyte**.

    ![Odkaz Egnyte v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-egnyte-sso"></a>Konfigurace jednotného přihlašování Egnyte

1. V jiném okně webového prohlížeče se přihlaste k webu Egnyte společnosti jako správce.

2. Klikněte na **Nastavení**.
   
    ![Nastavení](./media/egnyte-tutorial/ic787819.png "Nastavení")

3. V nabídce klikněte na **Nastavení**.

    ![Nastavení](./media/egnyte-tutorial/ic787820.png "Nastavení")

4. Klikněte na kartu **Konfigurace** a pak klikněte na **zabezpečení**.

    ![Zabezpečení](./media/egnyte-tutorial/ic787821.png "Zabezpečení")

5. V části **ověřování jednotného přihlašování** proveďte následující kroky:

    ![Ověřování jednotného přihlašování](./media/egnyte-tutorial/ic787822.png "Ověřování jednotného přihlašování")   
    
    a. Jako **ověřování pomocí jednotného přihlašování**vyberte **SAML 2,0**.
   
    b. Jako **zprostředkovatel identity**vyberte **AzureAD**.
   
    c. Vložte **přihlašovací adresu URL** zkopírovanou z Azure Portal do textového pole **Adresa URL pro přihlášení zprostředkovatele identity** .
   
    d. Do textového pole **ID entity zprostředkovatele identity** vložte **identifikátor Azure AD** , který jste zkopírovali z Azure Portal.
      
    e. Otevřete v programu Poznámkový blok certifikát s kódováním Base-64 stažený z Azure Portal, zkopírujte jeho obsah do schránky a vložte ho do textového pole **certifikát poskytovatele identity** .
   
    f. Jako **výchozí mapování uživatelů**vyberte **e-mailová adresa**.
   
    g. Jako **hodnotu vystavitele v doméně použijte**možnost **zakázáno**.
   
    h. Klikněte na **Uložit**.

### <a name="create-egnyte-test-user"></a>Vytvořit testovacího uživatele Egnyte

Aby se uživatelé Azure AD mohli přihlašovat k Egnyte, musí se zřídit v Egnyte. V případě Egnyte je zřizování ručním úkolem.

**Při zřizování uživatelských účtů proveďte následující kroky:**

1. Přihlaste se k webu **Egnyte** společnosti jako správce.

2. Přejít na **nastavení \> uživatelé & skupiny**.

3. Klikněte na tlačítko **Přidat nového uživatele**a potom vyberte typ uživatele, který chcete přidat.
   
    ![Uživatelé](./media/egnyte-tutorial/ic787824.png "Uživatelé")

4. V části **Nová skupina Power** Users proveďte následující kroky:
    
    ![Nový standardní uživatel](./media/egnyte-tutorial/ic787825.png "Nový standardní uživatel")   

    a. Do textového pole **e-mail** zadejte e-maily uživatele, jako je **Brittasimon\@contoso.com**.

    b. Do textového pole **uživatelské jméno** zadejte uživatelské jméno uživatele, jako je **Brittasimon**.

    c. Jako **typ ověřování**vyberte **jednotné přihlašování** .
   
    d. Klikněte na **Uložit**.
    
    >[!NOTE]
    >Držitel účtu Azure Active Directory dostane e-mail s oznámením.
    >

>[!NOTE]
>K zřizování uživatelských účtů Azure AD můžete použít jiné nástroje pro vytváření uživatelských účtů Egnyte nebo rozhraní API poskytovaná Egnyte.
>

### <a name="test-sso"></a>Test SSO

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Egnyte, měli byste se automaticky přihlásit k Egnyte, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
