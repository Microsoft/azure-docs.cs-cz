---
title: 'Kurz: Integrace Azure Active Directory s AnswerHub | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a AnswerHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: df88eee14ea8f3d7d7f8365a77750da67904793d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56188048"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Kurz: Integrace Azure Active Directory s AnswerHub

V tomto kurzu se dozvíte, jak integrovat AnswerHub s Azure Active Directory (Azure AD).
AnswerHub integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k AnswerHub.
* Můžete povolit uživatelům být automaticky přihlášeni k AnswerHub (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s AnswerHub, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* AnswerHub jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje AnswerHub **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-answerhub-from-the-gallery"></a>Přidání AnswerHub z Galerie

Konfigurace integrace AnswerHub do služby Azure AD, budete muset přidat AnswerHub z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat AnswerHub z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **AnswerHub**vyberte **AnswerHub** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![AnswerHub v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí AnswerHub podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v AnswerHub.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s AnswerHub, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace AnswerHub Single Sign-On](#configure-answerhub-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele AnswerHub](#create-answerhub-test-user)**  – Pokud chcete mít protějšek Britta Simon AnswerHub, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s AnswerHub, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **AnswerHub** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![AnswerHub domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<company>.answerhub.com`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<company>.answerhub.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory AnswerHub klienta](mailto:success@answerhub.com) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení AnswerHub** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-answerhub-single-sign-on"></a>Konfigurace AnswerHub jednotné přihlašování

1. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti AnswerHub.

    > [!NOTE]
    > Pokud potřebujete pomoc s konfigurací AnswerHub, obraťte se na [tým podpory vaší AnswerHub](mailto:success@answerhub.com.).

2. Přejděte na **správu**.

3. Klikněte na tlačítko **uživatelů a skupin** kartu.

4. V navigačním podokně na levé straně v **sociální nastavení** klikněte na tlačítko **nastavení SAML**.

5. Klikněte na tlačítko **konfigurace zprostředkovatele identity** kartu.

6. Na **konfigurace zprostředkovatele identity** kartu, proveďte následující kroky:

    ![Instalační program SAML](./media/answerhub-tutorial/ic785172.png "nastavení SAML")  
  
    a. V **přihlašovací adresa URL zprostředkovatele identity** vložit do textového pole **přihlašovací adresa URL** zkopírovanou z webu Azure portal.
  
    b. V **odhlašovací adresa URL zprostředkovatele identity** vložit do textového pole **odhlašovací adresa URL** hodnotu, která jste zkopírovali z portálu Azure portal.

    c. V **formát identifikátor zprostředkovatele identity názvu** textového pole zadejte uživatele, identifikátor stejné hodnoty jako panelu webu Azure portal v **atributy uživatele** oddílu.
  
    d. Klikněte na tlačítko **klíčích a certifikátech**.

7. Na **klíčích a certifikátech** kartu, proveďte následující kroky:

    ![Klíče a certifikáty](./media/answerhub-tutorial/ic785173.png "klíčů a certifikátů")  

    a. Otevřete váš certifikát kódovaný base-64, který jste si stáhli z webu Azure portal v programu Poznámkový blok, zkopírujte obsah ho do schránky a vložte ho do **zprostředkovatele identity veřejného klíče (x 509 formátu)** textového pole.
  
    b. Klikněte na **Uložit**.

8. Na **konfigurace zprostředkovatele identity** klikněte na tlačítko **Uložit**.

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

V této části je povolit Britta Simon k udělení přístupu k AnswerHub použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **AnswerHub**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **AnswerHub**.

    ![Odkaz AnswerHub v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-answerhub-test-user"></a>Vytvoření AnswerHub testovacího uživatele

Přihlaste se k AnswerHub Azure AD uživatelům umožnit, musí být poskytnuty do AnswerHub. V případě AnswerHub zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **AnswerHub** společnosti serveru jako správce.

2. Přejděte na **správu**.

3. Klikněte na tlačítko **uživatelů a skupin** kartu.

4. V navigačním podokně na levé straně v **spravovat uživatele** klikněte na tlačítko **vytvoření nebo import uživatelů** a potom klikněte na tlačítko **uživatelů a skupin**.

   ![Uživatelé a skupiny](./media/answerhub-tutorial/ic785175.png "uživatelů a skupin")

5. Typ **e-mailová adresa**, **uživatelské jméno** a **heslo** platného účtu služby Azure Active Directory, kterou chcete zřízení související textová pole a potom klikněte na  **Uložit**.

> [!NOTE]
> Můžete použít jakékoli jiné AnswerHub uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných AnswerHub uživatelským účtům, zřídit AAD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici AnswerHub na přístupovém panelu, můžete by měl být automaticky přihlášeni k AnswerHub, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

