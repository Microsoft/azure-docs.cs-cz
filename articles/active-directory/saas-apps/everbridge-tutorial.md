---
title: 'Kurz: Azure Active Directory integrace s EverBridge | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a EverBridge.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 732362ef7099e93697320d8e47180c1207e8cb32
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995892"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Kurz: Azure Active Directory integrace s EverBridge

V tomto kurzu se dozvíte, jak integrovat EverBridge s Azure Active Directory (Azure AD).
Když integrujete EverBridge s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k EverBridge.
* Umožněte uživatelům, aby se automaticky přihlásili k EverBridge pomocí svých účtů Azure AD. Toto řízení přístupu se nazývá jednotné přihlašování (SSO).
* Spravujte své účty v jednom centrálním umístění pomocí Azure Portal.
Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s EverBridge potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné EverBridge, které používá jednotné přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* EverBridge podporuje jednotné přihlašování iniciované IDP.

## <a name="add-everbridge-from-the-azure-marketplace"></a>Přidat EverBridge z Azure Marketplace

Pokud chcete nakonfigurovat integraci EverBridge do služby Azure AD, přidejte EverBridge z Azure Marketplace do seznamu spravovaných aplikací SaaS.

Pokud chcete přidat EverBridge z Azure Marketplace, postupujte podle těchto kroků.

1. V [Azure Portal](https://portal.azure.com)v levém navigačním podokně vyberte **Azure Active Directory**.

    ![Azure Active Directory – tlačítko](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte v horní části dialogového okna možnost **Nová aplikace** .

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **EverBridge**. Z panelu výsledek vyberte **EverBridge** a vyberte **Přidat**.

     ![EverBridge v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí EverBridge na základě testovacího uživatele Britta Simon.
Pokud chcete jednotné přihlašování pracovat, vytvořte vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v EverBridge.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí EverBridge, dokončete následující stavební bloky:

- [Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on) , aby mohli vaši uživatelé používat tuto funkci.
- [Nakonfigurujte EverBridge jako jednotné přihlašování na portále správce EverBridge](#configure-everbridge-as-everbridge-manager-portal-single-sign-on) a nakonfigurujte nastavení jednotného přihlašování na straně aplikace.
- [Nakonfigurujte EverBridge jako jednotné přihlašování pro členské portál EverBridge](#configure-everbridge-as-everbridge-member-portal-single-sign-on) a nakonfigurujte nastavení jednotného přihlašování na straně aplikace.
- [Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user) pro testování jednotného přihlašování Azure AD pomocí Britta Simon.
- [Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user) , aby mohl Britta Simon používat jednotné přihlašování Azure AD.
- [Vytvořte testovacího uživatele EverBridge](#create-an-everbridge-test-user) , který bude mít protějšek Britta Simon v EverBridge, který je propojený s zastoupením uživatele v Azure AD.
- [Otestujte jednotné přihlašování](#test-single-sign-on) a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí EverBridge, postupujte podle těchto kroků.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **EverBridge** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS** , abyste mohli povolit jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** vyberte **Upravit** a otevřete tak základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

    >[!NOTE]
    >Nakonfigurujte aplikaci buď jako portál správce, *nebo* jako členský portál na Azure Portal i na portálu EverBridge.

4. Chcete-li nakonfigurovat aplikaci **EverBridge** jako **portál správce EverBridge**, v **základní části Konfigurace SAML** použijte následující postup:

    ![Informace o jednotném přihlašování v doméně EverBridge a adresách URL](common/idp-intiated.png)

    a. Do pole **identifikátor** zadejte adresu URL, která následuje za vzorem. `https://sso.everbridge.net/<API_Name>`

    b. Do pole **Adresa URL odpovědi** zadejte adresu URL, která následuje za vzorem. `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a hodnotami adresy URL odpovědi. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory EverBridge](mailto:support@everbridge.com). Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Chcete-li nakonfigurovat aplikaci **EverBridge** jako **členský portál EverBridge**, v **základní části Konfigurace SAML** proveďte následující kroky:

  * Pokud chcete nakonfigurovat aplikaci v režimu iniciované IDP, postupujte takto:

     ![Informace jednotného přihlašování EverBridge domény a adres URL pro režim iniciované IDP](common/idp-intiated.png)

    a. Do pole **identifikátor** zadejte adresu URL, která následuje za vzorem. `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. Do pole **Adresa URL odpovědi** zadejte adresu URL, která následuje za vzorem. `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Chcete-li nakonfigurovat aplikaci v režimu iniciované SP, vyberte možnost **nastavit další adresy URL** a postupujte podle tohoto kroku:

     ![Informace o jednotném přihlašování EverBridge domény a adres URL pro režim inicializovaný v režimu SP](common/both-signonurl.png)

     a. Do pole **přihlašovací adresa URL** zadejte adresu URL, která následuje za vzorem. `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a hodnotami adresy URL pro přihlášení. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory EverBridge](mailto:support@everbridge.com). Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte v části **podpisový certifikát SAML** možnost **Stáhnout** a Stáhněte si **XML federačních metadat**. Uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

7. V části **Nastavení EverBridge** zkopírujte adresy URL, které potřebujete pro vaše požadavky:

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    - Přihlašovací adresa URL
    - Identifikátor Azure AD
    - Odhlašovací adresa URL

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Konfigurace EverBridge jako jednotného přihlašování portálu EverBridge Manager

Pokud chcete nakonfigurovat jednotné přihlašování na **EverBridge** jako aplikaci **portálu EverBridge Manager** , postupujte podle těchto kroků.
 
1. V jiném okně webového prohlížeče se přihlaste k EverBridge jako správce.

1. V nabídce v horní části vyberte kartu **Nastavení** . V části **zabezpečení** vyberte **jednotné přihlašování**.
   
     ![Konfigurace jednotného přihlašování](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. Do pole **název** zadejte název poskytovatele identifikátoru. Příkladem je název vaší společnosti.
   
     b. Do pole **název rozhraní API** zadejte název rozhraní API.
   
     c. Vyberte **zvolit soubor** a odešlete soubor metadat, který jste stáhli z Azure Portal.
   
     d. V části **umístění identity SAML** vyberte **Identita v elementu NameIdentifier příkazu Subject**.
   
     e. Do pole **Adresa URL pro přihlášení zprostředkovatele identity** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.
   
     f. U **poskytovatele služby iniciovala vazby žádosti** vyberte **Přesměrování protokolu HTTP**.

     například Vyberte **Uložit**.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Konfigurace EverBridge jako jednotného přihlašování pro členské portály EverBridge

Pokud chcete nakonfigurovat jednotné přihlašování na **EverBridge** jako **portál pro členské EverBridge**, pošlete stažený **kód XML federačních metadat** do [týmu podpory EverBridge](mailto:support@everbridge.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Chcete-li vytvořit testovacího uživatele Britta Simon v Azure Portal, postupujte podle následujících kroků.

1. V Azure Portal v levém podokně vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.

    ![Odkazy uživatelů a všech uživatelů](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. V dialogovém okně **uživatel** postupujte podle těchto kroků.

    ![Uživatel – dialogové okno](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **Uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension`. Příklad: BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** . Zapište hodnotu, která se zobrazí v poli **heslo** .

    d. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

Povolit Britta Simon pro použití jednotného přihlašování pomocí Azure udělením přístupu k EverBridge.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**  > **EverBridge**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **EverBridge**.

    ![Odkaz EverBridge v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz na uživatele a skupiny](common/users-groups-blade.png)

4. Vyberte možnost **Přidat uživatele**. V dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

    ![Dialogové okno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** . Zvolte **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Zvolte **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="create-an-everbridge-test-user"></a>Vytvořit testovacího uživatele v EverBridge

V této části vytvoříte testovacího uživatele Britta Simon v EverBridge. Pokud chcete přidat uživatele na platformě EverBridge, pracujte s [týmem podpory EverBridge](mailto:support@everbridge.com). Než začnete používat jednotné přihlašování, musí se uživatelé vytvořit a aktivovat v EverBridge. 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

Otestujte konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu vyberete dlaždici EverBridge, měli byste se automaticky přihlásit k účtu EverBridge, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)
- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)