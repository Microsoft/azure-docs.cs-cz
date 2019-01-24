---
title: 'Kurz: Integrace se SAP Business objekt cloudem Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP Business objekt cloudu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: ae2a28dea5fe240bbf216e8ec92f29c186c92255
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54825336"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Kurz: Integrace se SAP Business objekt cloudem Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat SAP Business objekt cloudu s Azure Active Directory (Azure AD).
SAP Business objekt cloudové integrace s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k SAP Business objekt cloudu.
* Uživatelům se automaticky přihlášeni k SAP Business objekt cloudu (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD se SAP Business objekt cloudem, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* SAP Business objekt Cloud jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* SAP Business objekt Cloud podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-sap-business-object-cloud-from-the-gallery"></a>Přidání SAP Business objekt Cloud z Galerie

Pokud chcete nakonfigurovat integraci SAP Business objektu cloudu do služby Azure AD, potřebujete přidat SAP Business objekt Cloud z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat SAP Business objekt Cloud z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SAP Business objekt cloudu**vyberte **SAP Business objekt cloudu** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![SAP Business objekt cloudu v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování pomocí SAP Business objekt cloudu založená na uživateli testu **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatel v SAP Business objekt cloudu.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se SAP cloudem objekt firmy, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace SAP Business objekt cloudu jednotného přihlašování](#configure-sap-business-object-cloud-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele SAP Business objekt cloudu](#create-sap-business-object-cloud-test-user)**  – Pokud chcete mít protějšek Britta Simon v SAP Business objekt cloudu, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotného přihlašování se SAP Business objekt cloudem, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **SAP Business objekt cloudu** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![SAP Business objekt Cloudová doména a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce:
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > Hodnoty v těchto adres URL jsou pouze ukázku. Aktualizujte hodnoty skutečné přihlašovací adresu URL a identifikátoru adresy URL. Získat adresu URL přihlašování, obraťte se [tým podpory SAP Business objekt cloudu klienta](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Identifikátoru adresy URL můžete získat stažením metadata SAP Business objekt cloudu z konzoly pro správu. To je vysvětleno dále v tomto kurzu.

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

### <a name="configure-sap-business-object-cloud-single-sign-on"></a>Konfigurace SAP Business objekt cloudu jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k serveru SAP Business objekt cloudu společnosti jako správce.

2. Vyberte **nabídky** > **systému** > **správu**.
    
    ![Vyberte nabídky, pak systém a Správa](./media/sapboc-tutorial/config1.png)

3. Na **zabezpečení** kartu, vyberte **upravit** ikony (pero).
    
    ![Na kartě zabezpečení vyberte ikonu pro úpravy](./media/sapboc-tutorial/config2.png)  

4. Pro **metodu ověřování**vyberte **SAML jednotné přihlašování (SSO)**.

    ![Vyberte metodu ověřování SAML jednotného přihlašování](./media/sapboc-tutorial/config3.png)  

5. Chcete-li stáhnout metadata poskytovatele služby (krok 1), vyberte **Stáhnout**. V souboru metadat, vyhledejte a zkopírujte **entityID** hodnotu. Na webu Azure Portal na **základní konfiguraci SAML** dialogového okna, vložte tuto hodnotu v **identifikátor** pole.

    ![Zkopírujte a vložte tuto hodnotu entityID](./media/sapboc-tutorial/config4.png)  

6. Nahrát zprostředkovatele metadat služby (krok 2) v souboru, který jste si stáhli z webu Azure portal v části **metadat zprostředkovatele Identity nahrát**vyberte **nahrát**.  

    ![V části metadata uložit zprostředkovatele Identity výběr nahrání](./media/sapboc-tutorial/config5.png)

7. V **atribut uživatele** vyberte atribut uživatele (krok 3), který chcete použít pro implementaci. Tento atribut uživatele se mapuje na zprostředkovatele identity. Chcete-li zadat vlastní atribut na stránku uživatele, použijte **vlastní mapování SAML** možnost. Nebo můžete vybrat, zda **e-mailu** nebo **ID uživatele** jako atribut uživatele. V našem příkladu jsme vybrali **e-mailu** protože jsme namapované identifikátor deklarace identity uživatele s **userprincipalname** atribut **atributy uživatele a deklarace identity** tématu na webu Azure portal. Tímto způsobem e-mail jedinečných uživatelů, která se posílají do SAP Business objekt cloudové aplikace v každé úspěšné odpovědi SAML.

    ![Vyberte atribut uživatele](./media/sapboc-tutorial/config6.png)

8. Chcete-li ověřit účet pomocí zprostředkovatele identity (krok 4), v **přihlašovacích údajů (E-mail)** zadejte e-mailovou adresu uživatele. Vyberte **ověřte účet**. Systém přidá přihlašovací údaje uživatelského účtu.

    ![Zadejte e-mailu a vyberte ověření účtu](./media/sapboc-tutorial/config7.png)

9. Vyberte **Uložit** ikonu.

    ![Ikonu Uložit](./media/sapboc-tutorial/save.png)

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure tak, že udělíte přístup k SAP Business objektu cloudu.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **SAP Business objekt cloudu**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **SAP Business objekt cloudu**.

    ![SAP Business objekt Cloud odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-sap-business-object-cloud-test-user"></a>Vytvořit testovacího uživatele SAP Business objekt cloudu

Uživatelé Azure AD musí být poskytnuty v cloudu objekt SAP Business předtím, než se mohl přihlásit k SAP Business objekt cloudu. V SAP Business objekt cloudu zřizování je ruční úloha.

Chcete-li vytvořit uživatelský účet:

1. Přihlaste se k serveru SAP Business objekt cloudu společnosti jako správce.

2. Vyberte **nabídky** > **zabezpečení** > **uživatelé**.

    ![Přidat zaměstnance](./media/sapboc-tutorial/user1.png)

3. Na **uživatelé** stránky a přidat nové podrobnosti o uživateli, vyberte **+**. 

    ![Stránka Přidat uživatele](./media/sapboc-tutorial/user4.png)

    Potom proveďte následující kroky:

    a. V **ID uživatele** zadejte ID uživatele uživatele, jako je třeba **Britta**.

    b. V **KŘESTNÍ jméno** zadejte jméno uživatele, jako je třeba **Britta**.

    c. V **příjmení** zadejte příjmení uživatele, jako je třeba **Simon**.

    d. V **ZOBRAZOVANÝ název** zadejte celé jméno uživatele, jako je třeba **Britta Simon**.

    e. V **e-mailu** zadejte e-mailovou adresu uživatele, jako je třeba **brittasimon@contoso.com**.

    f. Na **vybrat role** stránky, vyberte vhodnou roli pro uživatele a pak vyberte **OK**.

      ![Výběr role](./media/sapboc-tutorial/user3.png)

    g. Vyberte **Uložit** ikonu.    

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici SAP Business objekt cloudem na přístupovém panelu, vám by měl být automaticky přihlášeni do cloudu SAP Business objekt, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

