---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s využitím vzdálené podpory BeyondTrust | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a BeyondTrust vzdálenou podporou.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: jeedes
ms.openlocfilehash: 1996024d163a4bf7cfa741110038bb8db5b883e8
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632732"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyondtrust-remote-support"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s využitím vzdálené podpory BeyondTrust

V tomto kurzu se dozvíte, jak integrovat vzdálenou podporu BeyondTrust s Azure Active Directory (Azure AD). Když integrujete vzdálenou podporu BeyondTrust s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k BeyondTrust vzdálené podpoře.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k BeyondTrust vzdálené podpory pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* BeyondTrust vzdálená podpora jednotného přihlašování (SSO) s povoleným předplatným.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Vzdálená podpora BeyondTrust podporuje jednotné přihlašování se spuštěnou **aktualizací SP**
* Vzdálená podpora BeyondTrust podporuje zřizování uživatelů **jenom v čase**

## <a name="adding-beyondtrust-remote-support-from-the-gallery"></a>Přidání vzdálené podpory BeyondTrust z Galerie

Pokud chcete nakonfigurovat integraci BeyondTrust Remote support do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat BeyondTrust vzdálenou podporu z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **BeyondTrust vzdálená podpora** .
1. Na panelu výsledků vyberte **Vzdálená podpora BeyondTrust** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-beyondtrust-remote-support"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro BeyondTrust Remote support

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s BeyondTrust vzdálenou podporou pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v BeyondTrust vzdálené podpoře.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí vzdálené podpory BeyondTrust, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurovat jednotné přihlašování pro vzdálenou podporu BeyondTrust](#configure-beyondtrust-remote-support-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    * **[Vytvořte testovacího uživatele BeyondTrust Remote support](#create-beyondtrust-remote-support-test-user)** , abyste měli protějšek B. Simon ve vzdálené podpoře BeyondTrust, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **BeyondTrust Remote support** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<HOSTNAME>.bomgar.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<HOSTNAME>.bomgar.com/saml/sso`
    
    c. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<HOSTNAME>.bomgar.com/saml`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a adresou URL Sign-On. Tyto hodnoty se zobrazí později v tomto kurzu.

1. Aplikace BeyondTrust Remote support očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Aplikace BeyondTrust Remote support kromě výše očekává, že se v odpovědi SAML vrátí zpátky několik atributů, které jsou uvedené dál. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Name |  Zdrojový atribut|
    | ---------------| ----------|
    | Uživatelské jméno | User. userPrincipalName |
    | FirstName | User. křestní jméno |
    | LastName | User. příjmení |
    | E-mail | uživatel. pošta |
    | Skupiny | User. Groups |

    > [!NOTE]
    > Při přiřazování skupin Azure AD pro aplikaci BeyondTrust Remote support se skupiny vrácené v deklaraci identity musí změnit z možnosti žádné na skupinu zabezpečení. Skupiny se do aplikace naimportují jako ID jejich objektů. ID objektu skupiny Azure AD se dá najít tak, že zkontrolujete vlastnosti v rozhraní Azure Active Directory. Tato akce bude nutná pro odkazování a přiřazení skupin Azure AD ke správným zásadám skupiny.

1. Při nastavování jedinečného identifikátoru uživatele musí být tato hodnota nastavená na NameID-Format: **persistent**. Aby bylo možné správně identifikovat a přidružit uživatele ke správným zásadám skupiny pro oprávnění, je nutné, aby byl tento trvalý identifikátor. Kliknutím na ikonu Upravit otevřete dialogové okno **atributy uživatele & deklarací** pro úpravu hodnoty jedinečného identifikátoru uživatele.

1. V části **Spravovat deklaraci identity** klikněte na možnost **zvolit formát identifikátoru názvu** a nastavte hodnotu **trvalá** a klikněte na **Uložit**.

    ![Atributy a deklarace uživatele](./media/bomgarremotesupport-tutorial/attribute-unique-user-identifier.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení vzdálené podpory BeyondTrust** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup ke vzdálené podpoře BeyondTrust.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **BeyondTrust vzdálená podpora**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-beyondtrust-remote-support-sso"></a>Nakonfigurovat jednotné přihlašování BeyondTrust vzdálené podpory

1. V jiném okně webového prohlížeče se přihlaste k BeyondTrust vzdálené podpory jako správce.

1. Přejděte na **uživatele & Security**  >  **Security Providers**.

1. Klikněte na ikonu **Upravit** v **poskytovatelích SAML**.

    ![Ikona pro úpravy zprostředkovatelů SAML](./media/bomgarremotesupport-tutorial/saml-providers.png)

1. Rozbalte část **Nastavení poskytovatele služeb** .

1. Klikněte na **Stáhnout metadata poskytovatele služeb** , nebo můžete zkopírovat hodnoty **ID entity** a **adresy URL služby ACS** a použít tyto hodnoty v části **základní konfigurace SAML** v Azure Portal.

    ![Stáhnout metadata poskytovatele služby](./media/bomgarremotesupport-tutorial/service-provider-metadata.png)


1. V části nastavení zprostředkovatele identity klikněte na **nahrát metadata zprostředkovatele identity** a vyhledejte soubor XML s metadaty, který jste stáhli z Azure Portal.

1.  Automaticky se nahraje **ID entity**, **adresa url pro jednu Sign-On služby** a **certifikát serveru** a **vazba protokolu URL jednotného přihlašování** se bude muset změnit na **http post**.

    ![Snímek obrazovky s oddílem nastavení zprostředkovatele identity, kde tyto akce provedete, najdete v části.](./media/bomgarremotesupport-tutorial/identity-provider.png)

1. Klikněte na **Uložit**.

### <a name="create-beyondtrust-remote-support-test-user"></a>Vytvořit testovacího uživatele pro vzdálenou podporu BeyondTrust

V této části se v BeyondTrust vzdálené podpoře vytvoří uživatel s názvem Britta Simon. Vzdálená podpora BeyondTrust podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v BeyondTrust vzdálené podpoře neexistuje, vytvoří se po ověření nový.

Použijte následující postup, který je pro konfiguraci vzdálené podpory BeyondTrust povinný.

Tady budeme konfigurovat nastavení zřizování uživatelů. Hodnoty použité v tomto oddílu budou odkazovány z **atributů uživatele & deklarace identity** v Azure Portal. Nakonfigurovali jsme toto, aby byly výchozí hodnoty, které už jsou naimportované v době vytváření, ale v případě potřeby můžete tuto hodnotu přizpůsobit.

![Snímek obrazovky se zobrazí nastavení pro zřizování uživatelů, kde můžete nakonfigurovat hodnoty uživatelů.](./media/bomgarremotesupport-tutorial/user-attribute.png)

> [!NOTE]
> Atribut Groups and e-mail není pro tuto implementaci nezbytný. Pokud používáte skupiny Azure AD a přiřadíte je k BeyondTrust vzdálených zásad skupiny pro oprávnění, bude nutné, aby ID objektu skupiny bylo odkazováno prostřednictvím svých vlastností Azure Portal a umístěných v části "available Groups". Po dokončení této akce bude nyní k dispozici ID objektu/skupina AD pro přiřazení k zásadám skupiny pro oprávnění.

![Snímek obrazovky zobrazuje oddíl I T s typem členství, zdroj, typ a objekt I D.](./media/bomgarremotesupport-tutorial/config-user-2.png)

![Snímek obrazovky se stránkou základní nastavení pro zásady skupiny.](./media/bomgarremotesupport-tutorial/group-policy.png)

> [!NOTE]
> Případně můžete nastavit výchozí zásady skupiny u poskytovatele zabezpečení typu Saml2. Definováním této možnosti přiřadíte všem uživatelům, kteří se ověřují prostřednictvím SAML, oprávnění zadaná v rámci zásad skupiny. Obecné zásady členů jsou součástí BeyondTrust Remote support/Privileged Remote Access s omezenými oprávněními, která se dají použít k otestování ověřování a přiřazení uživatelů ke správným zásadám. Uživatelé neplní seznam typu Saml2 Users prostřednictvím/Login > uživatelé & zabezpečení až do prvního úspěšného pokusu o ověření. Další informace o zásadách skupiny najdete na následujícím odkazu: `https://www.beyondtrust.com/docs/remote-support/getting-started/admin/group-policies.htm`

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení ke vzdálené podpoře BeyondTrust, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro vzdálenou podporu BeyondTrust, která přímo přihlašuje, a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici vzdálená podpora BeyondTrust v části Moje aplikace, přesměruje se na adresu URL pro přihlášení ke vzdálené podpoře BeyondTrust. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete vzdálenou podporu BeyondTrust, můžete vymáhat ovládací prvky relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Ovládací prvky relace se rozšíří z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)