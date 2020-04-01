---
title: 'Kurz: Integrace služby Azure Active Directory s Everbridge | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Everbridge.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 60463a00c6864bed7b3a18e816ef0143d3573782
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67103257"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Kurz: Integrace Služby Azure Active Directory s Everbridge

V tomto kurzu se dozvíte, jak integrovat Everbridge s Azure Active Directory (Azure AD).
Když integrujete Everbridge s Azure AD, můžete:

* Řízení ve službě Azure AD, který má přístup k Everbridge.
* Umožněte uživatelům, aby se automaticky přihlásili k Everbridge pomocí svých účtů Azure AD. Toto řízení přístupu se nazývá jednotné přihlašování (SSO).
* Spravujte své účty v jednom centrálním umístění pomocí portálu Azure.
Další informace o integraci aplikací softwaru jako služby (SaaS) s Azure AD najdete v tématu [Co je přístup k aplikacím a jednotné přihlašování pomocí Služby Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Everbridge, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Everbridge předplatné, které používá jednotné přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Everbridge podporuje řízení s podněcovaní IDP.

## <a name="add-everbridge-from-the-azure-marketplace"></a>Přidání Everbridge z Azure Marketplace

Pokud chcete nakonfigurovat integraci Everbridge do Azure AD, přidejte Everbridge z Azure Marketplace do seznamu spravovaných aplikací SaaS.

Pokud chcete Everbridge přidat z Azure Marketplace, postupujte takto.

1. Na [webu Azure Portal](https://portal.azure.com)v levém navigačním podokně vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte v horní části dialogového okna možnost **Nová aplikace.**

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Everbridge**. Vyberte **Everbridge** z panelu výsledků a vyberte **Přidat**.

     ![Everbridge v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s Everbridge na základě testovacího uživatele Britta Simon.
Pro jednotné přihlašování k práci, vytvořit vztah propojení mezi uživatelem Azure AD a související uživatele v Everbridge.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí aplikace Everbridge, vyplňte následující stavební bloky:

- [Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on) tak, aby uživatelé mohli tuto funkci používat.
- [Nakonfigurujte Everbridge jako single sign-on portálu Everbridge manager](#configure-everbridge-as-everbridge-manager-portal-single-sign-on) a nakonfigurujte nastavení jednotného přihlášení na straně aplikace.
- [Nakonfigurujte Everbridge jako jednotné přihlášení portálu Everbridge](#configure-everbridge-as-everbridge-member-portal-single-sign-on) a nakonfigurujte nastavení jednotného přihlášení na straně aplikace.
- [Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user) pro testování jednotného přihlašování Azure AD s Brittou Simonovou.
- [Přiřaďte uživateli testu Azure AD,](#assign-the-azure-ad-test-user) aby britta Simon mohla používat jednotné přihlašování Azure AD.
- [Vytvořte testovacího uživatele Everbridge,](#create-an-everbridge-test-user) který bude mít protějšek Britty Simonové v Everbridge, který je propojený s reprezentací Azure AD uživatele.
- [Otestujte jednotné přihlášení](#test-single-sign-on) a ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Everbridge, postupujte takto.

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Everbridge** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlášení** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** vyberte **Upravit,** abyste otevřeli dialogové okno **Základní konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

    >[!NOTE]
    >Nakonfigurujte aplikaci buď jako portál pro *správce, nebo* jako portál pro členy na portálu Azure i na portálu Everbridge.

4. Chcete-li nakonfigurovat aplikaci **Everbridge** jako **portál správce Everbridge**, postupujte v části **Základní konfigurace SAML** takto:

    ![Everbridge domény a adresy URL jednotné přihlašovací informace](common/idp-intiated.png)

    a. Do pole **Identifikátor** zadejte adresu URL, která se řídí vzorem.`https://sso.everbridge.net/<API_Name>`

    b. Do pole **Adresa URL odpovědi** zadejte adresu URL, která se řídí vzorem.`https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnými hodnotami identifikátorů a adres URL pro odpověď. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory Everbridge](mailto:support@everbridge.com). Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Chcete-li nakonfigurovat aplikaci **Everbridge** jako **portál pro členy Everbridge**, postupujte v části **Základní konfigurace SAML** takto:

  * Pokud chcete aplikaci nakonfigurovat v režimu iniciovaném protokolem IDP, postupujte takto:

     ![Everbridge domény a adresy URL jednotné přihlašovací informace pro režim iniciovaný IDP](common/idp-intiated.png)

    a. Do pole **Identifikátor** zadejte adresu URL, která se řídí vzorem.`https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. Do pole **Adresa URL odpovědi** zadejte adresu URL, která se řídí vzorem.`https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Pokud chcete aplikaci nakonfigurovat v režimu iniciovaném službou SP, vyberte **Nastavit další adresy URL** a postupujte takto:

     ![Everbridge domény a adresy URL jednotné přihlašovací informace pro SP-inicioval režim](common/both-signonurl.png)

     a. Do pole **Přihlásit se na adresu URL** zadejte adresu URL, která se řídí vzorem.`https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnými hodnotami identifikátoru, adresy URL pro odpověď a přihlášením k adrese URL. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory Everbridge](mailto:support@everbridge.com). Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** včásti **Podpisový certifikát SAML** vyberte **stáhnout** a stáhněte si xml **metadat federace**. Uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. V části **Nastavit Everbridge** zkopírujte adresy URL, které potřebujete pro své požadavky:

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    - Přihlašovací adresa URL
    - Identifikátor azure reklamy
    - Adresa URL odhlášení

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Konfigurace everbridge jako jednotného přihlášení portálu Everbridge manager

Chcete-li nakonfigurovat přihlašovací služby na **Everbridge** jako aplikaci **portálu správce Everbridge,** postupujte takto.
 
1. V jiném okně webového prohlížeče se přihlaste k Everbridge jako správce.

1. V horní nabídce vyberte kartu **Nastavení.** V části **Zabezpečení**vyberte **Možnost Jednotné přihlašování**.
   
     ![Konfigurace jednotného přihlašování](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. Do pole **Název** zadejte název zprostředkovatele identifikátoru. Příkladem je název vaší společnosti.
   
     b. Do pole **Název rozhraní API** zadejte název rozhraní API.
   
     c. Vyberte **Vybrat soubor,** chcete-li nahrát soubor metadat, který jste stáhli z webu Azure Portal.
   
     d. V **případě umístění identity SAML**je v **yberte Identita v elementu NameIdentifier příkazu Subject**.
   
     e. Do pole **Přihlašovací adresa URL zprostředkovatele identity** vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z portálu Azure.
   
     f. V **případě poskytovatele služeb iniciované vazby požadavků**vyberte možnost Přesměrování **HTTP**.

     g. Vyberte **Uložit**.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Konfigurace everbridge jako jednotného přihlášení portálu Everbridge

Chcete-li nakonfigurovat jednotné přihlašování na **Everbridge** jako **portál pro členy Everbridge**, odešlete stažený **xml metadat federace** týmu [podpory Everbridge](mailto:support@everbridge.com). Toto nastavení nastaví tak, aby bylo připojení s přizasazené k samovazbě SAML správně nastaveno na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Chcete-li vytvořit testovacího uživatele Britta Simon na webu Azure Portal, postupujte takto.

1. Na webu Azure Portal v levém podokně vyberte možnost**Uživatelé služby** >  **Azure Active Directory** > **Všichni uživatelé**.

    ![Odkazy pro uživatele a všechny uživatele](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko Nového uživatele](common/new-user.png)

3. V dialogovém okně **Uživatel** postupujte takto.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. Do pole **Uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension`. Příklad: BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo.** Poznamenejte si hodnotu, která se zobrazí v poli **Heslo.**

    d. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

Umožněte Brittě Simonové používat jednotné přihlašování Azure udělením přístupu k Everbridge.

1. Na portálu Azure vyberte **Podnikové aplikace** > **Všechny aplikace** >**Everbridge**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Everbridge**.

    ![Odkaz Everbridge v seznamu aplikací](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz Uživatelé a skupiny](common/users-groups-blade.png)

4. Vyberte **Přidat uživatele**. V dialogovém okně **Přidat přiřazení** vyberte **Možnost Uživatelé a skupiny**.

    ![Dialogové okno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu uživatelů. V dolní části obrazovky zvolte **Vybrat.**

6. Pokud očekáváte jakoukoli hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu. V dolní části obrazovky zvolte **Vybrat.**

7. V dialogovém okně **Přidat přiřazení** vyberte **Přiřadit**.

### <a name="create-an-everbridge-test-user"></a>Vytvoření testovacího uživatele Everbridge

V této části vytvoříte testovací uživatele Britta Simon v Everbridge. Chcete-li přidat uživatele na platformě Everbridge, spolupracujte s [týmem podpory Everbridge](mailto:support@everbridge.com). Uživatelé musí být vytvořena a aktivována v Everbridge před použitím jednotného přihlášení. 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

Otestujte konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když vyberete dlaždici Everbridge na přístupovém panelu, měli byste být automaticky přihlášeni k účtu Everbridge, pro který nastavíte přiřazovací vztah. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

