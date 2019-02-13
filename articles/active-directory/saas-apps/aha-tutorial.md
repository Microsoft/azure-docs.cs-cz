---
title: 'Kurz: Integrace Azure Active Directory s Aha! | Dokumenty Microsoft'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Aha!.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ad955d3d-896a-41bb-800d-68e8cb5ff48d
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a68deb2cb6de90c577f2d828e24a1747662c7e2
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56162199"
---
# <a name="tutorial-azure-active-directory-integration-with-aha"></a>Kurz: Integrace Azure Active Directory s Aha!

V tomto kurzu se dozvíte, jak integrovat Aha! s Azure Active Directory (Azure AD).
Integrace Aha! s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Aha!.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k Aha! (Jednotné přihlašování) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Aha!, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* AHA! jednotné přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* AHA! podporuje **SP** jednotné přihlašování zahájené pomocí
* AHA! podporuje **JIT** zřizování uživatelů

## <a name="adding-aha-from-the-gallery"></a>Přidání Aha! z Galerie

Konfigurace integrace Aha! do služby Azure AD budete muset přidat Aha! z Galerie tak, aby váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Aha! v galerii proveďte následující kroky:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Aha!** vyberte **Aha!** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![AHA! v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Aha! na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci, vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Aha! je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Aha!, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Aha! Jednotné přihlašování](#configure-aha-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořte Aha! testovací uživatele](#create-aha-test-user)**  – Pokud chcete mít protějšek Britta Simon Aha! připojený k Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Aha!, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Aha!** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![AHA! Domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.aha.io/session/new`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.aha.io`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [Aha! Tým podpory klienta](https://www.aha.io/company/contact) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavení Aha!** části a zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-aha-single-sign-on"></a>Konfigurace Aha! Jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k vaší Aha! web společnosti jako správce.

2. V nabídce v horní části klikněte na tlačítko **nastavení**.

    ![Nastavení](./media/aha-tutorial/IC798950.png "nastavení")

3. Klikněte na tlačítko **účet**.
  
    ![Profil](./media/aha-tutorial/IC798951.png "profilu")

4. Klikněte na tlačítko **zabezpečení a jednotné přihlašování**.

    ![Zabezpečení a jednotné přihlašování](./media/aha-tutorial/IC798952.png "zabezpečení a jednotné přihlašování")

5. V **Single Sign-On** části jako **zprostředkovatele Identity**vyberte **SAML2.0**.

    ![Zabezpečení a jednotné přihlašování](./media/aha-tutorial/IC798953.png "zabezpečení a jednotné přihlašování")

6. Na **Single Sign-On** konfigurační stránce, proveďte následující kroky:

    ![Jednotné přihlašování](./media/aha-tutorial/IC798954.png "jednotného přihlašování")

    a. V **název** textového pole zadejte název pro vaši konfiguraci.

    b. Pro **konfigurace pomocí**vyberte **soubor metadat**.

    c. Nahrát soubor stažený metadat, klikněte na tlačítko **Procházet**.

    d. Klikněte na tlačítko **aktualizace**.

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

V této části povolíte Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Aha!.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Aha!**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Aha!**.

    ![Aha! odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-aha-test-user"></a>Vytvořte Aha! testovací uživatel

V této části se vytvoří uživateli Britta Simon v Aha!. AHA! podporuje just-in-time zřizování uživatelů, která je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi Aha!, nová je jedna vytvořená po ověření.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete Aha! na přístupovém panelu, dlaždice, vám by měl být automaticky přihlášeni k Aha! u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
