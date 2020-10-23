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
ms.date: 10/30/2019
ms.author: jeedes
ms.openlocfilehash: f5839b5df4cdaf4abf863587b5f84f87aa63b5a0
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92456921"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyondtrust-remote-support"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s využitím vzdálené podpory BeyondTrust

V tomto kurzu se dozvíte, jak integrovat vzdálenou podporu BeyondTrust s Azure Active Directory (Azure AD). Když integrujete vzdálenou podporu BeyondTrust s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k BeyondTrust vzdálené podpoře.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k BeyondTrust vzdálené podpory pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

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

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **BeyondTrust vzdálená podpora** .
1. Na panelu výsledků vyberte **Vzdálená podpora BeyondTrust** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-beyondtrust-remote-support"></a>Konfigurace a testování jednotného přihlašování Azure AD pro BeyondTrust vzdálenou podporu

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s BeyondTrust vzdálenou podporou pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v BeyondTrust vzdálené podpoře.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí vzdálené podpory BeyondTrust, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurovat jednotné přihlašování pro vzdálenou podporu BeyondTrust](#configure-beyondtrust-remote-support-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    * **[Vytvořte testovacího uživatele BeyondTrust Remote support](#create-beyondtrust-remote-support-test-user)** , abyste měli protějšek B. Simon ve vzdálené podpoře BeyondTrust, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **BeyondTrust Remote support** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<HOSTNAME>.bomgar.com/saml`

    b. Do pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<HOSTNAME>.bomgar.com`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<HOSTNAME>.bomgar.com/saml/sso`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným Sign-On URL, identifikátor a adresu URL odpovědi. Tyto hodnoty se zobrazí později v tomto kurzu.

1. Aplikace BeyondTrust Remote support očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Aplikace BeyondTrust Remote support kromě výše očekává, že se v odpovědi SAML vrátí zpátky několik atributů, které jsou uvedené dál. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Name |  Zdrojový atribut|
    | ---------------| ----------|
    | GivenName | User. křestní jméno |
    | EmailAddress | uživatel. pošta |
    | Name | User. userPrincipalName |
    | Uživatelské jméno | User. userPrincipalName |
    | Skupiny | User. Groups |
    | Jedinečný identifikátor uživatele | User. userPrincipalName |

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

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup ke vzdálené podpoře BeyondTrust.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **BeyondTrust vzdálená podpora**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-beyondtrust-remote-support-sso"></a>Nakonfigurovat jednotné přihlašování BeyondTrust vzdálené podpory

1. V jiném okně webového prohlížeče se přihlaste k BeyondTrust vzdálené podpory jako správce.

1. Klikněte na nabídku **stav** a zkopírujte **identifikátor**, **adresu URL odpovědi** a **adresu URL pro přihlášení** a použijte tyto hodnoty v části **základní konfigurace SAML** v Azure Portal.

    ![Snímek obrazovky se zobrazí v nabídce stav ve vzdálené podpoře BeyondTrust, kde můžete shromáždit tyto informace.](./media/bomgarremotesupport-tutorial/config-url-values.png)

1. Přejděte na rozhraní BeyondTrust Remote support/login na místě `https://support.example.com/login` , kde **support.example.com** je primárním názvem hostitele vašeho zařízení a ověřte ho pomocí přihlašovacích údajů pro správu.

1. Přejděte na **uživatele & Security**  >  **Security Providers**.

1. V rozevírací nabídce vyberte **SAML** a klikněte na tlačítko **vytvořit poskytovatele** .

1. V části nastavení zprostředkovatele identity je k dispozici možnost nahrát metadata zprostředkovatele identity. Vyhledejte soubor XML s metadaty, který jste stáhli z Azure Portal, a klikněte na tlačítko **nahrát** . Automaticky se nahrají **ID entity**, **Adresa URL služby a certifikát pro jednu Sign-On službu** a **vazba protokolu** se bude muset změnit na **http post**. Viz snímek obrazovky níže:

    ![Snímek obrazovky s oddílem nastavení zprostředkovatele identity, kde tyto akce provedete, najdete v části.](./media/bomgarremotesupport-tutorial/config-uploadfile.png)

### <a name="create-beyondtrust-remote-support-test-user"></a>Vytvořit testovacího uživatele pro vzdálenou podporu BeyondTrust

Tady budeme konfigurovat nastavení zřizování uživatelů. Hodnoty použité v tomto oddílu budou odkazovány z **atributů uživatele & deklarace identity** v Azure Portal. Nakonfigurovali jsme toto, aby byly výchozí hodnoty, které už jsou naimportované v době vytváření, ale v případě potřeby můžete tuto hodnotu přizpůsobit.

![Snímek obrazovky se zobrazí nastavení pro zřizování uživatelů, kde můžete nakonfigurovat hodnoty uživatelů.](./media/bomgarremotesupport-tutorial/config-user1.png)

> [!NOTE]
> Atribut Groups and e-mail není pro tuto implementaci nezbytný. Pokud používáte skupiny Azure AD a přiřadíte je k BeyondTrust vzdálených zásad skupiny pro oprávnění, bude nutné, aby ID objektu skupiny bylo odkazováno prostřednictvím svých vlastností Azure Portal a umístěných v části "available Groups". Po dokončení této akce bude nyní k dispozici ID objektu/skupina AD pro přiřazení k zásadám skupiny pro oprávnění.

![Snímek obrazovky zobrazuje oddíl I T s typem členství, zdroj, typ a objekt I D.](./media/bomgarremotesupport-tutorial/config-user2.png)

![Snímek obrazovky se stránkou základní nastavení pro zásady skupiny.](./media/bomgarremotesupport-tutorial/config-user3.png)

> [!NOTE]
> Případně můžete nastavit výchozí zásady skupiny u poskytovatele zabezpečení typu Saml2. Definováním této možnosti přiřadíte všem uživatelům, kteří se ověřují prostřednictvím SAML, oprávnění zadaná v rámci zásad skupiny. Obecné zásady členů jsou součástí BeyondTrust Remote support/Privileged Remote Access s omezenými oprávněními, která se dají použít k otestování ověřování a přiřazení uživatelů ke správným zásadám. Uživatelé neplní seznam typu Saml2 Users prostřednictvím/Login > uživatelé & zabezpečení až do prvního úspěšného pokusu o ověření. Další informace o zásadách skupiny najdete na následujícím odkazu: `https://www.beyondtrust.com/docs/remote-support/getting-started/admin/group-policies.htm`

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici vzdálená podpora BeyondTrust na přístupovém panelu, měli byste se automaticky přihlásit ke vzdálené podpoře BeyondTrust, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si vzdálenou podporu BeyondTrust s Azure AD](https://aad.portal.azure.com/)