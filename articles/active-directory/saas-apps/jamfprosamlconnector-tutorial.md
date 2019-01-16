---
title: 'Kurz: Integrace Azure Active Directory s Jamf Pro | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: ebd9f2115116e8f9cf976c86e4914f0b7f6beb39
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54322108"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>Kurz: Integrace Azure Active Directory s Jamf Pro

V tomto kurzu se dozvíte, jak integrace Jamf Pro s Azure Active Directory (Azure AD).
Integrace Jamf Pro s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Jamf Pro.
* Uživatelům se automaticky přihlášeni k Jamf Pro (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Jamf Pro, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Jamf Pro jednotné přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Jamf Pro podporuje **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí

## <a name="adding-jamf-pro-from-the-gallery"></a>Jamf Pro přidání z Galerie

Konfigurace integrace Jamf Pro do služby Azure AD, budete muset přidat Jamf Pro do seznamu spravovaných aplikací SaaS z galerie.

**Chcete-li přidat Jamf Pro z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Jamf Pro**vyberte **Jamf Pro** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![V seznamu výsledků Jamf Pro](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a test Azure AD jednotné přihlašování s Jamf Pro podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Jamf Pro.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Jamf Pro, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace aplikací Jamf Pro jednotné přihlašování](#configure-jamf-pro-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Jamf Pro](#create-jamf-pro-test-user)**  – Pokud chcete mít protějšek Britta Simon v Jamf Pro, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Jamf Pro, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Jamf Pro** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat v **IDP** intiated režimu proveďte následující kroky:

    ![Jamf Pro domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.jamfcloud.com/saml/SSO`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<subdomain>.jamfcloud.com`

    ![Jamf Pro domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Zobrazí se skutečná hodnota identifikátoru z **Single Sign-On** části portálu Jamf Pro, který je vysvětlen později v tomto kurzu. Můžete rozbalit skutečnou **subdoménu** hodnoty z hodnoty identifikátor, který budete používat **subdoménu** informace v přihlašovací adresu URL a adresy URL odpovědi. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a uložte ji na vaše počítač.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-jamf-pro-single-sign-on"></a>Konfigurace Jamf Pro jednotné přihlašování

1. K automatizaci konfigurace v Jamf Pro, je potřeba nainstalovat **Moje aplikace zabezpečené přihlašování rozšíření prohlížeče** kliknutím **nainstalovat rozšíření**.

    ![image](./media/jamfprosamlconnector-tutorial/install_extension.png)

2. Po přidání rozšíření do prohlížeče, klikněte na **nastavení Jamf Pro** nasměruje na aplikaci Jamf Pro. Odtud zadejte přihlašovací údaje správce pro přihlášení do Jamf Pro. Rozšíření prohlížeče budou automaticky nakonfigurovat aplikaci za vás a automatizovat kroky 3 – 7.

    ![image](./media/jamfprosamlconnector-tutorial/d1_saml.png)

3. Pokud chcete nastavit Jamf Pro ručně, otevřete nové okno webového prohlížeče a protokolu Jamf Pro web společnosti jako správce a proveďte následující kroky:

4. Klikněte na **ikona nastavení** v pravém horním rohu stránky.

    ![Jamf Pro Configuration](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Klikněte na **jednotné přihlašování**.

    ![Jamf Pro Configuration](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Na **Single Sign-On** stránku, proveďte následující kroky:

    ![Jeden Jamf Pro](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Vyberte **Jamf Pro Server** umožňuje jednotné přihlašování přístup.

    b. Výběrem **povolit jednorázové přihlášení pro všechny uživatele** uživatelé nebudou přesměrováni na stránku pro přihlášení zprostředkovatele Identity pro ověřování, ale můžete přihlásit k Jamf Pro přímo místo. Když se uživatel pokusí o přístup k Jamf Pro pomocí zprostředkovatele Identity, dojde k autorizaci a ověřování jednotného přihlašování zahájené pomocí IdP.

    c. Vyberte **NameID** možnost **mapování uživatelů: SAML**. Ve výchozím nastavení, toto nastavení nastavené na **NameID** ale můžete definovat vlastní atribut.

    d. Vyberte **e-mailu** pro **mapování uživatelů: JAMF PRO**. Jamf Pro mapování atributů SAML odeslán ve zprostředkovateli identity následujícími způsoby: uživatelé a skupiny. Když se uživatel pokusí o přístup k Jamf Pro, ve výchozím nastavení Jamf Pro získá informace o uživateli od zprostředkovatele Identity a shoduje s Jamf Pro uživatelské účty. Pokud příchozí uživatelský účet v Jamf Pro neexistuje, pak odpovídající název skupiny vyvolá.

    e. Vložte hodnotu `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` v **název ATRIBUTU skupiny** textového pole.

7. Na stejnou stránku přejít na **zprostředkovatele IDENTITY** pod **Single Sign-On** části a proveďte následující kroky:

    ![Jamf Pro Configuration](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Vyberte **jiných** jako možnost z **zprostředkovatele IDENTITY** rozevíracího seznamu.

    b. V **ostatní poskytovatele** textového pole zadejte **Azure AD**.

    c. Vyberte **adresa URL metadat** jako možnost z **zdroj METADAT zprostředkovatele IDENTITY** rozevíracího seznamu a v následujícím textovém poli, vložte **adresa Url federačních metadat aplikace** hodnotu, která jste zkopírovali z portálu Azure portal.

    d. Kopírovat **Entity ID** hodnotu a vložte ho do **identifikátor (Entity ID)** textového pole v **Jamf Pro domény a adresy URL** části na webu Azure portal.

    > [!NOTE]
    > Tady rozmazaný hodnota je součástí subdomény. K dokončení přihlašovací adresu URL a adresy URL odpovědi v použít tuto hodnotu **Jamf Pro domény a adresy URL** části na webu Azure portal.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Jamf Pro.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Jamf Pro**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Jamf Pro**.

    ![Jamf Pro odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-jamf-pro-test-user"></a>Vytvořit testovacího uživatele Jamf Pro

Přihlaste se k Jamf Pro Azure AD uživatelům umožnit, musí být poskytnuty do Jamf Pro. V případě Jamf Pro zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se na web společnosti Jamf Pro jako správce.

2. Klikněte na **ikona nastavení** v pravém horním rohu stránky.

    ![Přidat zaměstnance](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Klikněte na **Jamf Pro uživatelské účty a skupiny**.

    ![Přidat zaměstnance](./media/jamfprosamlconnector-tutorial/user1.png)

4. Klikněte na možnost **Nové**.

    ![Přidat zaměstnance](./media/jamfprosamlconnector-tutorial/user2.png)

5. Vyberte **vytvořit standardní účet**.

    ![Přidat zaměstnance](./media/jamfprosamlconnector-tutorial/user3.png)

6. Na **nový účet** dailog, proveďte následující kroky:

    ![Přidat zaměstnance](./media/jamfprosamlconnector-tutorial/user4.png)

    a. V **uživatelské jméno** textového pole zadejte úplný název BrittaSimon.

    b. Vyberte příslušné možnosti podle vaší organizaci pro **úroveň přístupu**, **oprávnění NASTAVENA**a pro **stav přístupu**.

    c. V **jméno a příjmení** textového pole zadejte úplný název Britta Simon.

    d. V **e-MAILOVOU adresu** textového pole zadejte e-mailovou adresu účtu Britta Simon.

    e. V **heslo** textového pole zadejte heslo uživatele.

    f. V **OVĚŘTE heslo** textového pole zadejte heslo uživatele.

    g. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Jamf Pro na přístupovém panelu, vám by měl být automaticky přihlášeni k Jamf Pro, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
