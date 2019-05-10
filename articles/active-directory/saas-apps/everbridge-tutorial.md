---
title: 'Kurz: Integrace Azure Active Directory s Everbridge | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Everbridge.
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
ms.openlocfilehash: f8dd11e7fb0b9fda0e0f1c7d3f794f6bfd766cdf
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231468"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Kurz: Integrace Azure Active Directory s Everbridge

V tomto kurzu se dozvíte, jak integrovat Everbridge s Azure Active Directory (Azure AD).
Když integrujete Everbridge s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k Everbridge.
* Umožní uživatelům být automaticky přihlášeni k Everbridge pomocí jejich účtů služby Azure AD. Toto řízení přístupu se nazývá jednotné přihlašování (SSO).
* Správa účtů na jednom místě pomocí webu Azure portal.
Další informace o softwaru jako integraci služby (SaaS) aplikací s Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Everbridge, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Everbridge předplatné, které používá jednotné přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Everbridge podporuje jednotné přihlašování zahájené pomocí IDP.

## <a name="add-everbridge-from-the-azure-marketplace"></a>Přidat Everbridge na webu Azure Marketplace

Ke konfiguraci integrace Everbridge do služby Azure AD, přidejte Everbridge z Azure Marketplace si na seznam spravovaných aplikací SaaS.

Chcete-li přidat Everbridge z Azure Marketplace, postupujte takto.

1. V [webu Azure portal](https://portal.azure.com), v levém navigačním podokně vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, **novou aplikaci** v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Everbridge**. Vyberte **Everbridge** z výsledku panelu a vyberte **přidat**.

     ![Everbridge v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Everbridge podle testovacího uživatele Britta Simon.
Pro jednotné přihlašování pro práci vytvořit vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatelem v Everbridge.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Everbridge, proveďte následující stavebních bloků:

- [Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on) aby uživatelé mohli tuto funkci používat.
- [Konfigurace Everbridge jako Everbridge správce portálu jednotného přihlašování](#configure-everbridge-as-everbridge-manager-portal-single-sign-on) ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
- [Konfigurace Everbridge jako Everbridge člen portálu jednotného přihlašování](#configure-everbridge-as-everbridge-member-portal-single-sign-on) ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
- [Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user) k otestování služby Azure AD jednotné přihlašování s Britta Simon.
- [Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user) umožňující Britta Simon používat Azure AD jednotného přihlašování.
- [Vytvořit testovacího uživatele Everbridge](#create-an-everbridge-test-user) mít protějšek Britta Simon Everbridge, který je propojený s Azure AD zastoupení uživatele.
- [Otestovat jednotné přihlašování](#test-single-sign-on) ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Everbridge, postupujte podle těchto kroků.

1. V [webu Azure portal](https://portal.azure.com/)na **Everbridge** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V **vybrat jedinou metodu přihlašování** dialogové okno, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** stránce **upravit** otevřít **základní konfiguraci SAML** dialogové okno.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

    >[!NOTE]
    >Konfigurace aplikace buď jako portál správce *nebo* jako člen portál na webu Azure portal a portálu Everbridge.

4. Ke konfiguraci **Everbridge** aplikace jako **portálu správce Everbridge**v **základní konfiguraci SAML** , postupujte podle těchto kroků:

    ![Everbridge domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** zadejte adresu URL, která odpovídá vzoru `https://sso.everbridge.net/<API_Name>`

    b. V **adresy URL odpovědi** zadejte adresu URL, která odpovídá vzoru `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečnými hodnotami identifikátoru a adresa URL odpovědi. K získání těchto hodnot, obraťte se [tým podpory Everbridge](mailto:support@everbridge.com). Také se můžete vrátit k tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Ke konfiguraci **Everbridge** aplikace jako **Everbridge člen portál**v **základní konfiguraci SAML** , postupujte podle těchto kroků:

  * Pokud chcete nakonfigurovat aplikace v režimu zahájené pomocí IDP, postupujte podle těchto kroků:

     ![Everbridge domény a adresy URL jednotného přihlašování informace týkající se režimu zahájené pomocí IDP](common/idp-intiated.png)

    a. V **identifikátor** zadejte adresu URL, která odpovídá vzoru `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. V **adresy URL odpovědi** zadejte adresu URL, která odpovídá vzoru `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Pokud chcete nakonfigurovat aplikace v režimu iniciovaného Zprostředkovatelem přihlašování, vyberte **nastavit další adresy URL** a postupujte podle tohoto kroku:

     ![Everbridge domény a adresy URL jednotného přihlašování informace týkající se režimu iniciovaného Zprostředkovatelem přihlašování](common/both-signonurl.png)

     a. V **přihlašovací adresa URL** zadejte adresu URL, která odpovídá vzoru `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizovat s identifikátorem skutečná adresa URL odpovědi a přihlaste se na hodnoty adresy URL. K získání těchto hodnot, obraťte se [tým podpory Everbridge](mailto:support@everbridge.com). Také se můžete vrátit k tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** vyberte **Stáhnout** ke stažení **kód XML metadat federace** . Uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. V **nastavení Everbridge** části, kopírování adresy URL je nutné pro vaše požadavky:

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    - Přihlašovací adresa URL
    - Identifikátor Azure AD
    - Adresa URL – odhlášení

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Konfigurace Everbridge jako Everbridge správce portálu jednotného přihlašování

Konfigurace jednotného přihlašování na **Everbridge** jako **portálu správce Everbridge** aplikace, postupujte podle těchto kroků.
 
1. V okně jiné webové prohlížeče Přihlaste se k Everbridge jako správce.

1. V nabídce v horní části vyberte **nastavení** kartu. V části **zabezpečení**vyberte **Single Sign-On**.
   
     ![Konfigurace jednotného přihlašování](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. V **název** zadejte název identifikátoru zprostředkovatele. Příkladem je název vaší společnosti.
   
     b. V **název rozhraní API** zadejte název rozhraní API.
   
     c. Vyberte **zvolit soubor** nahrát soubor metadat, který jste si stáhli z webu Azure portal.
   
     d. Pro **umístění Identity SAML**vyberte **identita je v elementu NameIdentifier příkazu subjektu**.
   
     e. V **přihlašovací adresa URL zprostředkovatele Identity** pole, vložte **přihlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.
   
     f. Pro **poskytovatele služeb inicioval žádost o vytvoření vazby**vyberte **přesměrování protokolu HTTP**.

     g. Vyberte **Uložit**.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Konfigurace Everbridge jako Everbridge člen portálu jednotného přihlašování

Ke konfiguraci jednotného přihlašování na **Everbridge** jako **Everbridge člen portál**, odeslat na stažený **kód XML metadat federace** k [Everbridge tým podpory](mailto:support@everbridge.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Chcete-li vytvořit testovacího uživatele Britta Simon na webu Azure Portal, postupujte takto.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory** > **uživatelé** > **všichni uživatelé**.

    ![Uživatelé a všechna propojení uživatelů](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. V **uživatele** dialogové okno pole, postupujte podle těchto kroků.

    ![Dialogové okno uživatelského](common/user-properties.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension`. Příklad: BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtávací políčko. Zapište si hodnotu, která se zobrazí v **heslo** pole.

    d. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

Povolte Britta Simon k udělení přístupu k Everbridge použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace** > **všechny aplikace** >**Everbridge**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Everbridge**.

    ![Everbridge odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Propojení uživatelů a skupin](common/users-groups-blade.png)

4. Vyberte **přidat uživatele**. V **přidat přiřazení** dialogu **uživatelů a skupin**.

    ![Přidat přiřazení – dialogové okno](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogu **Britta Simon** v seznamu uživatelů. Zvolte **vyberte** v dolní části obrazovky.

6. Pokud očekáváte, že všechny role hodnotu v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu. Zvolte **vyberte** v dolní části obrazovky.

7. V **přidat přiřazení** dialogu **přiřadit**.

### <a name="create-an-everbridge-test-user"></a>Vytvořit testovacího uživatele Everbridge

V této části vytvoříte Britta Simon v Everbridge testovacího uživatele. Přidat uživatele na platformě Everbridge, pracovat [tým podpory Everbridge](mailto:support@everbridge.com). Uživatelé musí vytvořit a aktivovat v Everbridge, než použití jednotného přihlašování. 

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování 

Testování Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Při výběru dlaždice Everbridge na přístupovém panelu, vám by měl být automaticky přihlášeni Everbridge účet, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

