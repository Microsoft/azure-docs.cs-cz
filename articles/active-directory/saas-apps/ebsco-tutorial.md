---
title: 'Kurz: Integrace Azure Active Directory s EBSCO | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a EBSCO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 2753daf225016d3bd8e07383193a1260b40a36d5
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564971"
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>Kurz: Integrace Azure Active Directory s EBSCO

V tomto kurzu se dozvíte, jak integrovat EBSCO s Azure Active Directory (Azure AD).
EBSCO integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k EBSCO.
* Můžete povolit uživatelům být automaticky přihlášeni k EBSCO (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s EBSCO, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* EBSCO jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje EBSCO **SP** a **IDP** jednotné přihlašování zahájené pomocí

* Podporuje EBSCO **JIT** zřizování uživatelů

## <a name="adding-ebsco-from-the-gallery"></a>Přidání EBSCO z Galerie

Konfigurace integrace EBSCO do služby Azure AD, budete muset přidat EBSCO z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat EBSCO z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **EBSCO**vyberte **EBSCO** na panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![EBSCO v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí EBSCO podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v EBSCO.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s EBSCO, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace EBSCO Single Sign-On](#configure-ebsco-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele EBSCO](#create-ebsco-test-user)**  – Pokud chcete mít protějšek Britta Simon EBSCO, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s EBSCO, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **EBSCO** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na tlačítko **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, postupujte následovně:

    ![EBSCO domény a adresy URL jednotného přihlašování – informace](common/idp-identifier.png)

    V **identifikátor** textové pole, zadejte adresu URL:  `pingsso.ebscohost.com`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![image](common/both-preintegrated-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > Hodnota přihlašovací adresa URL není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory EBSCO klienta](mailto:sso@ebsco.com) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

    o **jedinečných prvků:**  

    o **Custid** = zadejte jedinečné ID zákazníka EBSCO 

    o **profilu** = klientů můžete přizpůsobit na odkaz k přesměrování uživatelů k určitému profilu (v závislosti na tom, co může zakoupit od EBSCO). Zadat ID konkrétní profil Hlavní ID jsou eds (EBSCO zjišťování služby) a ehost (EBSOCOhost databáze). Jsou uvedeny pokyny pro stejný [tady](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

6. EBSCO aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Na následujícím snímku obrazovky se zobrazí v seznamu atributů výchozí. Klikněte na tlačítko **upravit** ikony otevřete **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

     > [!Note]
    > **Název** atribut je povinný a je namapovaný s **hodnota název identifikátoru** EBSCO aplikace. Ve výchozím nastavení to je přidáván, takže není nutné přidat ručně.

7. Kromě toho výše EBSCO aplikace očekává, že několik dalších atributů musí být předány zpět odpověď SAML. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulka: 

    | Název | Zdrojový atribut|
    | ---------------| --------------- |    
    | FirstName   | user.givenname |
    | LastName   | user.surname |
    | Email   | user.mail |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na **Uložit**.

8. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

9. Na **nastavení EBSCO** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-ebsco-single-sign-on"></a>Konfigurace EBSCO jednotného přihlašování

Ke konfiguraci jednotného přihlašování na **EBSCO** straně, je nutné odeslat na stažený **soubor XML s metadaty** a vhodné zkopírovaný adresy URL z webu Azure portal [tým podpory EBSCO](mailto:sso@ebsco.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole brittasimon@yourcompanydomain.extension. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k EBSCO použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **EBSCO**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **EBSCO**.

    ![Odkaz EBSCO v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-ebsco-test-user"></a>Vytvoření EBSCO testovacího uživatele

V případě EBSCO je automatické zřizování uživatelů.

**K poskytnutí uživatelského účtu, postupujte následovně:**

Azure AD předá aplikaci EBSCO požadovaná data. Zřizování uživatelů pro EBSCO lze automatické nebo vyžadují formuláři jednorázového. To závisí na Určuje, zda má klient spoustu existující účty EBSCOhost s osobní nastavení se uložila. Stejné můžete popsané s [tým podpory EBSCO](mailto:sso@ebsco.com) během provádění. V obou případech klient nemá k vytváření účtů EBSCOhost před testování.

   >[!Note]
   >Můžete automatizovat zřizování uživatelů EBSCOhost/přizpůsobení. Kontakt [tým podpory EBSCO](mailto:sso@ebsco.com) o Just-In-Time zřizování uživatelů. 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

1. Po kliknutí na dlaždici EBSCO na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci EBSCO.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

2. Jakmile se přihlásíte do aplikace, klikněte na **přihlášení** tlačítko v pravém horním rohu.

    ![Přihlášení EBSCO v seznamu aplikací](./media/ebsco-tutorial/tutorial_ebsco_signin.png)
 
3. Zobrazí se jednorázové řádku spárovat institucionální/SAML přihlášení pomocí **propojit existující účet MyEBSCOhost se svým účtem instituce nyní** nebo **vytvořit nový účet MyEBSCOhost a připojit ho k vaší účet instituce**. Účet se používá pro přizpůsobení v aplikaci EBSCOhost. Vyberte možnost **vytvořit nový účet** a uvidíte, že formuláře pro přizpůsobení je předem vyplněný s hodnotami z odpověď saml, jak je znázorněno v následujícím snímku obrazovky. Klikněte na tlačítko **'Pokračovat'** uložte tento výběr.
    
     ![EBSCO uživatele v seznamu aplikací](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. Po dokončení výše uvedených nastavení vymazání mezipaměti nebo soubory cookie a přihlaste se znovu. Nebudete už muset ručně znovu se přihlaste a uloží, individuální nastavení

## <a name="additional-sesources"></a>Další sesources

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

