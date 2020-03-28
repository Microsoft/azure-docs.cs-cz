---
title: 'Kurz: Integrace služby Azure Active Directory s Mitel Connect | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Mitel Connect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: 30a4dcbd15d8a47d99b3a61879a7e61938d3b133
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160532"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>Kurz: Integrace Azure Active Directory s Mitel MiCloud Connect

V tomto kurzu se dozvíte, jak integrovat Mitel MiCloud Connect s Azure Active Directory (Azure AD). Integrace MiCloud Connect s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, kdo má přístup k aplikacím MiCloud Connect pomocí jejich podnikových přihlašovacích údajů.
* Uživatelům vašeho účtu můžete povolit automatické přihlášení ke službě MiCloud Connect (jednotné přihlášení) pomocí jejich účtů Azure AD.


Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí služby MiCloud Connect, potřebujete následující položky:

* Předplatné Azure AD

  Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Účet Mitel MiCloud Connect

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD (SSO).

* Mitel Connect **podporuje** sp inicioval SSO

## <a name="adding-mitel-connect-from-the-gallery"></a>Přidání Mitel Connect z galerie

Pokud chcete nakonfigurovat integraci Mitel Connect do Azure AD, musíte přidat Mitel Connect z galerie do seznamu spravovaných aplikací SaaS na webu Azure Portal.

**Chcete-li přidat Mitel Connect z galerie, postupujte takto:**

1. Na webu **[Azure Portal](https://portal.azure.com)** klikněte na levém navigačním panelu na **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Klepněte na **položku Podnikové aplikace** a potom na **položku Všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Klepněte na tlačítko **Nová aplikace**.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Zadejte **Mitel Connect** do vyhledávacího pole, klepněte na **Mitel Connect** z panelu výsledků a potom klepněte na **Přidat**.

     ![Mitel Connect v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí služby MiCloud Connect na základě testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v MiCloud Connect.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby MiCloud Connect, musíte provést následující kroky:

1. **[Konfigurace MiCloud Connect pro spřiis u Azure AD](#configure-micloud-connect-for-sso-with-azure-ad)** – chcete-li uživatelům umožnit používat tuto funkci a konfigurovat nastavení přihlašování na straně aplikace.
2. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
3. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
4. **[Vytvořte testovacího uživatele Mitel MiCloud Connect](#create-a-mitel-micloud-connect-test-user)** – chcete-li mít na účtu MiCloud Connect protějšek Britty Simonové, který je propojený s reprezentací Azure AD uživatele.
5. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Konfigurace připojení MiCloud pro službu Přizpůsobované ho s azure ad

V této části povolíte jednotné přihlašování Azure AD pro MiCloud Connect na webu Azure Portal a nakonfigurujete svůj účet MiCloud Connect tak, aby umožňoval jednotné přihlašování pomocí Azure AD.

Chcete-li nakonfigurovat MiCloud Connect s přiřazovacím služeb pro Azure AD, je nejjednodušší otevřít portál Azure a portál účtu Mitel vedle sebe. Budete muset zkopírovat některé informace z portálu Azure na portál účtu Mitel a některé z portálu účtu Mitel na portál Azure.


1. Pokud chcete otevřít stránku konfigurace na [webu Azure Portal](https://portal.azure.com/), postupujte takto:

    a. Na stránce integrace aplikací **Mitel Connect** klikněte na **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

    b. V **dialogovém okně Vybrat metodu jednotného přihlášení** klepněte na tlačítko **SAML**.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)
    
    Zobrazí se přihlašovací stránka založená na SAML.

2. Chcete-li otevřít dialogové okno konfigurace na portálu účtu Mitel, postupujte takto:

    a. V nabídce **Telefonní systém** klepněte na **položku Funkce doplňků**.

    b. Vpravo od **položky Jednotné přihlašování**klepněte na **tlačítko Aktivovat** nebo **Nastavení**.
    
    Zobrazí se dialogové okno Připojit nastavení jednotného přihlášení.
    
3. Zaškrtněte políčko **Povolit jednotné přihlašování.**
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png)


4. Na webu Azure Portal klikněte na ikonu **Upravit** v části **Základní konfigurace SAML.**
    ![image](common/edit-urls.png)

    Zobrazí se dialogové okno Základní konfigurace SAML.

5.  Zkopírujte adresu URL z pole **Identifikátor Mitel (ID entity)** na portálu účtu Mitel a vložte ji do pole **Identifikátor (ID entity)** na webu Azure Portal.

6. Zkopírujte adresu URL z pole **Adresa URL odpovědi (adresa URL služby Assertion Consumer Service)** na portálu účtu Mitel a vložte ji do pole **Adresa URL pro odpověď (Adresa URL služby Assertion Consumer Service)** na webu Azure Portal.  
   ![image](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png)

7. Do textového pole **Adresa URL přihlášení** zadejte jednu z následujících adres URL:

    * **https://portal.shoretelsky.com**- používat portál účtu Mitel jako výchozí aplikaci Mitel
    * **https://teamwork.shoretel.com**- používat týmovou práci jako výchozí aplikaci Mitel

    **Poznámka:** Výchozí aplikace Mitel je aplikace přístupná, když uživatel klikne na dlaždici Mitel Connect na přístupovém panelu. Toto je také aplikace, ke které se přistupuje při provádění nastavení testu z Azure AD.

8. Klikněte na **Uložit** v dialogovém okně **Základní konfigurace SAML** na webu Azure Portal.

9. V části **Podpisový certifikát SAML** na přihlašovací stránce **založené na saml na** webu Azure Portal klikněte na **Stáhnout** vedle **certifikátu (Base64)** a stáhněte **podpisový certifikát** a uložte ho do počítače.
    ![image](./media/mitel-connect-tutorial/Azure_SigningCert.png)

10. Otevřete soubor podpisového certifikátu v textovém editoru, zkopírujte všechna data v souboru a vložte data do pole **Podpisový certifikát** na portálu účtu Mitel. 
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png)

11. V části **Setup Mitel Connect** na přihlašovací stránce webu Azure Portal **založené na SAML** postupujte takto:

    a. Zkopírujte adresu URL z pole **Přihlašovací adresa URL** a vložte ji do pole Přihlašovací adresa **URL** na portálu účtu Mitel.

    b. Zkopírujte adresu URL z pole **Identifikátor Azure AD** a vložte ji do pole **ID entity** na portálu účtu Mitel.
    ![image](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png)

12. V dialogovém okně **Připojit jednotné přihlašování** na portálu Mitel klikněte na **Uložit.**

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal klikněte v levém podokně na **Azure Active Directory**, klikněte na **Uživatelé**a potom klikněte na **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky klikněte na **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. V dialogovém okně Vlastnosti uživatele proveďte následující kroky:

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. Do pole **Uživatelské jméno** \<zadejte\>brittasimon@ firemní doménu . \<prodloužení\>.  
Například, BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli **Heslo.**

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k Mitel Connect.

1. Na portálu Azure klikněte na **Podnikové aplikace**a potom klikněte na **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací klepněte na **položku Mitel Connect**.

    ![Odkaz Mitel Connect v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo klikněte na **Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. V dialogovém okně **Přidat přiřazení** klikněte na **Přidat uživatele**a potom klikněte na **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu **Uživatelé** a v dolní části obrazovky klikněte na **Vybrat.**

6. Pokud v kontrolním výrazu SAML očekáváte libovolnou hodnotu role, vyberte příslušnou roli pro uživatele ze seznamu v dialogovém okně **Vybrat roli** a v dolní části obrazovky klepněte na **tlačítko Vybrat.**

7. V dialogovém okně **Přidat přiřazení** klepněte na **tlačítko Přiřadit**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Vytvoření testovacího uživatele Mitel MiCloud Connect

V této části vytvoříte uživatele s názvem Britta Simon na vašem účtu MiCloud Connect. Uživatelé musí být vytvořena a aktivována před použitím jednotného přihlášení.

Podrobnosti o přidávání uživatelů na portálu účtu Mitel najdete v článku [Přidání uživatele](https://docs.shoretel.com/connectcloud/Account/Users/AddUser) ve znalostní bázi Mitel.

Vytvořte si uživatele na svém účtu MiCloud Connect s následujícími podrobnostmi:

  * **Název:** Britta Simonová

* **Firemní e-mailová adresa:**`brittasimon@<yourcompanydomain>.<extension>`   
(Příklad: [brittasimon@contoso.com](mailto:brittasimon@contoso.com))

* **Uživatelské jméno:**`brittasimon@<yourcompanydomain>.<extension>`  
(Příklad: [brittasimon@contoso.com](mailto:brittasimon@contoso.com); uživatelské jméno uživatele je obvykle stejné jako firemní e-mailová adresa uživatele)

**POZNÁMKA:** Uživatelské jméno uživatele MiCloud Connect musí být shodné s e-mailovou adresou uživatele v Azure.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Mitel Connect na přístupovém panelu, měli byste být automaticky přesměrováni na přihlášení k aplikaci MiCloud Connect, kterou jste nakonfigurovali jako výchozí v poli **Přihlašovací adresa URL.** Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
