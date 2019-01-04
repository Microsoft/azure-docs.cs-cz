---
title: 'Kurz: Integrace Azure Active Directory s Dropbox for Business | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Dropbox for Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/20/2018
ms.author: jeedes
ms.openlocfilehash: a05a246a3bdf0594484cbbf89998e337d2be5a12
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974960"
---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Kurz: Integrace Azure Active Directory s Dropbox for Business

V tomto kurzu se dozvíte, jak integrovat Dropboxu pro firmy s Azure Active Directory (Azure AD).
Dropbox for Business integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup na Dropbox for Business.
* Můžete povolit uživatelům, aby se automaticky přihlášeni na Dropbox for Business (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Dropbox for Business, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Z Dropboxu obchodní jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Dropbox pro firmy podporuje **SP** jednotné přihlašování zahájené pomocí

* Dropbox pro firmy podporuje **JIT** zřizování uživatelů

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Dropbox for Business přidat z Galerie

Pokud chcete nakonfigurovat integraci Dropbox for Business do služby Azure AD, budete muset Dropbox for Business přidat z Galerie na váš seznam spravovaných aplikací SaaS.

**Dropbox for Business přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Dropbox for Business**vyberte **Dropbox for Business** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Dropbox for Business v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestovat Azure AD jednotné přihlašování s Dropboxu firmy podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Dropboxu pro firmy.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Dropbox for Business, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Dropboxu pro obchodní Single Sign-On](#configure-dropbox-for-business-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření Dropboxu pro obchodní testovacího uživatele](#create-dropbox-for-business-test-user)**  – Pokud chcete mít protějšek Britta Simon v Dropboxu pro podniky, které souvisí s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Dropbox for Business, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Dropbox for Business** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Dropbox obchodní domény a adresy URL jednotné přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://www.dropbox.com/sso/<id>`

    b. V **identifikátor (Entity ID)** textového pole zadejte hodnotu: `Dropbox`

    > [!NOTE]
    > Předchozí hodnota přihlašovací adresa URL není skutečné hodnoty. Hodnota bude aktualizován skutečné přihlašovací adresa URL, který je vysvětlen později v tomto kurzu.

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení Dropbox for Business** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-dropbox-for-business-single-sign-on"></a>Konfigurace Dropboxu pro obchodní jednotného přihlašování

1. Ke konfiguraci jednotného přihlašování na **Dropbox for Business** straně, přejděte na váš Dropbox pro obchodní tenanta a přihlaste do vaší schránky pro obchodní tenanta.

    ![Konfigurace jednotného přihlašování](./media/dropboxforbusiness-tutorial/ic769509.png "nakonfigurovat jednotné přihlašování")

2. Klikněte na **ikonu uživatele** a vyberte **nastavení** kartu.

    ![Konfigurace jednotného přihlašování](./media/dropboxforbusiness-tutorial/configure1.png "nakonfigurovat jednotné přihlašování")

3. V navigačním podokně na levé straně klikněte na tlačítko **konzoly pro správu**.

    ![Konfigurace jednotného přihlašování](./media/dropboxforbusiness-tutorial/configure2.png "nakonfigurovat jednotné přihlašování")

4. Na **konzoly pro správu**, klikněte na tlačítko **nastavení** v levém navigačním podokně.

    ![Konfigurace jednotného přihlašování](./media/dropboxforbusiness-tutorial/configure3.png "nakonfigurovat jednotné přihlašování")

5. Vyberte **jednotného přihlašování** v části **ověřování** oddílu.

    ![Konfigurace jednotného přihlašování](./media/dropboxforbusiness-tutorial/configure4.png "nakonfigurovat jednotné přihlašování")

6. V **jednotného přihlašování** části, proveďte následující kroky:  

    ![Konfigurace jednotného přihlašování](./media/dropboxforbusiness-tutorial/configure5.png "nakonfigurovat jednotné přihlašování")

    a. Vyberte **vyžaduje** jako možnost z rozevíracího seznamu **jednotného přihlašování**.

    b. Klikněte na **přidat přihlašovací adresu URL** a **přihlašovací adresa URL zprostředkovatele Identity** vložit do textového pole **přihlašovací adresa URL** hodnotu, která jste zkopírovali z Azure portal a potom vyberte  **Provádí**.

    ![Konfigurace jednotného přihlašování](./media/dropboxforbusiness-tutorial/configure6.png "nakonfigurovat jednotné přihlašování")

    c. Klikněte na tlačítko **nahrát certifikát**a potom přejděte k vaší **soubor certifikátu v kódování Base64** který jste si stáhli z portálu Azure portal.

    d. Klikněte na **Kopírovat odkaz** a vložte zkopírovaný hodnotu do **přihlašovací adresa URL** textové pole z **Dropboxu obchodní domény a adresy URL** části na webu Azure portal.

    e. Klikněte na **Uložit**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu na Dropbox for Business.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Dropbox for Business**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **Dropbox for Business**.

    ![Dropbox pro obchodní odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-dropbox-for-business-test-user"></a>Vytvoření Dropboxu pro obchodní testovacího uživatele

V této části se vytvoří uživateli Britta Simon v Dropboxu pro firmy. Dropbox for Business podporuje zřizování uživatelů v čase, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel ještě neexistuje v Dropboxu pro firmy, se po ověření vytvoří nový.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory obchodních klienta z Dropboxu](https://www.dropbox.com/business/contact)

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na Dropboxu pro obchodní dlaždici na přístupovém panelu, vám by měl být automaticky přihlášeni na Dropbox for Business, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

