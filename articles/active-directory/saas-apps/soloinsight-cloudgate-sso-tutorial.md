---
title: 'Kurz: Integrace Azure Active Directory s jednotným Přihlašováním Soloinsight CloudGate | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Soloinsight CloudGate jednotného přihlašování.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9263c241-85a4-4724-afac-0351d6275958
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: jeedes
ms.openlocfilehash: 00a3a462ebdac49f0801205f7105a32914416d23
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56007994"
---
# <a name="tutorial-azure-active-directory-integration-with-soloinsight-cloudgate-sso"></a>Kurz: Integrace Azure Active Directory s jednotným Přihlašováním Soloinsight CloudGate

V tomto kurzu se dozvíte, jak integrovat Soloinsight CloudGate jednotného přihlašování s Azure Active Directory (Azure AD).
Integrace Soloinsight CloudGate jednotného přihlašování s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Soloinsight CloudGate jednotného přihlašování.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k Soloinsight CloudGate jednotné přihlašování (Single Sign-On) pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s jednotným Přihlašováním Soloinsight CloudGate, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Jednotné přihlašování Soloinsight CloudGate jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje jednotné přihlašování Soloinsight CloudGate **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Přidání jednotného přihlašování Soloinsight CloudGate z Galerie

Konfigurace integrace Soloinsight CloudGate jednotné přihlašování do služby Azure AD, budete muset přidat Soloinsight CloudGate jednotného přihlašování na váš seznam spravovaných aplikací SaaS z galerie.

**Chcete-li přidat Soloinsight CloudGate jednotné přihlašování z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **jednotného přihlašování Soloinsight CloudGate**vyberte **Soloinsight CloudGate jednotného přihlašování** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Jednotné přihlašování Soloinsight CloudGate v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování s jednotným Přihlašováním Soloinsight CloudGate podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Soloinsight CloudGate jednotného přihlašování.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s jednotným Přihlašováním Soloinsight CloudGate, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Soloinsight CloudGate jednotného přihlašování Single Sign-On](#configure-soloinsight-cloudgate-sso-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Soloinsight CloudGate SSO](#create-soloinsight-cloudgate-sso-test-user)**  – Pokud chcete mít protějšek Britta Simon Soloinsight CloudGate jednotného přihlašování, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s jednotným Přihlašováním Soloinsight CloudGate, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **jednotného přihlašování Soloinsight CloudGate** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Soloinsight CloudGate jednotného přihlašování k doméně a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<SUBDOMAIN>.sigateway.com/login`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<SUBDOMAIN>.sigateway.com/process/sso`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné přihlašovací adresu URL a identifikátor, který je vysvětlen později v **nakonfigurovat Soloinsight CloudGate jednotného přihlašování jednotného přihlašování** části kurzu.

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavit jednotné přihlašování Soloinsight CloudGate** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-soloinsight-cloudgate-sso-single-sign-on"></a>Konfigurace Soloinsight CloudGate jednotného přihlašování jednotného přihlašování

1. K získání hodnot, které se mají vložit na webu Azure Portal při konfiguraci základní SAML, přihlaste se k CloudGate webový portál, pomocí svých přihlašovacích údajů a potom přístup k nastavení jednotného přihlašování, které najdete v následující cestě **Domů > Správa > systému Nastavení > Obecné**.

    ![Nastavení jednotného přihlašování CloudGate](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

2. **Adresa URL zákazníka SAML**

    * Zkopírujte odkazy k dispozici na **adresu URL příjemce Saml** a **adresy URL pro přesměrování** polí a vložit je na webu Azure Portal **základní konfiguraci SAML** části  **Identifikátor (Entity ID)** a **adresy URL odpovědi** polí v uvedeném pořadí.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

3. **Podpisový certifikát SAML**

    * Přejděte na zdroj, který jste stáhli z Azure podpisový certifikát SAML webu Portal seznamy a klikněte na něj pravým tlačítkem na soubor certifikátu (Base64). Zvolte **upravovat pomocí poznámkového bloku ++** možnost ze seznamu. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Zkopírujte obsah v souboru poznámkového bloku ++ certifikát (Base64).

        ![Kopie certifikátu](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Vložte obsah v nastavení jednotného přihlašování k portálu CloudGate webu **certifikát** pole a klikněte na tlačítko Uložit.

        ![Portál Certificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

4. **Výchozí skupiny**

    * Vyberte **správce Business** z rozevíracího seznamu **výchozí skupinu** možnost CloudGate webovém portálu

        ![Výchozí skupiny](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

5. **Identifikátor AD a přihlašovací adresa URL**

    * Zkopírovaný **přihlašovací adresa URL** z webu Azure Portal **nastavit jednotné přihlašování Soloinsight CloudGate** konfigurace mají být zadány v části Nastavení jednotného přihlašování k CloudGate webu portálu. 

    * Vložit **přihlašovací adresa URL** odkazu z webu Azure Portal na webovém portálu CloudGate **přihlašovací adresa URL AD** pole.
     
    * Vložit **Azure AD identifikátor** odkazu z webu Azure Portal na webovém portálu CloudGate **AD identifikátor** pole

        ![Přihlášení k AD](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Soloinsight CloudGate jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **jednotného přihlašování Soloinsight CloudGate**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **jednotného přihlašování Soloinsight CloudGate**.

    ![Jednotné přihlašování Soloinsight CloudGate odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Vytvořit testovacího uživatele Soloinsight CloudGate jednotného přihlašování

Chcete-li vytvořit testovacího uživatele, vyberte **zaměstnanci** v hlavní nabídce CloudGate webový portál a vyplnit formulář přidat nové zaměstnance. Úroveň autoritu, která má být přiřazena testovacího uživatele je **správce Business** klikněte na **vytvořit** po všechna povinná pole jsou vyplněna.

![Zaměstnanec testu](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Soloinsight CloudGate jednotného přihlašování na přístupovém panelu, vám by měl být automaticky přihlášeni ke Soloinsight CloudGate jednotného přihlašování, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

