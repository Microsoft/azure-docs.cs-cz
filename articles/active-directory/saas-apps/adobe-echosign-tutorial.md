---
title: 'Kurz: Azure Active Directory integrace s podpisem Adobe | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a symbolem Adobe.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: 18a034c6af9659e113a821e072098aee873c2ee2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91760797"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Kurz: Azure Active Directory integrací pomocí Adobe Sign

V tomto kurzu se dozvíte, jak integrovat znaménko Adobe pomocí Azure Active Directory (Azure AD).
Integrace společnosti Adobe Sign with Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k Adobe Sign.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k programu Adobe Signing (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD pomocí podepisování Adobe budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Přihlášení k odběru s povoleným jednotným přihlašováním společnosti Adobe

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Adobe **Sign podporuje jednotné** přihlašování.

## <a name="adding-adobe-sign-from-the-gallery"></a>Přidání znaménka Adobe z Galerie

Pokud chcete nakonfigurovat integraci Adobe přihlašování do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat znaménko Adobe z galerie.

**Chcete-li přidat znaménko Adobe z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte text **Adobe Sign**, na panelu výsledků vyberte **Adobe Sign** (podepsat) a pak klikněte na **Přidat** tlačítko a přidejte aplikaci.

     ![Symbol Adobe v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí programu Adobe Signing na základě testovacího uživatele s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v symbolech Adobe.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí přihlášení Adobe, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurovat Adobe Sign Single Sign-on](#configure-adobe-sign-single-sign-on)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořit Adobe Sign Test User](#create-adobe-sign-test-user)** – abyste měli protějšek Britta Simon v symbolech Adobe, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí přihlášení Adobe, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **Adobe Sign** aplikace Integration vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně a adresách URL společnosti Adobe](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.echosign.com/`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.echosign.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory společnosti Adobe Signer support](https://helpx.adobe.com/in/contact/support.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavit znaménko Adobe** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-adobe-sign-single-sign-on"></a>Nakonfigurovat Adobe Sign Single Sign-On

1. Před konfigurací se obraťte na [tým podpory společnosti Adobe Signer support](https://helpx.adobe.com/in/contact/support.html) , který přidá vaši doménu do seznamu povolených podpisů Adobe. Tady je postup, jak přidat doménu:

    a. [Tým podpory podepisování klientů společnosti Adobe](https://helpx.adobe.com/in/contact/support.html) vám pošle náhodně vygenerovaný token. Pro vaši doménu bude token vypadat takto: **Adobe-Sign-ověřování = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Publikujte token ověřování v textovém záznamu DNS a upozorněte [tým podpory klienta podpory společnosti Adobe](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Tato možnost může trvat několik dní nebo déle. Všimněte si, že zpoždění šíření DNS znamenají, že hodnota publikovaná v DNS nemusí být viditelná za hodinu nebo déle. Váš správce IT by měl být znalostí o tom, jak tento token publikovat v textovém záznamu DNS.
    
    c. Když [klientovi podpory společnosti Adobe podepíše](https://helpx.adobe.com/in/contact/support.html) prostřednictvím lístku podpory, po publikování tokenu ověří doménu a přidá ji do svého účtu.
    
    d. Obecně platí, že tady je postup publikování tokenu na záznamu DNS:

    * Přihlaste se ke svému účtu domény.
    * Vyhledejte stránku pro aktualizaci záznamu DNS. Tato stránka se může jmenovat správa DNS, Správa názvového serveru nebo rozšířená nastavení.
    * Vyhledejte záznamy TXT pro vaši doménu.
    * Přidejte záznam TXT s hodnotou úplného tokenu poskytnutou společností Adobe.
    * Uložte provedené změny.

1. V jiném okně webového prohlížeče se přihlaste k webu společnosti Adobe Signing Company jako správce.

1. V nabídce SAML vyberte **Nastavení účtu**  >  **Nastavení SAML**.
   
    ![Snímek obrazovky se stránkou nastavení pro Adobe – podepsání SAML](./media/adobe-echosign-tutorial/ic789520.png "Účet")

1. V části **Nastavení SAML** proveďte následující kroky:
  
   ![Snímek obrazovky, který zvýrazní nastavení SAML, včetně povinného SAML.](./media/adobe-echosign-tutorial/ic789521.png "Nastavení SAML")
   
   ![Snímek obrazovky s nastavením SAML](./media/adobe-echosign-tutorial/ic789522.png "Nastavení SAML")

   a. V části **režim SAML**vyberte **SAML povinný**.
   
   b. Vyberte možnost **dovolit správcům účtu EchoSign se přihlaste pomocí svých přihlašovacích údajů EchoSign**.
   
   c. V části **Vytvoření uživatele**vyberte možnost **automaticky přidat uživatele ověřené prostřednictvím SAML**.

   d. Do textového pole **ID entity IDP** vložte **identifikátor Azure AD**, který jste zkopírovali z Azure Portal.
    
   e. Vložte **adresu URL pro přihlášení**, kterou jste zkopírovali z Azure Portal do textového pole **přihlašovací adresa URL pro IDP** .
   
   f. Vložte **adresu URL pro odhlášení**, kterou jste zkopírovali z Azure Portal do textového pole **Adresa URL pro odhlášení IDP** .

   například Otevřete stažený soubor **certifikátu (Base64)** v programu Poznámkový blok. Zkopírujte obsah této složky do schránky a vložte ji do textového pole **certifikát IDP** .

   h. Vyberte **Uložit změny**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Adobe Sign.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Adobe Sign (podepsat**).

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **symbol Adobe**.

    ![Odkaz na symbol Adobe v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-adobe-sign-test-user"></a>Vytvořit Adobe Sign Test User

Aby se uživatelé Azure AD mohli přihlásit k přihlašování do Adobe, musí se zřídit v symbolech Adobe. Toto je ruční úloha.

>[!NOTE]
>K zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů nebo rozhraní API pro přihlášení společnosti Adobe, které poskytuje symbol Adobe Signing. 

1. Přihlaste se k webu společnosti **Adobe Sign** společnosti jako správce.

2. V nabídce v horní části vyberte **účet**. Pak v levém podokně vyberte **Uživatelé & skupiny**  >  **vytvořit nového uživatele**.
   
    ![Snímek obrazovky společnosti Adobe podepsat firemní web, s účtem, uživateli &skupin a vytvořit nově zvýrazněného uživatele](./media/adobe-echosign-tutorial/ic789524.png "Účet")
   
3. V části **vytvořit nového uživatele** proveďte následující kroky:
   
    ![Snímek obrazovky s oddílem pro vytvoření nového uživatele](./media/adobe-echosign-tutorial/ic789525.png "Vytvořit uživatele")
   
    a. Zadejte **e-mailovou adresu**, **jméno**a **příjmení** platného účtu Azure AD, který chcete zřídit, do příslušných textových polí.
   
    b. Vyberte **vytvořit uživatele**.

>[!NOTE]
>Držitel účtu Azure Active Directory obdrží e-mail, který obsahuje odkaz na potvrzení účtu, než bude aktivní. 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici symbol Adobe na přístupovém panelu, měli byste se automaticky přihlásit k přihlašovacímu znaku Adobe, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

