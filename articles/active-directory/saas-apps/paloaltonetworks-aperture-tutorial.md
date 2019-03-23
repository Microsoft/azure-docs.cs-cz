---
title: 'Kurz: Integrace Azure Active Directory s Palo Alto Networks - clona | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Palo Alto Networks - clona.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a5ea18d3-3aaf-4bc6-957c-783e9371d0f1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: beb84671db01020f0118502e35128387d36845d6
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361417"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Kurz: Integrace Azure Active Directory s Palo Alto Networks - clona

V tomto kurzu se dozvíte, jak integrovat Palo Alto Networks - clona se službou Azure Active Directory (Azure AD).
Integrace Palo Alto Networks - clona s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Palo Alto Networks - clona.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k Palo Alto Networks - Clona (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Palo Alto Networks - clona, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Palo Alto Networks - clona jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje clona Palo Alto Networks - **SP** a **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Přidání Palo Alto Networks - clona z Galerie

Ke konfiguraci integrace Palo Alto Networks - clona do služby Azure AD, budete muset přidat Palo Alto Networks - clona z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Palo Alto Networks - clona z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Palo Alto Networks - clona**vyberte **Palo Alto Networks - clona** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Palo Alto Networks - otvorů v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, nakonfigurovat a otestovat Azure AD jednotné přihlašování s Palo Alto Networks – clona podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování k práci, vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Palo Alto Networks - clona potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Palo Alto Networks - clona, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Palo Alto Networks - clona Single Sign-On](#configure-palo-alto-networks---aperture-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření Palo Alto Networks - clona testovacího uživatele](#create-palo-alto-networks---aperture-test-user)**  – Pokud chcete mít protějšek Britta Simon Palo Alto Networks - clona, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Palo Alto Networks - clona, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Palo Alto Networks - clona** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![Palo Alto Networks - clona domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Palo Alto Networks - clona domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečnou adresu URL identifikátor, adresa URL odpovědi a přihlašování. Kontakt [Palo Alto Networks - tým podpory clona klienta](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. Na **nastavení Palo Alto Networks - clona** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-palo-alto-networks---aperture-single-sign-on"></a>Konfigurace Palo Alto Networks - clona jednotného přihlašování

1. V jiné okno webového prohlížeče, přihlaste se k Palo Alto Networks - clona jako správce.

2. V horní nabídce klikněte na **nastavení**.

    ![Na kartě nastavení](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

3. Přejděte do **aplikace** klikněte na **ověřování** formuláře levé nabídce.

    ![Na kartě vícefaktorového ověřování](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
4. Na **ověřování** stránku, proveďte následující kroky:
    
    ![Na kartě ověřování](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Zkontrolujte, **povolit jednotné přihlašování – On(Supported SSP Providers are Okta, One login)** z **Single Sign-On** pole.

    b. V **ID zprostředkovatele Identity** textového pole vložte hodnotu **Azure AD identifikátor**, který jste zkopírovali z portálu Azure portal.

    c. Klikněte na tlačítko **zvolit soubor** na kterou odešlete certifikát stažený z Azure AD **certifikát poskytovatele Identity** pole.

    d. V **adresa URL jednotného přihlašování pro zprostředkovatele Identity** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    e. Přečtěte si informace zprostředkovatele identity z **clona informace** části a stáhněte si certifikát od **clona klíč** pole.

    f. Klikněte na **Uložit**.

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

V této části je povolit Britta Simon k udělení přístupu k Palo Alto Networks - clona použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Palo Alto Networks - clona**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Palo Alto Networks - clona**.

    ![Palo Alto Networks - link clona v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-palo-alto-networks---aperture-test-user"></a>Vytvoření Palo Alto Networks - clona testovacího uživatele

V této části vytvoříte uživatele v Palo Alto Networks - clona jako Britta Simon. Práce s [Palo Alto Networks - tým podpory clona klienta](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) k přidání uživatelů do Palo Alto Networks - clona platformy. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na Palo Alto Networks - clona dlaždici na přístupovém panelu, vám by měl být automaticky přihlášeni ke Palo Alto Networks - clona, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

