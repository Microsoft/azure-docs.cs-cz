---
title: 'Kurz: Integrace s Azure Active Directory s HRworks Single Sign-On | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a HRworks Single Sign-On.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c4c5d434-3f8a-411e-83a5-c3d5276ddc0a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: e994150f68c0f274c97c27fccc89f226bc8236cd
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2019
ms.locfileid: "66000498"
---
# <a name="tutorial-azure-active-directory-integration-with-hrworks-single-sign-on"></a>Kurz: Integrace s Azure Active Directory s HRworks jednotného přihlašování

V tomto kurzu se dozvíte, jak integrovat HRworks jednotné přihlašování s Azure Active Directory (Azure AD).
Integrace HRworks jednotné přihlašování s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k HRworks jednotného přihlašování.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k HRworks jednotné přihlašování (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s HRworks Single Sign-On, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* HRworks Single Sign-On jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* HRworks jednotného přihlašování podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>Přidání HRworks Single Sign-On z Galerie

Konfigurace integrace nástroje HRworks jednotné přihlašování do služby Azure AD, budete muset přidat HRworks Single Sign-On z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat HRworks Single Sign-On z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **HRworks Single Sign-On**vyberte **HRworks Single Sign-On** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![HRworks jednotné přihlašování v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestovat Azure AD jednotné přihlašování s HRworks jednotného přihlašování na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v HRworks jednotného přihlašování.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s HRworks jednotného přihlašování, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace HRworks jednotné přihlašování jednotného přihlašování](#configure-hrworks-single-sign-on-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele HRworks Single Sign-On](#create-hrworks-single-sign-on-test-user)**  – Pokud chcete mít protějšek Britta Simon v HRworks jednotného přihlašování, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s HRworks Single Sign-On, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **HRworks Single Sign-On** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![HRworks jednotné přihlašování domény a adresy URL jednotného přihlašování – informace](common/sp-signonurl.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://login.hrworks.de/?companyId=<companyId>&directssologin=true`

    > [!NOTE]
    > Hodnota není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory HRworks jednotné přihlašování klienta](mailto:support@hrworks.de) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavit až HRworks Single Sign-On** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-hrworks-single-sign-on-single-sign-on"></a>Konfigurace HRworks Single Sign-On jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k HRworks Single Sign-On jako správce.

2. Klikněte na **správce** > **Základy** > **zabezpečení** > **jednotného přihlašování** z levé nabídky panelu a proveďte následující kroky:

       ![Configure Single Sign-On](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Zkontrolujte, **použití jednotného přihlašování** pole.

    b. Vyberte **XML metadat** jako **metadat vstupní metodu**.

    c. Vyberte **jednotlivé NameID identifikátor** jako **hodnotu NameID**.

    d. V poznámkovém bloku otevřete kód XML metadat, který jste si stáhli z webu Azure portal, zkopírujte jeho obsah a vložte jej do **metadat** textového pole.

    e. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** pole, zadejte uživatelské jméno jako je BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k HRworks Single Sign-On.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **HRworks Single Sign-On**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **HRworks Single Sign-On**.

    ![Odkaz HRworks Single Sign-On v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-hrworks-single-sign-on-test-user"></a>Vytvoření HRworks Single Sign-On testovacího uživatele

Azure AD uživatelům umožnit přihlásit v k HRworks Single Sign-On, se musí být poskytnuty do HRworks jednotného přihlašování. V HRworks jednotného přihlašování, zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k HRworks jednotné přihlášení jako správce.

2. Klikněte na **správce** > **osoby** > **osoby** > **nové osobě** z levé části řádku nabídek.

     ![Konfigurace jednotného přihlašování](./media/hrworks-single-sign-on-tutorial/configure02.png)

3. V automaticky otevíraném okně klikněte na **Další**.

    ![Konfigurace jednotného přihlašování](./media/hrworks-single-sign-on-tutorial/configure03.png)

4. Na **vytvořit nové osoba s země pro právní podmínky** automaticky otevíraném okně zadejte příslušné podrobnosti, jako jsou **křestní jméno**, **příjmení** a klikněte na tlačítko **vytvořit**.
    
    ![Konfigurace jednotného přihlašování](./media/hrworks-single-sign-on-tutorial/configure04.png)

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici HRworks Single Sign-On na přístupovém panelu, vám by měl být automaticky přihlášeni k HRworks jednotného přihlašování u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

