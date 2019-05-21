---
title: 'Kurz: Integrace Azure Active Directory s SAP Business ByDesign | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP Business ByDesign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23669671c9aec2ebad8e03e06a0ea1b139214cad
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65902842"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Kurz: Integrace Azure Active Directory s SAP Business ByDesign

V tomto kurzu se dozvíte, jak integrovat SAP Business ByDesign s Azure Active Directory (Azure AD).
SAP Business ByDesign integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k SAP Business ByDesign.
* Uživatelům se automaticky přihlášeni k SAP Business ByDesign (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SAP Business ByDesign, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* SAP Business ByDesign jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* SAP Business ByDesign podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Přidání SAP Business ByDesign z Galerie

Konfigurace integrace SAP Business ByDesign do služby Azure AD, budete muset přidat SAP Business ByDesign z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat SAP Business ByDesign z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SAP Business ByDesign**vyberte **SAP Business ByDesign** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![SAP Business ByDesign v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování pomocí SAP Business ByDesign podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v SAP Business ByDesign.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se SAP Business ByDesign, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace SAP Business ByDesign Single Sign-On](#configure-sap-business-bydesign-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele SAP Business ByDesign](#create-sap-business-bydesign-test-user)**  – Pokud chcete mít protějšek Britta Simon v SAP Business ByDesign, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotného přihlašování se SAP Business ByDesign, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **SAP Business ByDesign** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![SAP Business ByDesign domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<servername>.sapbydesign.com`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory SAP Business ByDesign klienta](https://www.sap.com/products/cloud-analytics.support.html) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Aplikace SAP Business ByDesign očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

6. Klikněte na **upravit** ikona pro úpravy **název hodnota identifikátoru**.

    ![image](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. Na **spravovat deklarace identity uživatelů** části, proveďte následující kroky: ![bitové kopie](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Vyberte **transformace** jako **zdroj**.

    b. V **transformace** rozevíracího seznamu vyberte **ExtractMailPrefix()**.

    c. V **parametr 1** rozevíracího seznamu vyberte atribut uživatele, které chcete použít pro implementaci. Například pokud chcete použít jako jedinečný identifikátor uživatele EmployeeID a hodnota atributu jsou uložené v ExtensionAttribute2, vyberte user.extensionattribute2.

    d. Klikněte na **Uložit**.

8. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

9. Na **nastavit SAP Business ByDesign** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-sap-business-bydesign-single-sign-on"></a>Konfigurace SAP Business ByDesign jednotné přihlašování

1. Přihlaste se k portálu SAP Business ByDesign s oprávněním správce.

2. Přejděte do **aplikace a běžné úlohy správy uživatele** a klikněte na tlačítko **zprostředkovatele Identity** kartu.

3. Klikněte na tlačítko **nového zprostředkovatele Identity** a vyberte soubor XML metadat, který jste si stáhli z portálu Azure portal. Importováním metadata systému automaticky nahrává dodejka certifikát a certifikát pro šifrování.

    ![Konfigurace jednotného přihlašování](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Zahrnout **adresa URL služby Assertion příjemce** do požadavku SAML, vyberte **zahrnují URL služby příjemce kontrolního výrazu**.

5. Klikněte na tlačítko **aktivovat jednotné přihlašování**.

6. Uložte provedené změny.

7. Klikněte na tlačítko **systém** kartu.

    ![Konfigurace jednotného přihlašování](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. V **přihlášení Azure AD na adrese URL** vložit do textového pole **přihlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    ![Konfigurace jednotného přihlašování](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Určete, zda zaměstnance můžete ručně vybrat mezi variantami přihlášení pomocí ID uživatele a heslo nebo jednotného přihlašování tak, že vyberete **výběru zprostředkovatele Identity ruční**.

10. V **adresu URL jednotného přihlašování** části, zadejte adresu URL, který se má použít pro zaměstnance na přihlášení k systému.
    V adresu URL odeslané na zaměstnance rozevírací seznam můžete vybrat mezi následujícími možnostmi:

    **Adresa URL – jednotné přihlašování**

    Systém odešle jenom adresu URL normální systému zaměstnance. Zaměstnanec nelze přihlášení pomocí jednotného přihlašování a musí používat heslo nebo místo certifikátu.

    **ADRESA URL PRO JEDNOTNÉ PŘIHLAŠOVÁNÍ**

    Systém odešle pouze adresu URL jednotného přihlašování pro zaměstnance. Zaměstnanci můžou přihlášení pomocí jednotného přihlašování. Požadavek na ověření je přesměrován prostřednictvím zprostředkovatele identity.

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
  
    b. V **uživatelské jméno** typ pole `brittasimon@yourcompanydomain.extension`. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k SAP Business ByDesign.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **SAP Business ByDesign**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **SAP Business ByDesign**.

    ![V seznamu aplikací na odkaz SAP Business ByDesign](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-sap-business-bydesign-test-user"></a>Vytvořit testovacího uživatele SAP Business ByDesign

V této části vytvoříte uživatele v SAP Business ByDesign jako Britta Simon. Spojte se prosím s [tým podpory SAP Business ByDesign klienta](https://www.sap.com/products/cloud-analytics.support.html) přidat uživatele na platformě SAP Business ByDesign. 

> [!NOTE]
> Ujistěte se prosím, že NameID hodnota by měla odpovídat s polem uživatelské jméno na platformě SAP Business ByDesign.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici SAP Business ByDesign na přístupovém panelu, vám by měl být automaticky přihlášeni k SAP Business ByDesign, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
