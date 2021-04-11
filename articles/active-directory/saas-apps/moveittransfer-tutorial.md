---
title: 'Kurz: Azure Active Directory integrace s přenosem MOVEit – integrace služby Azure AD | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a přenosem MOVEit – integraci Azure AD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 73f378bb0f71df4df3731a3ef2a1fdb7f8abb4aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653030"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Kurz: Azure Active Directory integrace s přenosem MOVEit – Integrace Azure AD

V tomto kurzu se dozvíte, jak integrovat MOVEit přenos – Azure AD Integration with Azure Active Directory (Azure AD). Když integrujete MOVEit přenos – Integrace Azure AD s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k MOVEit přenosu – Azure AD Integration.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k MOVEit přenosu – Integrace Azure AD s jejich účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* MOVEit Transfer – odběr služby Azure AD Integration Single Signing (SSO) s podporou jednotného přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* MOVEit Transfer – integrace služby Azure AD podporuje jednotné přihlašování se spuštěnou službou **SP**

## <a name="add-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Přidání přenosu MOVEit – Integrace Azure AD z Galerie

Pokud chcete nakonfigurovat integraci MOVEit přenosu – Integrace Azure AD do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat MOVEit přenos – Azure AD Integration z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **MOVEit Transfer – Azure AD Integration** .
1. Vyberte **MOVEit Transfer – Integrace Azure AD** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-moveit-transfer---azure-ad-integration"></a>Konfigurace a testování jednotného přihlašování Azure AD pro MOVEit přenos – Azure AD Integration

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s MOVEit přenosem – integrací Azure AD s použitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v integraci služby MOVEit – Azure AD.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí přenosu MOVEit – integrace služby Azure AD, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte MOVEit Transfer – jednotné přihlašování Azure AD](#configure-moveit-transfer---azure-ad-integration-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvoření MOVEit transferu – testovací uživatel pro integraci se službou Azure AD](#create-moveit-transfer---azure-ad-integration-test-user)** – Pokud má protějšek B. Simon v MOVEit přenosu – integrace služby Azure AD, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce **migrace MOVEit – Azure AD Integration** Application Integration, najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud máte **soubor metadat poskytovatele služeb** v **základní části Konfigurace SAML** , proveďte následující kroky:

    a. Klikněte na **nahrát soubor metadat**.

    ![Nahrát soubor metadat](common/upload-metadata.png)

    b. Kliknutím na **logo složky** vyberte soubor metadat a klikněte na **nahrát**.

    ![zvolit soubor metadat](common/browse-upload-metadata.png)

    c. Po úspěšném nahrání souboru metadat se hodnota **Adresa URL** pro **identifikátor** a odpověď automaticky naplní v **základní části Konfigurace SAML** :

    ![MOVEit Transfer – informace o jednotném přihlašování v doméně Integrace Azure AD a adresách URL](common/sp-identifier-reply.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://contoso.com`

    > [!NOTE]
    > Hodnota **přihlašovací adresy URL** není reálné číslo. Aktualizujte hodnotu skutečnou adresou Sign-On. Kontaktujte [MOVEit Transfer – tým podpory klienta Azure AD Integration](https://community.ipswitch.com/s/support) pro získání hodnoty. **Soubor metadat poskytovatele služby** si můžete stáhnout z **adresy URL metadat poskytovatele služby** , která je vysvětlená dále v části **Konfigurace MOVEit přenosu – jednotné přihlašování Azure AD** v kurzu. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **nastavení přenosu MOVEit – integrace služby Azure AD** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k integraci MOVEit – Azure AD.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **MOVEit přenos – integrace služby Azure AD**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="configure-moveit-transfer---azure-ad-integration-sso"></a>Konfigurace přenosu MOVEit – jednotné přihlašování Azure AD

1. Přihlaste se ke klientovi MOVEit Transfer jako správce.

2. V levém navigačním podokně klikněte na **Nastavení**.

    ![Oddíl nastavení na straně aplikace](./media/moveittransfer-tutorial/settings.png)

3. Klikněte na odkaz na **jeden jednotného** , který se nachází v **zásadách zabezpečení – > ověření uživatele**.

    ![Zásady zabezpečení na straně aplikace](./media/moveittransfer-tutorial/sso.png)

4. Pro stažení dokumentu metadat klikněte na odkaz adresa URL metadat.

    ![Adresa URL metadat poskytovatele služby](./media/moveittransfer-tutorial/metadata.png)
    
   * Ověřte, že **entityID** odpovídá **identifikátoru** v části **základní konfigurace SAML** .
   * V části **základní konfigurace SAML** ověřte, že adresa URL umístění **AssertionConsumerService** odpovídá **adrese URL odpovědi** .
    
     ![Konfigurovat jednu Sign-On na straně aplikace](./media/moveittransfer-tutorial/xml.png)

5. Klikněte na tlačítko **Přidat zprostředkovatele identity** a přidejte nového federovaného zprostředkovatele identity.

    ![Přidat zprostředkovatele identity](./media/moveittransfer-tutorial/idp.png)

6. Klikněte na **Procházet...** a vyberte soubor metadat, který jste stáhli z Azure Portal, a pak kliknutím na **Přidat zprostředkovatele identity** nahrajte stažený soubor.

    ![Zprostředkovatel identity SAML](./media/moveittransfer-tutorial/saml.png)

7. Vyberte možnost **Ano**, pokud je **povolená** na stránce **Upravit nastavení zprostředkovatele federovaných identit...** a klikněte na **Uložit**.

    ![Nastavení zprostředkovatele federovaných identit](./media/moveittransfer-tutorial/save.png)

8. Na stránce **Upravit uživatelská nastavení zprostředkovatele federovaných identit** proveďte následující akce:
    
    ![Upravit nastavení federovaného zprostředkovatele identity](./media/moveittransfer-tutorial/attributes.png)
    
    a. Jako **přihlašovací jméno** vyberte **SAML NameId** .
    
    b. Vyberte možnost **jiný** jako **úplný název** a do textového pole **název atributu** zadejte hodnotu: `http://schemas.microsoft.com/identity/claims/displayname` .
    
    c. Vyberte možnost **jiné** jako **e-mail** a v textovém poli **název atributu** zadejte hodnotu: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .
    
    d. V jednotného vyberte **Ano** jako **automaticky vytvořit účet**.
    
    e. Klikněte na tlačítko **Uložit** .

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Vytvoření MOVEit transferu – testovací uživatel pro integraci Azure AD

Cílem této části je vytvořit uživatele s názvem Britta Simon v článku věnovaném přenosu MOVEit – Integrace Azure AD. MOVEit Transfer – integrace služby Azure AD podporuje zřizování za běhu, které jste povolili. V této části není žádná položka akce. Při pokusu o přístup k MOVEit přenosu – Azure AD Pokud ještě neexistuje, vytvoří se nový uživatel.

>[!NOTE]
>Pokud potřebujete ručně vytvořit uživatele, musíte se obrátit na [tým podpory služby MOVEit Transfer – Azure AD Integration Client](https://community.ipswitch.com/s/support).

### <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na MOVEit přenos – adresa URL pro integraci Azure AD, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlašování k integraci Azure AD a spustit tok přihlašovacích údajů přímo v MOVEit transferu

* Můžete použít aplikaci Microsoft moje aplikace. Po kliknutí na dlaždici MOVEit Transfer – integrace služby Azure AD v části Moje aplikace byste měli být automaticky přihlášeni k integraci MOVEit Transfer – Azure AD, pro kterou jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete MOVEit Transfer – integraci Azure AD, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).