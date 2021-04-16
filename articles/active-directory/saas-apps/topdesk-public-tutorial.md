---
title: 'Kurz: Azure Active Directory integrace s TOPdesk-Public | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TOPdesk-Public.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 5d16fd87b01db69d3f55e22aad573b7847b9048c
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518025"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Kurz: Azure Active Directory integrace s TOPdesk – Public

V tomto kurzu se dozvíte, jak integrovat TOPdesk-Public s Azure Active Directory (Azure AD). Když integrujete TOPdesk-Public s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k TOPdesk veřejnosti.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k TOPdesk svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* TOPdesk – veřejné předplatné s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* TOPdesk – Public podporuje jednotné přihlašování iniciované v **SP** .

## <a name="add-topdesk---public-from-the-gallery"></a>Přidání TOPdesk-Public z Galerie

Pokud chcete nakonfigurovat integraci TOPdesk-Public do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat TOPdesk – Public z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **TopDesk-Public** .
1. Z panelu výsledků vyberte **TopDesk-Public** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-topdesk---public"></a>Konfigurace a testování jednotného přihlašování Azure AD pro TOPdesk – Public

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí TOPdesk-Public pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v TOPdesk – Public.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí TOPdesk-Public, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace jednotného přihlašování TopDesk](#configure-topdesk---public-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte TopDesk-Public Test User](#create-topdesk---public-test-user)** -to, abyste měli protějšek B. Simon v TopDesk – Public, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **TopDesk** vyhledejte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4.  Pokud máte **soubor metadat poskytovatele služeb** v **základní části Konfigurace SAML** , proveďte následující kroky:

    >[!NOTE]
    >**Soubor metadat poskytovatele služeb** získáte z oddílu **Konfigurace jednotného přihlašování TopDesk** , který se vysvětluje později v tomto kurzu.

    a. Klikněte na **nahrát soubor metadat**.
    
    ![Nahrát soubor metadat](common/upload-metadata.png)

    b. Kliknutím na **logo složky** vyberte soubor metadat a klikněte na **nahrát**.

    ![zvolit soubor metadat](common/browse-upload-metadata.png)

    c. Po úspěšném nahrání souboru metadat se hodnoty **adresy URL** pro **identifikátor** a odpověď získají automaticky v základní části Konfigurace SAML.

    d. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.topdesk.net`

    e. Do textového pole **Adresa URL identifikátoru** zadejte adresu URL metadat TopDesk, kterou můžete načíst z konfigurace TopDesk. Měl by používat následující vzor: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`
    
    f. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > Pokud se hodnoty **adresy URL** pro **identifikátor** a odpověď nezískají automaticky, je nutné je zadat ručně. V případě identifikátoru postupujte podle vzoru uvedeného výše a získejte adresu URL odpovědi z oddílu **Konfigurace jednotného přihlašování TopDesk** , který je vysvětlen dále v tomto kurzu. Hodnota **přihlašovací adresy URL** není skutečná, takže je potřeba aktualizovat hodnotu skutečnou adresou Sign-On. Kontaktujte [TopDesk – tým podpory veřejného klienta](https://help.topdesk.com/saas/enterprise/user/) , který získá hodnotu. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení TopDesk-Public** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Vytvoření testovacího uživatele Azure AD 

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k TOPdesk – Public.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **TopDesk-Public**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name=&quot;configure-topdesk---public-sso&quot;></a>Konfigurace jednotného přihlašování TOPdesk

1. Přihlaste se k vaší veřejné firemní síti **TopDesk** jako správce.

2. V nabídce **TopDesk** klikněte na **Nastavení**.
   
    ![Nastavení](./media/topdesk-public-tutorial/menu.png &quot;Nastavení")

3. Klikněte na tlačítko **Nastavení přihlášení**.
   
    ![Nastavení přihlášení](./media/topdesk-public-tutorial/login.png "Nastavení přihlášení")

4. Rozbalte nabídku **Nastavení přihlášení** a pak klikněte na **Obecné**.
   
    ![Obecné nastavení](./media/topdesk-public-tutorial/general.png "Obecná nastavení")

5. V části **Public** v části konfigurace **přihlášení SAML** proveďte následující kroky:
   
    ![Technické nastavení](./media/topdesk-public-tutorial/public.png "Technické nastavení")
   
    a. Klikněte na tlačítko **Stáhnout** a Stáhněte si soubor veřejné metadat a uložte ho místně na svém počítači.
   
    b. Otevřete stažený soubor metadat a vyhledejte uzel **AssertionConsumerService** .

    ![AssertionConsumerService](./media/topdesk-public-tutorial/service.png "AssertionConsumerService")
   
    c. Zkopírujte hodnotu **AssertionConsumerService** a vložte tuto hodnotu do textového pole **Adresa URL odpovědi** v **základní části Konfigurace SAML** .      
   
6. Chcete-li vytvořit soubor certifikátu, proveďte následující kroky:
    
    ![Certifikát](./media/topdesk-public-tutorial/certificate-file.png "Certifikát")
    
    a. Otevřete stažený soubor metadat z Azure Portal.
    
    b. Rozbalte uzel **RoleDescriptor** , který má **atribut xsi: Type** dodaný **: ApplicationServiceType**.
    
    c. Zkopírujte hodnotu uzlu **certifikátu x509** .
    
    d. Uložte zkopírovanou hodnotu **certifikátu x509** lokálně na svém počítači do souboru.

7. V části **Public** klikněte na **Přidat**.
    
    ![Přihlášení SAML](./media/topdesk-public-tutorial/add.png "Přihlášení SAML")

8. Na stránce **Průvodce konfigurací SAML** proveďte následující kroky:
    
    ![Pomocník pro konfiguraci SAML](./media/topdesk-public-tutorial/configuration.png "Pomocník pro konfiguraci SAML")
    
    a. Pokud chcete nahrát stažený soubor metadat z Azure Portal, klikněte v části **federační metadata** na **Procházet**.

    b. Pokud chcete nahrát soubor certifikátu, klikněte v části **certifikát (RSA)** na **Procházet**.

    c. Pokud chcete nahrát soubor loga, který jste dostali z týmu podpory TOPdesk, klikněte v části **ikona loga** na **Procházet**.

    d. Do textového pole **atributu uživatelského jména** zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    e. Do textového pole **Zobrazovaný název** zadejte název konfigurace.

    f. Klikněte na **Uložit**.

### <a name="create-topdesk---public-test-user"></a>Vytvořit TOPdesk – veřejný testovací uživatel

Aby se uživatelé Azure AD mohli přihlašovat k TOPdesk veřejnému, musí se zřídit v TOPdesk – Public. V případě TOPdesk-Public je zřizování ručním úkolem.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Při konfiguraci zřizování uživatelů proveďte následující kroky:

1. Přihlaste se k vaší veřejné lokalitě společnosti **TopDesk** jako správce.

2. V nabídce v horní části klikněte na **TopDesk \> nové \> soubory podpory \> osoba**.
   
    ![Person (Osoba)](./media/topdesk-public-tutorial/files.png "Person (Osoba)")

3. V dialogovém okně Nová osoba proveďte následující kroky:
   
    ![Nová osoba](./media/topdesk-public-tutorial/new.png "Nová osoba")
   
    a. Klikněte na kartu Obecné.

    b. Do textového pole **příjmení** zadejte příjmení uživatele, jako je Simon.
 
    c. Vyberte **lokalitu** pro tento účet.
 
    d. Klikněte na **Uložit**.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné TOPdesk nástroje pro vytváření veřejných uživatelských účtů nebo rozhraní API poskytovaná TOPdesk-Public.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlašování TOPdesk, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro veřejné přihlašování (TOPdesk) přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici TOPdesk-Public v části Moje aplikace, přesměruje se na přihlašovací adresu URL pro veřejné přihlašování TOPdesk. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování TOPdesk můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
