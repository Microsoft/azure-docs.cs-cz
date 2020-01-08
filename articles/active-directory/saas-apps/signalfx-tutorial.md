---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s SignalFx | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SignalFx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea81f0046d7f73d845ed49325a3d621e6b7735e7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443283"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s SignalFx

V tomto kurzu se dozvíte, jak integrovat SignalFx s Azure Active Directory (Azure AD). Když integrujete SignalFx s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k SignalFx.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k SignalFx svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* SignalFx odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SignalFx podporuje jednotné přihlašování **IDP** .
* SignalFx podporuje zřizování uživatelů **jenom v čase** .

## <a name="adding-signalfx-from-the-gallery"></a>Přidání SignalFx z Galerie

Pokud chcete nakonfigurovat integraci SignalFx do služby Azure AD, musíte přidat SignalFx z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **SignalFx** .
1. Na panelu výsledků vyberte **SignalFx** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-signalfx"></a>Konfigurace a testování jednotného přihlašování Azure AD pro SignalFx

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí SignalFx pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v SignalFx.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí SignalFx, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte SIGNALFX SSO](#configure-signalfx-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte SignalFx Test User](#create-signalfx-test-user)** -to, abyste měli protějšek B. Simon v SignalFx, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **SignalFx** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor** zadejte adresu URL: `https://api.signalfx.com/v1/saml/metadata`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://api.signalfx.com/v1/saml/acs/<integration ID>`

    > [!NOTE]
    > Předchozí hodnota není skutečná hodnota. Hodnotu aktualizujete skutečnou adresou URL odpovědi, která je vysvětlena dále v tomto kurzu.

1. SignalFx aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace SignalFx několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Name (Název) |  Zdrojový atribut|
    | ------------------- | -------------------- |
    | User.FirstName  | user.givenname |
    | User.email  | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User. LastName       | user.surname    |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení SignalFx** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k SignalFx.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **SignalFx**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-signalfx-sso"></a>Konfigurace jednotného přihlašování SignalFx

1. Přihlaste se k webu SignalFx společnosti jako správce.

1. V SignalFx klikněte nahoře na **integrace** a otevřete stránku integrace.

    ![Integrace SignalFx](./media/signalfx-tutorial/tutorial_signalfx_intg.png)

1. Klikněte na dlaždici **Azure Active Directory** v části **přihlašovací služby** .

    ![SignalFx SAML](./media/signalfx-tutorial/tutorial_signalfx_saml.png)

1. Klikněte na **Nová integrace** a na kartě **instalovat** proveďte následující kroky:

    ![SignalFx samlintgpage](./media/signalfx-tutorial/tutorial_signalfx_azure.png)

    a. Do textového pole **název** zadejte nový název integrace, jako je **OurOrgName SAML SSO**.

    b. Zkopírujte hodnotu **ID integrace** a přihlaste se k **adrese URL odpovědi** na místě `<integration ID>` v poli **Adresa URL odpovědi** **základní sekce konfigurace SAML** v Azure Portal.

    c. Kliknutím na **nahrát soubor** odešlete **certifikát kódovaný v kódování Base64** stažený z Azure Portal v textovém poli **certifikátu** .

    d. Do textového pole **Adresa URL vystavitele** vložte hodnotu **identifikátoru služby Azure AD**, který jste zkopírovali z Azure Portal.

    e. Do textového pole **Adresa URL metadat** vložte **přihlašovací adresu URL** , kterou jste zkopírovali z Azure Portal.

    f. Klikněte na možnost **Uložit**.

### <a name="create-signalfx-test-user"></a>Vytvořit testovacího uživatele SignalFx

Cílem této části je vytvořit uživatele s názvem Britta Simon v SignalFx. SignalFx podporuje zřizování za běhu, které je ve výchozím nastavení povolené. V této části není žádná položka akce. Nový uživatel se vytvoří během pokusu o přístup k SignalFx, pokud ještě neexistuje.

Když se uživatel poprvé přihlásí k SignalFx z jednotného přihlašování [SAML,](mailto:kmazzola@signalfx.com) pošle jim e-mail s odkazem, který musí kliknout k ověření. K tomu dojde pouze při prvním přihlášení uživatele; následné pokusy o přihlášení nebudou vyžadovat ověření e-mailem.

> [!Note]
> Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory SignalFx](mailto:kmazzola@signalfx.com) .

## <a name="test-sso"></a>Test SSO

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici SignalFx, měli byste se automaticky přihlásit k SignalFx, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si SignalFx s Azure AD](https://aad.portal.azure.com/)