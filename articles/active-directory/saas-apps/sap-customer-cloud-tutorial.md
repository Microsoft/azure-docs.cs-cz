---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s SAP cloudem pro zákazníky | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP cloudem pro zákazníky.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 60381c7d8c452277b53e1af67ae7fc85349521c8
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735670"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-for-customer"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s SAP cloudem pro zákazníky

V tomto kurzu se dozvíte, jak integrovat SAP Cloud pro zákazníky s Azure Active Directory (Azure AD). Když integruje SAP Cloud pro zákazníky s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup ke službě SAP Cloud pro zákazníky.
* Umožněte uživatelům, aby se pro zákazníky s účty Azure AD automaticky přihlásili ke službě SAP Cloud.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.


## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Cloud SAP pro předplatné s jednotným přihlašováním (SSO) s podporou jednotného přihlašování pro zákazníky.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SAP Cloud pro zákazníky podporuje jednotné přihlašování na základě služby **SP**

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Přidání cloudu SAP pro zákazníky z Galerie

Pokud chcete nakonfigurovat integraci služby SAP Cloud for Customer do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat pro zákazníka SAP Cloud z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **SAP Cloud for Customer** .
1. Z panelu výsledků vyberte **SAP Cloud for Customer** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-for-customer"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro SAP v cloudu pro zákazníky

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí SAP cloudu pro zákazníky pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v cloudu SAP pro zákazníky.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí SAP cloudu pro zákazníky, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Cloud SAP pro zákazníka SSO](#configure-sap-cloud-for-customer-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte si testovacího uživatele pro SAP Cloud pro zákazníka](#create-sap-cloud-for-customer-test-user)** – abyste měli protějšek B. Simon ve službě SAP Cloud pro zákazníka, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **SAP pro zákazníky** vyhledejte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<server name>.crm.ondemand.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pro získání těchto hodnot se obraťte na [tým podpory zákaznických klientů SAP](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aplikace SAP Cloud for Customer očekává kontrolní výrazy SAML v konkrétním formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na tlačítko **Upravit** ikonu otevřete dialogové okno atributy uživatele.

    ![Snímek obrazovky, který zobrazuje dialogové okno atributy uživatele se zvolenou ikonou upravit](common/edit-attribute.png)

1. V části **atributy uživatele** v dialogovém okně **atributy uživatele & deklarací** proveďte následující kroky:

    a. Kliknutím na **ikonu Upravit** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![Snímek obrazovky, který zobrazuje "atributy uživatele & deklarací identity" s vybranou ikonou "Upravit".](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Jako **zdroj** vyberte **transformovat** .

    c. V seznamu **transformace** vyberte **ExtractMailPrefix ()**.

    d. V seznamu **parametr 1** vyberte atribut uživatele, který chcete použít pro vaši implementaci.
    Například pokud chcete použít ČísloZaměstnance jako jedinečný identifikátor uživatele a uloženou hodnotu atributu v ExtensionAttribute2, vyberte User. ExtensionAttribute2.

    e. Klikněte na **Uložit**.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **nastavení SAP pro Cloud pro zákazníka** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup ke službě SAP Cloud pro zákazníky.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **SAP Cloud pro zákazníka**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-sap-cloud-for-customer-sso"></a>Konfigurace cloudu SAP pro jednotné přihlašování zákazníka

1. Otevřete nové okno webového prohlížeče a přihlaste se ke svému cloudu SAP pro zákazníky společnosti jako správce.

2. V levé části nabídky klikněte na **poskytovatelé identit**  >  **podnikové identity**  >  **Přidat** a v automaticky otevíraném okně Přidat název zprostředkovatele identity, jako je **Azure AD**, klikněte na **Uložit** a pak klikněte na **Konfigurace SAML 2,0**.

    ![Snímek obrazovky zobrazující stránku zprostředkovatelé identit se zvýrazněným dialogovým oknem přidat zprostředkovatele identity a vybraným tlačítkem Uložit.](./media/sap-customer-cloud-tutorial/configure01.png)

3. V části **Konfigurace SAML 2,0** proveďte následující kroky:

    ![Snímek obrazovky zobrazující konfiguraci S A M L 2,0 s vybraným tlačítkem Procházet.](./media/sap-customer-cloud-tutorial/configure02.png)

    a. Klikněte na **Procházet** a nahrajte soubor XML federačních metadat, který jste stáhli z Azure Portal.

    b. Po úspěšném nahrání souboru XML se automaticky vyplní následující hodnoty a pak klikněte na **Uložit**.

### <a name="create-sap-cloud-for-customer-test-user"></a>Vytvoření cloudu SAP pro testovací uživatele pro zákazníky

Aby se uživatelé Azure AD mohli přihlašovat ke službě SAP Cloud pro zákazníky, musí se zřídit do cloudu SAP pro zákazníky. V cloudu SAP pro zákazníky je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se ke službě SAP Cloud pro zákazníka jako správce zabezpečení.

2. Na levé straně nabídky klikněte na **uživatele & autorizaci**  >  **Správa uživatelů**  >  **Přidat uživatele**.

    ![Snímek obrazovky zobrazující stránku Správa uživatelů s vybraným tlačítkem Přidat uživatele](./media/sap-customer-cloud-tutorial/configure03.png)

3. V části **Přidat nového uživatele** proveďte následující kroky:

    ![Konfigurace SAP](./media/sap-customer-cloud-tutorial/configure04.png)

    a. Do textového **pole jméno a** příjmení zadejte jméno uživatele jako **B**.

    b. Do textového pole **poslední jméno** zadejte jméno uživatele, jako je **Simon**.

    c. Do textového pole **e-mail** zadejte e-maily uživatele, jako je například `B.Simon@contoso.com` .

    d. Do textového pole **přihlašovací jméno** zadejte jméno uživatele jako **B. Simon**.

    e. Podle potřeby vyberte **typ uživatele** .

    f. Jako požadavek vyberte možnost **Aktivace účtu** .

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL cloudu SAP pro přihlašování zákazníka, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení zákazníka přímo do cloudu SAP a spustit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici SAP Cloud for Customer v části Moje aplikace, přesměruje se na adresu URL pro přihlášení zákazníka na Cloud SAP. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete Cloud SAP pro zákazníka, můžete vynutilit řízení relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Ovládací prvky relace se rozšíří z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).