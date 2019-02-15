---
title: 'Kurz: Integrace Azure Active Directory s věrností NetBenefits | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a NetBenefits přesnosti.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 77dc8a98-c0e7-4129-ab88-28e7643e432a
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25171a2d455bda3f6a3c0ee85bad47c44820110c
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56300750"
---
# <a name="tutorial-azure-active-directory-integration-with-fidelity-netbenefits"></a>Kurz: Integrace Azure Active Directory s věrností NetBenefits

V tomto kurzu se dozvíte, jak integrovat věrnost NetBenefits s Azure Active Directory (Azure AD).
Věrnost NetBenefits integrace s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k NetBenefits přesnosti.
* Uživatelům se automaticky přihlášeni k NetBenefits věrnost (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s věrností NetBenefits, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Věrnost NetBenefits jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje věrnost NetBenefits **IDP** jednotné přihlašování zahájené pomocí

* Podporuje věrnost NetBenefits **JIT** zřizování uživatelů

## <a name="adding-fidelity-netbenefits-from-the-gallery"></a>Přidání věrnost NetBenefits z Galerie

Ke konfiguraci integrace věrnost NetBenefits do služby Azure AD, budete muset přidat z Galerie NetBenefits věrností na váš seznam spravovaných aplikací SaaS.

**Věrnost NetBenefits přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **věrnost NetBenefits**vyberte **věrnost NetBenefits** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Věrnost NetBenefits v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí NetBenefits věrností na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v NetBenefits přesnosti.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s věrností NetBenefits, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace věrnost NetBenefits Single Sign-On](#configure-fidelity-netbenefits-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele věrnost NetBenefits](#create-fidelity-netbenefits-test-user)**  – Pokud chcete mít protějšek Britta Simon věrnost NetBenefits, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s věrností NetBenefits, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **věrnost NetBenefits** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránce, proveďte následující kroky:

    ![Věrnost NetBenefits domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce:

    Pro testování prostředí:  `urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    Pro produkční prostředí:  `urn:sp:fidelity:geninbndnbparts20`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, aby věrnost poskytované v době provádění nebo se obraťte na vašeho přiřazené věrnost klienta portálu Service Manager.

5. Věrnost NetBenefits aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, přičemž **nameidentifier** je namapována na žádnou **user.userprincipalname**. Věrnost NetBenefits aplikace očekává **nameidentifier** namapovat s **employeeid** nebo jiné deklarace, které lze použít pro vaši organizaci jako **nameidentifier**, takže budete muset kliknout na Upravit mapování atributů **upravit** ikonu a změnit mapování atributů.

    ![image](common/edit-attribute.png)

    >[!Note]
    >Věrnost NetBenefits podporují statické a dynamické federace. Statické znamená, že jej nebude používat protokol SAML podle metody just-in čas uživatele zřizování a dynamické znamená, že se podporuje jenom v době zřizování uživatelů. Pomocí JIT na základě zřizování zákazníků muset přidat některé další deklarace identity v Azure AD, jako jsou datum narození uživatele atd. Tyto informace jsou poskytovány vaše přiřazené **portálu Service Manager věrnost klienta** a mají k povolení této dynamické federace pro vaši instanci.

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. Na **nastavení přesné NetBenefits** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-fidelity-netbenefits-single-sign-on"></a>Konfigurace věrnost NetBenefits jednotné přihlašování

Ke konfiguraci jednotného přihlašování na **věrnost NetBenefits** straně, je nutné odeslat na stažený **kód XML metadat federace** a vhodné zkopírovaný adresy URL z webu Azure portal [věrnost Tým podpory NetBenefits](mailto:SSOMaintenance@fmr.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k NetBenefits přesnosti.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **věrnost NetBenefits**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **věrnost NetBenefits**.

    ![Věrnost NetBenefits odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-fidelity-netbenefits-test-user"></a>Vytvoření věrnost NetBenefits testovacího uživatele

V této části vytvořte uživatele Britta Simon v NetBenefits přesnosti. Při vytváření statických federace, prosím práci s vaší přiřazené **portálu Service Manager věrnost klienta** vytvořit uživatele v věrnost NetBenefits platformy. Tito uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

Pro dynamické federace uživatelé se vytvoří JIT zřizování uživatelů. Pomocí JIT na základě zřizování zákazníků muset přidat některé další deklarace identity v Azure AD, jako jsou datum narození uživatele atd. Tyto informace jsou poskytovány vaše přiřazené **portálu Service Manager věrnost klienta** a mají k povolení této dynamické federace pro vaši instanci.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici NetBenefits věrností na přístupovém panelu, vám by měl být automaticky přihlášeni ke NetBenefits věrností, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

