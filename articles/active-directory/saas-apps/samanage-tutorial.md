---
title: 'Kurz: Integrace Azure Active Directory s Samanage | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Samanage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: 55a666d03d49d298622c2936a99ccd30326c03bf
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54816496"
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Kurz: Integrace Azure Active Directory s Samanage

V tomto kurzu se dozvíte, jak integrovat Samanage s Azure Active Directory (Azure AD).
Samanage integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Samanage.
* Můžete povolit uživatelům být automaticky přihlášeni k Samanage (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Samanage, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Samanage jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Samanage **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-samanage-from-the-gallery"></a>Přidání Samanage z Galerie

Konfigurace integrace Samanage do služby Azure AD, budete muset přidat Samanage z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Samanage z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Samanage**vyberte **Samanage** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Samanage v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Samanage podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Samanage.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Samanage, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Samanage Single Sign-On](#configure-samanage-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Samanage](#create-samanage-test-user)**  – Pokud chcete mít protějšek Britta Simon Samanage, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Samanage, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Samanage** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Samanage domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné přihlašovací adresu URL a identifikátor, který je vysvětlen později v tomto kurzu. Další podrobnosti získáte [tým podpory Samanage klienta](https://www.samanage.com/support). Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení Samanage** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-samanage-single-sign-on"></a>Konfigurace Samanage jednotné přihlašování

1. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti Samanage.

2. Klikněte na tlačítko **řídicí panel** a vyberte **nastavení** v levém navigačním podokně.
   
    ![Řídicí panel](./media/samanage-tutorial/tutorial_samanage_001.png "řídicího panelu")

3. Klikněte na tlačítko **jednotného přihlašování**.
   
    ![Jednotné přihlašování](./media/samanage-tutorial/tutorial_samanage_002.png "jednotného přihlašování")

4. Přejděte do **přihlášení pomocí SAML** části, proveďte následující kroky:
   
    ![Přihlášení pomocí SAML](./media/samanage-tutorial/tutorial_samanage_003.png "přihlášení pomocí SAML")
 
    a. Klikněte na tlačítko **povolit jednotné přihlašování pomocí SAML**.  
 
    b. V **adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **Azure Ad identifikátor** zkopírovanou z webu Azure portal.    
 
    c. Potvrďte **přihlašovací adresa URL** odpovídá **přihlašovací adresa URL** z **základní konfiguraci SAML** části webu Azure Portal.
 
    d. V **odhlašovací adresa URL** textového pole zadejte hodnotu **odhlašovací adresa URL** zkopírovanou z webu Azure portal.
 
    e. V **SAML vystavitele** textové pole, zadejte identifikátor URI id aplikace nastavení ve zprostředkovateli identity.
 
    f. Otevření certifikátu kódováním base-64 stáhnout z webu Azure portal v programu Poznámkový blok, zkopírujte obsah ho do schránky a a vložte ho do **vložte vašeho zprostředkovatele Identity x.509 Certificate níže** textového pole.
 
    g. Klikněte na tlačítko **vytvořit uživatele, pokud ještě neexistují v Samanage**.
 
    h. Klikněte na tlačítko **aktualizace**.

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

V této části je povolit Britta Simon k udělení přístupu k Samanage použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Samanage**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Samanage**.

    ![Odkaz Samanage v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-samanage-test-user"></a>Vytvoření Samanage testovacího uživatele

Přihlaste se k Samanage Azure AD uživatelům umožnit, musí být poskytnuty do Samanage.  
V případě Samanage zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlášení na webu společnosti Samanage jako správce.

2. Klikněte na tlačítko **řídicí panel** a vyberte **nastavení** v levém navigačním podokně.
   
    ![Instalační program](./media/samanage-tutorial/tutorial_samanage_001.png "instalační program")

3. Klikněte na tlačítko **uživatelé** kartu
   
    ![Uživatelé](./media/samanage-tutorial/tutorial_samanage_006.png "uživatelů")

4. Klikněte na tlačítko **nového uživatele**.
   
    ![Nový uživatel](./media/samanage-tutorial/tutorial_samanage_007.png "nového uživatele")

5. Typ **název** a **e-mailovou adresu** chcete zřídit a klikněte na účtu služby Azure Active Directory **vytvořit uživatele**.
   
    ![Vytvoření uživatele](./media/samanage-tutorial/tutorial_samanage_008.png "vytvoření uživatele")
   
   >[!NOTE]
   >Držitel účtu Azure Active Directory bude dostávat e-mailu a odkaz potvrďte svůj účet, pak se změní na aktivní. Můžete použít jakékoli jiné Samanage uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Samanage ke zřízení služby Azure Active Directory uživatelské účty.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Samanage na přístupovém panelu, můžete by měl být automaticky přihlášeni k Samanage, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

