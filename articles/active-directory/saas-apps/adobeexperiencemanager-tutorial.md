---
title: 'Kurz: Azure Active Directory integrace se správcem aplikace Adobe Experience Manager | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a aplikací Adobe Experience Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 4607d4dfba68ca8f78ec4e3aaa4da36966ea843a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91760811"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Kurz: Azure Active Directory integrace se správcem prostředí Adobe Experience Manager

V tomto kurzu se dozvíte, jak integrovat správce platformy Adobe Experience pomocí Azure Active Directory (Azure AD).
Integrace programu Adobe Experience Manager se službou Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k programu Adobe Experience Manager.
* Uživatelům můžete povolit, aby se automaticky přihlásili k programu Adobe Experience Manager (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD pomocí programu Adobe Experience Manager budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné programu Adobe Experience Manager s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Adobe Experience Manager podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

* Správce platformy Adobe Experience podporuje zřizování uživatelů **jenom v čase** .

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Přidání programu Adobe Experience Manager z Galerie

Chcete-li nakonfigurovat integraci programu Adobe Experience Manager do služby Azure AD, je nutné přidat správce aplikace Adobe Experience Manager z Galerie do seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat správce aplikace Adobe Experience z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Adobe Experience Manager**, na panelu výsledků vyberte **Adobe Experience Manager** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Správce prostředí Adobe Experience v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí [název aplikace] na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být navázán vztah odkazu mezi uživatelem služby Azure AD a souvisejícím uživatelem v [název aplikace].

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí [název aplikace], musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurovat jednotné přihlašování pro Adobe Experience Manager](#configure-adobe-experience-manager-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele v programu Adobe Experience Manager](#create-adobe-experience-manager-test-user)** – abyste měli protějšek Britta Simon ve Správci Adobe Experience Manageru, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí [název aplikace], proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **správce prostředí Adobe Experience** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Snímek obrazovky zobrazující základní konfigurační oddíl SAML a zvýrazní textové pole identifikátoru a odpovědi URL](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte jedinečnou hodnotu, kterou definujete i na serveru AEM.

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > Hodnota adresy URL odpovědi není reálné číslo. Aktualizujte hodnotu adresy URL odpovědi skutečnou adresou URL odpovědi. Chcete-li získat tuto hodnotu, obraťte se na [tým podpory pro správce aplikace Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) , který tuto hodnotu získá. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Přihlašovací údaje domény a adresy URL jednotného přihlašování pro Adobe Experience Manager](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL serveru aplikace Adobe Experience Manager.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavení programu Adobe Experience Manager** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-adobe-experience-manager-single-sign-on"></a>Konfigurace nástroje Adobe Experience Manager Single Sign-On

1. V jiném okně prohlížeče otevřete portál pro správu **nástroje Adobe Experience Manager** .

2. Vyberte **Nastavení**  >  **zabezpečení**  >  **Uživatelé**.

    ![Snímek obrazovky, který zobrazuje dlaždici uživatelé ve správci programu Adobe Experience Manager.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

3. Vyberte **správce** nebo jakýkoli jiný příslušný uživatel.

    ![Snímek obrazovky, který zvýrazní uživatele Adminisrator.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

4. Vyberte **Nastavení účtu**  >  **Spravovat TrustStore**.

    ![Snímek obrazovky, který ukazuje správu TrustStore v části nastavení účtu.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

5. V části **Přidat certifikát ze souboru CER**klikněte na **Vybrat soubor certifikátu**. Vyhledejte a vyberte soubor certifikátu, který jste už stáhli z Azure Portal.

    ![Snímek obrazovky, který zvýrazní tlačítko Vybrat soubor certifikátu](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

6. Certifikát se přidá do TrustStore. Poznamenejte si alias certifikátu.

    ![Snímek obrazovky, který ukazuje, že se certifikát přidal do TrustStore.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

7. Na stránce **Uživatelé** vyberte **ověřování-služba**.

    ![Sreenshot, který zvýrazní ověřování – služba na obrazovce.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

8. Vyberte **Nastavení účtu**  >  **vytvořit/spravovat úložiště klíčů**. Vytvořte úložiště klíčů zadáním hesla.

    ![Snímek obrazovky, který zvýrazní správu úložiště klíčů.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

9. Vraťte se zpátky na obrazovku správce. Pak vyberte **Nastavení**  >  **Operations**  >  **Web Console**.

    ![Snímek obrazovky, který zvýrazní webovou konzolu v rámci operací v části nastavení.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Otevře se stránka konfigurace.

    ![Konfigurovat tlačítko pro uložení jednotného přihlašování](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

10. Najděte **obslužnou rutinu ověřování SAML 2,0 pro Adobe Granite**. Pak vyberte ikonu **Přidat** .

    ![Snímek obrazovky, který zvýrazňuje obslužnou rutinu ověřování SAML 2,0 pro Adobe Granite.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

11. Na této stránce proveďte následující akce.

    ![Tlačítko pro konfiguraci jednoho Sign-On Uložit](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. Do pole **cesta** zadejte **/** .

    b. Do pole **Adresa URL IDP** zadejte **adresu URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c. Do pole **alias certifikátu IDP** zadejte hodnotu **aliasu certifikátu** , kterou jste přidali v TrustStore.

    d. Do pole **ID entity poskytnuté zabezpečení** zadejte jedinečnou hodnotu **identifikátoru Azure AD** , kterou jste nakonfigurovali v Azure Portal.

    e. Do pole **Adresa URL služby pro příjemce kontrolního výrazu** zadejte hodnotu **adresy URL odpovědi** , kterou jste nakonfigurovali v Azure Portal.

    f. Do pole **heslo pro úložiště klíčů** zadejte **heslo** , které jste nastavili v úložišti klíčů.

    například V poli **ID atributu uživatele** zadejte **ID** nebo jiné ID uživatele, které je ve vašem případě relevantní.

    h. Vyberte možnost **autocreate CRX Users**.

    i. Do pole **Adresa URL pro odhlášení** zadejte jedinečnou **adresu URL pro odhlášení** , kterou jste získali z Azure Portal.

    j. Vyberte **Uložit**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k programu Adobe Experience Manager.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **správce aplikace Adobe Experience**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **správce prostředí Adobe Experience**.

    ![Odkaz na správce na stránce Adobe Experience Manager v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-adobe-experience-manager-test-user"></a>Vytvořit testovacího uživatele pro aplikaci Adobe Experience Manager

V této části vytvoříte uživatele s názvem Britta Simon ve správci aplikace Adobe Experience Manager. Pokud jste vybrali možnost automaticky **vytvořit uživatele CRX** , budou uživatelé po úspěšném ověření automaticky vytvořeni.

Pokud chcete ručně vytvořit uživatele, spolupracujte s [týmem podpory správce aplikace Adobe Experience](https://helpx.adobe.com/support/experience-manager.html)   a přidejte uživatele na platformě programu Adobe Experience Manager.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Správce prostředí Adobe Experience na přístupovém panelu byste se měli automaticky přihlášeni k programu Adobe Experience Manager, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
