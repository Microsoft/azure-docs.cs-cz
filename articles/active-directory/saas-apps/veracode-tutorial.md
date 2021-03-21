---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Veracode | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Veracode.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: f56f2dc974df58575c72c93a0609026cd7bbf88d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101652619"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Veracode

V tomto kurzu se dozvíte, jak integrovat Veracode s Azure Active Directory (Azure AD). Když integrujete Veracode s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Veracode.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Veracode svým účtům Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným Veracodem jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. Veracode podporuje zprostředkovatele identity iniciované jednotného přihlašování a zřizování uživatelů za běhu.

## <a name="add-veracode-from-the-gallery"></a>Přidání Veracode z Galerie

Pokud chcete nakonfigurovat integraci Veracode do služby Azure AD, přidejte Veracode z Galerie do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole text "Veracode".
1. Na panelu výsledků vyberte **Veracode** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-veracode"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Veracode

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Veracode pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Veracode.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí Veracode postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
    * Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
1. Nakonfigurujte jednotné přihlašování **[VERACODE SSO](#configure-veracode-sso)** pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte Veracode testovacího uživatele](#create-veracode-test-user)** , který bude mít protějšek B. Simon v Veracode propojený s uživatelem v zastoupení Azure AD.
1. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Veracode** Najděte oddíl **Manage** . Vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyberte ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V **základní sekci konfigurace SAML** je aplikace předem nakonfigurovaná a potřebné adresy URL už jsou předem naplněné pomocí Azure. Vyberte **Uložit**.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **certifikát (Base64)**. Vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Snímek obrazovky s oddílem podpisového certifikátu SAML se zvýrazněným odkazem pro stažení](common/certificatebase64.png)

1. Veracode očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![Snímek obrazovky s atributy uživatele & část deklarace identity](common/default-attributes.png)

1. Veracode také očekává několik dalších atributů, které se mají zpětně předat v odpovědi SAML. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle svých požadavků.

    | Name | Zdrojový atribut|
    | ---------------| --------------- |
    | FirstName |User. křestní jméno |
    | polím |User. příjmení |
    | e-mail |Uživatel. pošta |

1. V části **Nastavení Veracode** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Snímek obrazovky s oddílem nastavení Veracode s zvýrazněnými adresami URL konfigurace](common/copy-configuration-urls.png)

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Veracode.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Veracode**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-veracode-sso"></a>Konfigurace jednotného přihlašování Veracode

1. V jiném okně webového prohlížeče se přihlaste k webu Veracode společnosti jako správce.

1. V nabídce v horní části vyberte **Nastavení**  >  **správce**.
   
    ![Snímek obrazovky se správou Veracode s ikonami nastavení a zvýrazněným správcem](./media/veracode-tutorial/admin.png "Správa")

1. Vyberte kartu **SAML** .

1. V části **nastavení pro organizaci SAML** proveďte následující kroky:

    ![Snímek obrazovky oddílu nastavení SAML organizace](./media/veracode-tutorial/saml.png "Správa")

    a.  Pro **vystavitele** vložte hodnotu **identifikátoru služby Azure AD** , který jste zkopírovali z Azure Portal.

    b. Pro **podpisový certifikát kontrolního výrazu** vyberte **zvolit soubor** a nahrajte stažený certifikát z Azure Portal.

    c. Pro **samostatnou registraci** vyberte **Povolit samostatnou registraci**.

1. V části **nastavení pro osobní registraci** proveďte následující kroky a potom vyberte **Uložit**:

    ![Snímek obrazovky s oddílem nastavení pro samostatnou registraci s vybranými různými možnostmi](./media/veracode-tutorial/save.png "Správa")

    a. Pro **aktivaci nového uživatele** vyberte možnost **nepožaduje se žádná aktivace**.

    b. V případě **aktualizací uživatelských dat** vyberte **Předvolby Veracode uživatelská data**.

    c. V části **Podrobnosti atributu SAML** vyberte následující:
      * **Role uživatelů**
      * **Správce zásad**
      * **Kontrolor**
      * **Vedoucí zabezpečení**
      * **Manažerský**
      * **Odesílatel**
      * **tvůrce**
      * **Všechny typy kontrol**
      * **Členství v týmu**
      * **Výchozí tým**

### <a name="create-veracode-test-user"></a>Vytvořit testovacího uživatele Veracode

V této části se v Veracode vytvoří uživatel s názvem B. Simon. Veracode podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V tomto oddílu není žádná položka akce. Pokud uživatel ještě v Veracode neexistuje, vytvoří se po ověření nový.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jiné nástroje pro vytváření uživatelských účtů Veracode nebo rozhraní API poskytovaná Veracode.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Klikněte na testovat tuto aplikaci v Azure Portal a měli byste se automaticky přihlášeni k Veracode, pro které jste nastavili jednotné přihlašování.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Veracode v okně moje aplikace, měli byste se automaticky přihlásit k Veracode, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Veracode můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).