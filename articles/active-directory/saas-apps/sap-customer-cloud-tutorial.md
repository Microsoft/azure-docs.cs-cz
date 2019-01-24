---
title: 'Kurz: Integrace Azure Active Directory se SAP cloudem pro zákazníka | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP cloudem pro zákazníka.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: c25196f03d9c4e70a43b9ea8c3a24ed6c0e39ead
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54816003"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>Kurz: Integrace Azure Active Directory se SAP cloudem pro odběratele

V tomto kurzu se dozvíte, jak integrovat SAP cloudem pro zákazníky se službou Azure Active Directory (Azure AD).
Integrace SAP cloudem pro zákazníky s využitím Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k SAP cloudem pro zákazníka.
* Můžete povolit uživatelům být automaticky přihlášeni k SAP Cloud zákazníka (Single Sign-On) pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD se SAP cloudem pro zákazníka, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* SAP cloudem pro zákazníka jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* SAP cloudem pro zákazníka podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Přidání SAP cloudem pro zákazníka z Galerie

Ke konfiguraci integrace SAP cloudem pro zákazníka do služby Azure AD, budete muset přidat SAP cloudem pro zákazníka v galerii na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat SAP cloudem pro zákazníka z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SAP cloudem pro zákazníka**vyberte **SAP cloudem pro zákazníka** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![SAP cloudem pro zákazníka v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a testovací služby Azure AD jednotného přihlašování se SAP cloudem pro zákazníky na základě testu uživatelem volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v SAP cloudem pro zákazníka.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se SAP cloudem pro zákazníka, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace cloudu SAP pro zákazníka Single Sign-On](#configure-sap-cloud-for-customer-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření SAP cloudem pro zákazníka testovacího uživatele](#create-sap-cloud-for-customer-test-user)**  – Pokud chcete mít protějšek Britta Simon ve SAP Cloud for zákazníka, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotného přihlašování se SAP cloudem pro zákazníky, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **SAP cloudem pro zákazníka** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![SAP cloudem informace zákazníků domény a adresy URL jednotné přihlašování](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<server name>.crm.ondemand.com`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [SAP cloudem pro tým podpory zákazníků klienta](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. SAP cloudem pro zákaznické aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

6. V **atributy uživatele** části na **atributy uživatele a deklarace identity** dialogového okna, proveďte následující kroky:

    a. Klikněte na tlačítko **ikonu pro úpravu** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Vyberte **transformace** jako **zdroj**.

    c. Z **transformace** seznamu vyberte **ExtractMailPrefix()**.

    d. Z **parametr 1** vyberte atribut uživatele, které chcete použít pro implementaci.
    Například pokud chcete použít jako jedinečný identifikátor uživatele EmployeeID a hodnota atributu jsou uložené v ExtensionAttribute2, vyberte user.extensionattribute2.

    e. Klikněte na **Uložit**.

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

8. Na **nastavit SAP cloudem pro zákazníka** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-sap-cloud-for-customer-single-sign-on"></a>Konfigurace SAP cloudem pro zákazníka jednotného přihlašování
   
1. Přihlaste se k SAP cloudem pro zákaznického portálu služeb s oprávněním správce.
   
2. Přejděte **aplikace a běžné úlohy správy uživatele** a klikněte na tlačítko **zprostředkovatele Identity** kartu.
   
3. Klikněte na tlačítko **nového zprostředkovatele Identity** a vyberte soubor XML metadat, který jste si stáhli z portálu Azure portal. Importováním metadata systému automaticky nahrává dodejka certifikát a certifikát pro šifrování.
   
    ![Konfigurace jednotného přihlašování](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_54.png)
   
4. Azure Active Directory vyžaduje element adresa URL služby příjemce kontrolního výrazu v požadavku SAML, proto vyberte **zahrnují URL služby příjemce kontrolního výrazu** zaškrtávací políčko.
   
5. Klikněte na tlačítko **aktivovat jednotné přihlašování**.
   
6. Uložte provedené změny.
   
7. Klikněte na tlačítko **systém** kartu.
   
    ![Konfigurace jednotného přihlašování](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_52.png)
   
8. V **přihlášení Azure AD na adrese URL** vložit do textového pole **přihlašovací adresa URL** zkopírovanou z webu Azure portal.
   
    ![Konfigurace jednotného přihlašování](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_53.png)
   
9. Určete, zda zaměstnance můžete ručně vybrat mezi variantami přihlášení pomocí ID uživatele a heslo nebo jednotného přihlašování tak, že vyberete **výběru zprostředkovatele Identity ruční**.
   
10. V **adresu URL jednotného přihlašování** části, zadejte adresu URL, který se má použít vaši zaměstnanci k přihlášení k systému. 
    V **URL odesílat zaměstnance** seznamu, můžete si vybrat následující možnosti:
   
    **Adresa URL – jednotné přihlašování**
   
    Systém odešle jenom adresu URL normální systému zaměstnance. Zaměstnanec nelze přihlášení pomocí jednotného přihlašování a musí používat heslo nebo místo certifikátu.
   
    **ADRESA URL PRO JEDNOTNÉ PŘIHLAŠOVÁNÍ** 
   
    Systém odešle pouze adresu URL jednotného přihlašování pro zaměstnance. Zaměstnanec se může přihlásit pomocí jednotného přihlašování. Požadavek na ověření je přesměrován prostřednictvím zprostředkovatele identity.
   
    **Automatický výběr**
   
    Pokud jednotného přihlašování není aktivní, systém odešle URL normální systému zaměstnanci. Pokud se jednotné přihlašování je aktivní, systém zkontroluje, zda zaměstnance heslo. Pokud je k dispozici heslo, adresu URL jednotného přihlašování a adresy URL bez jednotného přihlašování se odesílají zaměstnanci. Ale pokud zaměstnanec nemá žádné heslo, pouze adresu URL jednotného přihlašování se odešle zaměstnanci.
   
11. Uložte provedené změny.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k SAP cloudem pro zákazníka.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **SAP cloudem pro zákazníka**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **SAP cloudem pro zákazníka**.

    ![SAP cloudem pro spojení zákazníka v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-sap-cloud-for-customer-test-user"></a>Vytvoření SAP cloudem pro zákazníka testovacího uživatele

V této části vytvoříte uživateli Britta Simon v SAP cloudem pro zákazníka. Práce s [SAP cloudem pro tým podpory](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) přidejte uživatele, v cloudu SAP pro platformu zákazníka. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

> [!NOTE]
> Ujistěte se prosím, že NameID hodnota by měla odpovídat s pole uživatelské jméno v SAP cloudem pro platformu zákazníka.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na SAP cloudem pro zákazníka dlaždici na přístupovém panelu, můžete by měl být automaticky přihlášeni SAP cloud pro zákazníka, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

