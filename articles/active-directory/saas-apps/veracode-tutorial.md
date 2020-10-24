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
ms.date: 10/10/2019
ms.author: jeedes
ms.openlocfilehash: a62863607798e7f64a74926ebea4f1a1d05cbd6c
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517714"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Veracode

V tomto kurzu se dozvíte, jak integrovat Veracode s Azure Active Directory (Azure AD). Když integrujete Veracode s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Veracode.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Veracode svým účtům Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným Veracodem jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. Veracode podporuje zprostředkovatele identity iniciované jednotného přihlašování a zřizování uživatelů za běhu.

## <a name="add-veracode-from-the-gallery"></a>Přidání Veracode z Galerie

Pokud chcete nakonfigurovat integraci Veracode do služby Azure AD, přidejte Veracode z Galerie do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole text "Veracode".
1. Na panelu výsledků vyberte **Veracode** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-veracode"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Veracode

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Veracode pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Veracode.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Veracode, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
    * Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
1. Nakonfigurujte jednotné přihlašování **[VERACODE SSO](#configure-veracode-sso)** pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte Veracode testovacího uživatele](#create-veracode-test-user)** , který bude mít protějšek B. Simon v Veracode propojený s uživatelem v zastoupení Azure AD.
1. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Veracode** Najděte oddíl **Manage** . Vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyberte ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Snímek obrazovky s nastavením jednoduchého Sign-On s SAML se zvýrazněnou ikonou tužky](common/edit-urls.png)

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

## <a name="configure-veracode-sso"></a>Konfigurace jednotného přihlašování Veracode

1. V jiném okně webového prohlížeče se přihlaste k webu Veracode společnosti jako správce.

1. V nabídce v horní části vyberte **Nastavení**  >  **správce**.
   
    ![Snímek obrazovky se správou Veracode s ikonami nastavení a zvýrazněným správcem](./media/veracode-tutorial/ic802911.png "Správa")

1. Vyberte kartu **SAML** .

1. V části **nastavení pro organizaci SAML** proveďte následující kroky:

    ![Snímek obrazovky oddílu nastavení SAML organizace](./media/veracode-tutorial/ic802912.png "Správa")

    a.  Pro **vystavitele**vložte hodnotu **identifikátoru služby Azure AD** , který jste zkopírovali z Azure Portal.

    b. Pro **podpisový certifikát kontrolního výrazu**vyberte **zvolit soubor** a nahrajte stažený certifikát z Azure Portal.

    c. Pro **samostatnou registraci**vyberte **Povolit samostatnou registraci**.

1. V části **nastavení pro osobní registraci** proveďte následující kroky a potom vyberte **Uložit**:

    ![Snímek obrazovky s oddílem nastavení pro samostatnou registraci s vybranými různými možnostmi](./media/veracode-tutorial/ic802913.png "Správa")

    a. Pro **aktivaci nového uživatele**vyberte možnost **nepožaduje se žádná aktivace**.

    b. V případě **aktualizací uživatelských dat**vyberte **Předvolby Veracode uživatelská data**.

    c. V části **Podrobnosti atributu SAML**vyberte následující:
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

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte **Azure Active Directory**  > **Uživatelé**  >  **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:

   1. Jako **název**zadejte `B.Simon` .  
   1. Do pole **uživatelské jméno**zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Vyberte možnost **Zobrazit heslo**a potom zapište zobrazenou hodnotu.
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon použití jednotného přihlašování Azure tím, že udělíte přístup k Veracode.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu aplikace vyberte **Veracode**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Snímek obrazovky s oddílem spravovat se zvýrazněnými uživateli a skupinami](common/users-groups-blade.png)

1. Vyberte možnost **Přidat uživatele**. V dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

    ![Snímek stránky uživatelů a skupin se zvýrazněným možností přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v části **Uživatelé**možnost **B. Simon**. Pak zvolte **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Pak zvolte **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="create-veracode-test-user"></a>Vytvořit testovacího uživatele Veracode

Pokud se chcete přihlásit k Veracode, je potřeba, aby se uživatelé Azure AD zřídili v Veracode. Tato úloha je automatizovaná a vy nemusíte nic dělat ručně. Uživatelé se v případě potřeby automaticky vytvoří při prvním pokusu o jednotné přihlašování.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jiné nástroje pro vytváření uživatelských účtů Veracode nebo rozhraní API poskytovaná Veracode.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu vyberete **Veracode** , měli byste se automaticky přihlásit k Veracode, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si Veracode s Azure AD](https://aad.portal.azure.com/)