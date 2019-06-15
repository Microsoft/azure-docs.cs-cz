---
title: 'Kurz: Integrace Azure Active Directory s Mitel Connect | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Mitel připojení.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: b656bbb7c5924f14b300f0352551530bb47f2a53
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67097120"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>Kurz: Integrace Azure Active Directory s Mitel MiCloud připojení

V tomto kurzu se dozvíte, jak integrovat Mitel MiCloud připojení s Azure Active Directory (Azure AD). Integrace s Azure AD MiCloud Connect poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k aplikacím MiCloud připojovat pomocí svých podnikových přihlašovacích údajů.
* Uživatelům můžete povolit na svůj účet a být automaticky přihlášeni k MiCloud připojení (Single Sign-On) s jejich účty Azure AD.


Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s MiCloud připojit, potřebujete následující položky:

* Předplatné Azure AD

  Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Účet připojení MiCloud Mitel

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu budete nakonfigurovat a otestovat Azure AD jednotné přihlašování (SSO).

* Připojit Mitel podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-mitel-connect-from-the-gallery"></a>Přidání připojení Mitel z Galerie

Pokud chcete nakonfigurovat integraci Mitel připojení do služby Azure AD, musíte doplnit Mitel připojit z Galerie váš seznam spravovaných aplikací SaaS na webu Azure Portal.

**Pokud chcete přidat připojení Mitel z galerie, proveďte následující kroky:**

1. Na webu **[Azure Portal](https://portal.azure.com)** klikněte na levém navigačním panelu na **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Klikněte na tlačítko **podnikové aplikace** a potom klikněte na tlačítko **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Klikněte na tlačítko **novou aplikaci**.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Typ **Mitel připojení** do vyhledávacího pole, klikněte na tlačítko **Mitel připojení** panel výsledků a pak klikněte na **přidat**.

     ![Mitel připojení v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části budete konfigurovat a test Azure AD jednotné přihlašování s MiCloud připojení podle testovacího uživatele s názvem **Britta Simon**. Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v MiCloud připojení.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s MiCloud Connect, budete muset provést následující kroky:

1. **[Konfigurace připojení MiCloud pro jednotné přihlašování s Azure AD](#configure-micloud-connect-for-sso-with-azure-ad)**  – Pokud chcete povolit uživatelům používat tuto funkci a ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
4. **[Vytvoření zkušebního uživatele připojit MiCloud Mitel](#create-a-mitel-micloud-connect-test-user)**  – Pokud chcete mít protějšek Britta Simon na vašem účtu MiCloud připojení, který je propojený s Azure AD zastoupení uživatele.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Konfigurace MiCloud pro jednotné přihlašování s Azure AD Connect

V této části kurzu povolit Azure AD single sign-on pro MiCloud připojit na webu Azure Portal a nakonfigurujete účtu MiCloud připojit k povolení jednotného přihlašování pomocí Azure AD.

Konfigurace tlačítka MiCloud připojení s jednotným Přihlašováním pro službu Azure AD, je nejjednodušší otevřít na webu Azure portal a na portálu účtů Mitel vedle sebe. Budete potřebovat ke kopírování některé informace z portálu Azure portal do portálu účtu Mitel a některé z portálu účtu Mitel k webu Azure portal.


1. Otevře se stránka konfigurace v [webu Azure portal](https://portal.azure.com/), postupujte takto:

    a. Na **Mitel připojení** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

    b. V **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **SAML**.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)
    
    Zobrazí se stránka založené na SAML přihlašování.

2. Chcete-li otevřít dialogové okno konfigurace na portálu účtů Mitel, postupujte takto:

    a. Na **systému Phone** nabídky, klikněte na tlačítko **doplňkové funkce**.

    b. Napravo od **Single Sign-On**, klikněte na tlačítko **aktivovat** nebo **nastavení**.
    
    Zobrazí se dialogové okno Připojit jednotné přihlašování – nastavení.
    
3. Vyberte **povolit Single Sign-On** zaškrtávací políčko.
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png)


4. Na webu Azure Portal, klikněte na tlačítko **upravit** ikonu **základní konfiguraci SAML** oddílu.
    ![image](common/edit-urls.png)

    Zobrazí se dialogové okno základní konfiguraci SAML.

5.  Zkopírujte adresu URL **Mitel identifikátor (Entity ID)** pole na portálu účtů Mitel a vložte ho do **identifikátor (Entity ID)** pole na webu Azure Portal.

6. Zkopírujte adresu URL **adresy URL odpovědi (adresa URL služby příjemce kontrolního výrazu)** pole na portálu účtů Mitel a vložte ho do **adresy URL odpovědi (adresa URL služby příjemce kontrolního výrazu)** pole na webu Azure Portal.  
   ![image](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png)

7. V **přihlašovací adresa URL** textového pole, zadejte jeden z následujících adres URL:

    * **https://portal.shoretelsky.com** – Chcete použít na portálu účtů Mitel jako výchozí Mitel aplikace
    * **https://teamwork.shoretel.com** – Chcete použít jako výchozí Mitel aplikace týmovou spolupráci

    **POZNÁMKA:** Výchozí Mitel aplikace je aplikace přistupovat po kliknutí na dlaždici Mitel připojení na přístupovém panelu. Toto je také aplikace přístupné při nastavení testu ze služby Azure AD.

8. Klikněte na tlačítko **Uložit** v **základní konfiguraci SAML** dialogové okno na webu Azure Portal.

9. V **podpisový certifikát SAML** části na **přihlašování na základě SAML** stránce na webu Azure Portal, klikněte na tlačítko **Stáhnout** vedle **certifikát (Base64)** ke stažení **certifikát pro podpis** a uložte ho do počítače.
    ![image](./media/mitel-connect-tutorial/Azure_SigningCert.png)

10. V textovém editoru otevřete soubor podpisového certifikátu, zkopírování všech dat v souboru a vložte data **certifikát pro podpis** pole na portálu účtů Mitel. 
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png)

11. V **instalace Mitel připojit** části na **přihlašování na základě SAML** stránky na webu Azure Portal, postupujte takto:

    a. Zkopírujte adresu URL **přihlašovací adresa URL** pole a vložte ho do **přihlašovací adresa URL** pole na portálu účtů Mitel.

    b. Zkopírujte adresu URL **Azure AD identifikátor** pole a vložte ho do **Entity ID** pole na portálu účtů Mitel.
    ![image](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png)

12. Klikněte na tlačítko **Uložit** na **připojení jednotné přihlašování – nastavení** dialogové okno na portálu účtů Mitel.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory**, klikněte na tlačítko **uživatelé**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Klikněte na tlačítko **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. V dialogovém okně Vlastnosti uživatele proveďte následující kroky:

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte brittasimon @\<doména_společnosti\>.\< rozšíření\>.  
Například, BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Mitel připojení.

1. Na webu Azure Portal, klikněte na tlačítko **podnikové aplikace**a potom klikněte na tlačítko **všechny aplikace**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací klepněte **připojení Mitel**.

    ![Odkaz Mitel připojení v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele**, pak klikněte na tlačítko **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v **uživatelé** seznamu a potom klikněte na **vyberte** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML, vyberte vhodnou roli pro uživatele v seznamu **vybrat roli** dialogového okna a pak klikněte na tlačítko **vyberte** v dolní části obrazovky.

7. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Vytvoření zkušebního uživatele Mitel MiCloud připojení

V této části vytvoříte uživatele ve vašem účtu MiCloud připojení s názvem Britta Simon. Uživatelé musí vytvořit a aktivovat před použitím jednotného přihlašování.

Podrobnosti o přidávání uživatelů na portálu účtů Mitel najdete v tématu [přidání uživatele](https://docs.shoretel.com/connectcloud/Account/Users/AddUser) článku znalostní báze Knowledge Base Mitel.

Vytvoření uživatele ve vašem účtu MiCloud připojení s následujícími podrobnostmi:

  * **Jméno:** Britta Simon

* **Pracovní e-mailové adresy:** `brittasimon@<yourcompanydomain>.<extension>`   
(Příklad: [ brittasimon@contoso.com ](mailto:brittasimon@contoso.com))

* **Uživatelské jméno:** `brittasimon@<yourcompanydomain>.<extension> `  
(Příklad: [ brittasimon@contoso.com ](mailto:brittasimon@contoso.com); uživatelské jméno je obvykle stejné jako uživatele pracovní e-mailové adresy)

**POZNÁMKA:** Uživatelské jméno MiCloud připojení musí být stejný jako e-mailovou adresu uživatele v Azure.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části budete testovat vaše konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.

Když kliknete na dlaždici Mitel připojení na přístupovém panelu, měli byste být automaticky přesměrovaní pro přihlášení k aplikaci připojit MiCloud jste nakonfigurovali jako výchozí v **přihlašovací adresa URL** pole. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
