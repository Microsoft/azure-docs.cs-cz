---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s schůzkami Cisco Webex | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Cisco Webex schůzky.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: bb8ea637d0353e4efa0cb946f486d68639fc699d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592485"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s WebEx schůzkami Cisco

V tomto kurzu se dozvíte, jak integrovat schůzky Cisco Webex s Azure Active Directory (Azure AD). Když integrujete schůzky Cisco Webex s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k WebEx schůzkám společnosti Cisco
* Umožněte, aby se vaši uživatelé automaticky přihlásili k WebEx schůzce Cisco s jejich účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.


## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Cisco Webex schůzky s povoleným jednotným přihlašováním (SSO).

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Schůzky Cisco Webex podporují **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

* Schůzky Cisco Webex podporují zřizování uživatelů **jenom v čase** .

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Přidávání schůzek Cisco Webex z Galerie

Pokud chcete nakonfigurovat integraci schůzek Cisco Webex do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat schůzky Cisco Webex z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **WebEx schůzky Cisco** .
1. Na panelu výsledků vyberte **schůzky Cisco Webex** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex-meetings"></a>Konfigurace a testování jednotného přihlašování Azure AD pro WebEx schůzky Cisco

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí setkání Cisco Webex s využitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v WebEx schůzce Cisco.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí WebEx schůzek Cisco, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
   1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
   1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
   
1. **[NAKONFIGURUJTE jednotné přihlašování pro schůzky Cisco Webex](#configure-cisco-webex-meetings-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
   * **[Vytvořte si uživatele WebEx schůzky Cisco](#create-cisco-webex-meetings-test-user)** , abyste měli protějšek B. Simon v setkáních Cisco Webex, které jsou propojené s reprezentací uživatele v Azure AD.
    
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **schůzky Cisco Webex** vyhledejte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML můžete nakonfigurovat aplikaci v režimu iniciované **IDP** tím, že nahrajete soubor **metadat poskytovatele služby** následujícím způsobem:
   1. Klikněte na **nahrát soubor metadat**.
   1. Kliknutím na **logo složky** vyberte soubor metadat a klikněte na **nahrát**.
   1. Po úspěšném dokončení nahrávání souboru metadat poskytovatele **služby se v** části **základní konfigurace SAML** automaticky naplní hodnoty **adresy URL pro odpovědi** .
   
      > [!Note]
      > Soubor metadat poskytovatele služeb získáte v části **Konfigurace jednotného přihlašování pro Cisco Webex schůzky** , které jsou vysvětleny dále v tomto kurzu. 

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** , proveďte následující kroky:  
   1. V části **základní konfigurace SAML** klikněte na ikonu Upravit/pero.

      ![Upravit základní konfiguraci SAML](common/edit-urls.png)

   1. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<customername>.my.webex.com`

1. Aplikace WebEx pro schůzky Cisco očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na tlačítko **Upravit** ikonu otevřete dialogové okno atributy uživatele.

   ![image](common/edit-attribute.png)

1. Kromě výše očekává aplikace WebEx schůzky Cisco několik atributů, které se vrátí zpátky v odpovědi SAML. V části deklarace identity uživatelů v dialogovém okně atributy uživatele proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulce: 

   | Name | Zdrojový atribut|
   | ---------------|  --------- |
   |   FirstName    | User. křestní jméno |
   |   polím    | User. příjmení |
   |   e-mail       | uživatel. pošta |
   |   UID    | uživatel. pošta |

   1. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .
   1. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.
   1. Ponechte **obor názvů** prázdný.
   1. Jako **atribut** vyberte zdroj.
   1. V seznamu **zdrojový atribut** vyberte v rozevíracím seznamu hodnotu atributu zobrazenou pro tento řádek.
   1. Klikněte na **Uložit**.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

   ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení schůzek Cisco Webex** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k schůzkám Cisco Webex.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **schůzky Cisco Webex**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-cisco-webex-meetings-sso"></a>Konfigurace jednotného přihlašování pro schůzky Cisco Webex

1. Přihlaste se ke schůzkám Cisco Webex pomocí svých přihlašovacích údajů správce.
1. Přejděte na **běžné nastavení webu** a přejděte na **Konfigurace jednotného přihlašování**.

   ![Snímek obrazovky se správou Cisco Webex s vybranými běžnými nastaveními lokality a konfigurací S. O.](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

1. Na stránce **Správa WebEx** proveďte následující kroky:

   ![Snímek obrazovky se stránkou pro správu WebEx, která obsahuje informace popsané v tomto kroku.](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

   1. jako **federační protokol** vyberte **SAML 2,0** .
   1. Kliknutím na odkaz **importovat metadata SAML** nahrajte soubor metadat, který jste stáhli z Azure Portal.
   1. Vyberte možnost **profil jednotného přihlašování** jako **IDP**  a kliknutím na tlačítko **exportovat** Stáhněte soubor metadat poskytovatele služby a nahrajte ho do části **základní konfigurace SAML** na Azure Portal.
   1. Do textového pole **AuthContextClassRef** zadejte jednu z následujících hodnot:
      * `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified`
      * `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`
    
      Pokud chcete povolit vícefaktorové ověřování pomocí Azure AD, zadejte dvě hodnoty jako: `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

   1. Vyberte **Automatické vytváření účtů**.
   
      > [!NOTE]
      > Pokud chcete povolit zřizování uživatelů za **běhu** , musíte si ověřit **Automatické vytváření účtů**. Kromě toho je potřeba předat atributy tokenu SAML v odpovědi SAML.

   1. Klikněte na **Uložit**.

      > [!NOTE]
      > Tato konfigurace je určena pouze pro zákazníky, kteří používají WebEx UserID ve formátu e-mailu.
      > 
      > Další informace o tom, jak nakonfigurovat schůzky Cisco Webex, najdete na stránce [dokumentace WebEx](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog) .

### <a name="create-cisco-webex-meetings-test-user"></a>Vytvoření testovacího uživatele pro schůzky Cisco Webex

Cílem této části je vytvořit uživatele s názvem B. Simon na schůzkách Cisco Webex. Schůzky Cisco Webex podporují zřizování za **běhu** , které je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel na schůzkách Cisco Webex ještě neexistuje, vytvoří se nový, když se pokusíte o přístup k schůzkám Cisco Webex.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>SP iniciované

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro schůzky Cisco Webex, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlášení k Cisco Webex schůzky přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k schůzkám Cisco Webex, pro které jste nastavili jednotné přihlašování.

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici schůzky Cisco Webex v části Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k schůzkám Cisco Webex, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete schůzky Cisco Webex, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)