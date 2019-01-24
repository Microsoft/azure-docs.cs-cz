---
title: 'Kurz: Integrace Azure Active Directory s 8 x 8 virtuálních Office | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a 8 x 8 virtuální Office.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.openlocfilehash: 11e316d2dc0811faaf3a9daab219386441fb3bb8
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54823636"
---
# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Kurz: Integrace Azure Active Directory s 8 x 8 virtuálních Office

V tomto kurzu se dozvíte, jak integrovat 8 x 8 virtuálních Office se službou Azure Active Directory (Azure AD).
Integrace 8 x 8 virtuální Office pomocí služby Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Office virtuální 8 x 8.
* Uživatelům se automaticky přihlášeni k 8 x 8 virtuálních Office (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s 8 x 8 virtuálních Office, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* 8 x 8 virtuální Office jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.


* podporuje virtuální Office 8 x 8 **IDP** jednotné přihlašování zahájené pomocí

* podporuje virtuální Office 8 x 8 **JIT** zřizování uživatelů

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Přidání virtuální Office 8 x 8 z Galerie

Pokud chcete nakonfigurovat integraci Office virtuální 8 x 8 do služby Azure AD, budete muset přidat Office virtuální 8 x 8 z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Office virtuální 8 x 8 z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **8 x 8 virtuálních Office**vyberte **8 x 8 virtuálních Office** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![8 x 8 virtuálních Office v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfiguraci a testování Azure AD jednotné přihlašování s 8 x 8 virtuální Office podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci vztah odkazu mezi uživatele služby Azure AD a související uživatelské v 8 x 8 virtuální Office je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s 8 x 8 virtuálních Office, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace 8 x 8 virtuální Office Single Sign-On](#configure-8x8-virtual-office-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele virtuální Office 8 x 8](#create-8x8-virtual-office-test-user)**  – Pokud chcete mít protějšek Britta Simon v Office virtuální 8 x 8, která souvisí s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s 8 x 8 virtuálních Office, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **8 x 8 virtuálních Office** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** dialogového okna, proveďte následující kroky:

    ![8 x 8 virtuální domény Office a adresy URL jednotné přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://sso.8x8.com/saml2`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://sso.8x8.com/saml2`

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Raw)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificateraw.png)

6. Na **nastavení 8 x 8 virtuálních Office** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-8x8-virtual-office-single-sign-on"></a>Konfigurace 8 x 8 virtuální Office jednotného přihlašování

7. Přihlášení k tenantovi virtuální Office 8 x 8 jako správce.

8. Vyberte **virtuální Office účet správce** na panelu aplikace.

    ![Konfigurace na straně aplikace](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

9. Vyberte **obchodní** účtu pro správu a klikněte na tlačítko **Sign In** tlačítko.

    ![Konfigurace na straně aplikace](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

10. Klikněte na tlačítko **účty** karty v seznamu v nabídce.

    ![Konfigurace na straně aplikace](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

11. Klikněte na tlačítko **Single Sign On** v seznamu účtů.
  
    ![Konfigurace na straně aplikace](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

12. Vyberte **Single Sign On** podle metody ověřování a klikněte na tlačítko **SAML**.

    ![Konfigurace na straně aplikace](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

13. V **jednotného přihlašování SAML** části, proveďte následující kroky:

    ![Konfigurace na straně aplikace](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. V **přihlašování v adrese URL** vložit do textového pole **přihlašovací adresa URL** hodnotu, která jste zkopírovali z portálu Azure portal.

    b. V **adresy URL odhlašovací** vložit do textového pole **odhlašovací adresa URL** hodnotu, která jste zkopírovali z portálu Azure portal.

    c. V **URL vystavitele** vložit do textového pole **Azure AD identifikátor** hodnotu, která jste zkopírovali z portálu Azure portal.

    d. Klikněte na tlačítko **Procházet** tlačítko Nahrát certifikát, který jste si stáhli z webu Azure portal.

    e. Klikněte na tlačítko **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Office virtuální 8 x 8.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **8 x 8 virtuálních Office**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **8 x 8 virtuálních Office**.

    ![Propojení virtuální Office 8 x 8 v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-8x8-virtual-office-test-user"></a>Vytvořit 8 x 8 virtuální Office testovacího uživatele

V této části se vytvoří uživateli Britta Simon v Office virtuální 8 x 8. podporuje virtuální Office 8 x 8 **zřizování uživatelů just-in-time**, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi 8 x 8 virtuálních Office, vytvoří se nový po ověření.

> [!NOTE]
> Pokud je potřeba ručně vytvořit uživatele, budete muset požádat [tým podpory virtuální Office 8 x 8](https://www.8x8.com/about-us/contact-us).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Office virtuální 8 x 8 na přístupovém panelu, vám by měl být automaticky přihlášeni Office virtuální 8 x 8, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

