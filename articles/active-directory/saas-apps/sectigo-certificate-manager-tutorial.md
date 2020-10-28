---
title: 'Kurz: Azure Active Directory integrace se správcem certifikátů Sectigo | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Sectigo správcem certifikátů.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: c589db84b6221aa23868b3b49aea84f33623619f
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92673860"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Kurz: Azure Active Directory integrace se správcem certifikátů Sectigo

V tomto kurzu se naučíte integrovat správce certifikátů Sectigo (označovaný také jako SCM) s Azure Active Directory (Azure AD).

Integrace Sectigo Certificate Manageru s Azure AD přináší následující výhody:

* Pomocí Azure AD můžete řídit, kdo má přístup k Sectigo správce certifikátů.
* Uživatelé můžou být přihlášeni automaticky k Sectigo správce certifikátů pomocí svých účtů Azure AD (jednotné přihlašování).
* Účty můžete spravovat v jednom centrálním umístění, Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD pomocí Správce certifikátů Sectigo potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte předplatné služby Azure AD, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Sectigo účet správce certifikátů.

> [!NOTE]
> Sectigo spustí více instancí správce certifikátů Sectigo. Hlavní instance Správce certifikátů Sectigo je  **https: \/ /CERT-Manager.com** a tato adresa URL se používá v tomto kurzu.  Pokud je váš účet na jiné instanci, je nutné odpovídajícím způsobem upravit adresy URL.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí a integrujte správce certifikátů Sectigo s Azure AD.

Správce certifikátů Sectigo podporuje následující funkce:

* **Jednotné přihlašování inicializované v SP**
* **Jednotné přihlašování iniciované IDP**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Přidat správce certifikátů Sectigo do Azure Portal

Pokud chcete integrovat správce certifikátů Sectigo s Azure AD, musíte přidat správce certifikátů Sectigo do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V nabídce vlevo vyberte **Azure Active Directory** .

    ![Možnost Azure Active Directory](common/select-azuread.png)

1. Vyberte **podnikové aplikace**  >  **všechny aplikace** .

    ![Podokno podnikové aplikace](common/enterprise-applications.png)

1. Chcete-li přidat aplikaci, vyberte možnost **Nová aplikace** .

    ![Možnost nové aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **Sectigo Certificate Manager** . Ve výsledcích hledání vyberte **Správce certifikátů Sectigo** a pak vyberte **Přidat** .

    ![Správce certifikátů Sectigo v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Sectigo správce certifikátů na základě testovacího uživatele s názvem **Britta Simon** . Aby jednotné přihlašování fungovalo, musíte vytvořit propojený vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem ve Správci certifikátů Sectigo.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Správce certifikátů Sectigo, musíte dokončit tyto stavební bloky:

| Úloha | Popis |
| --- | --- |
| **[Konfigurace jednotného přihlašování Azure AD](#configure-azure-ad-single-sign-on)** | Umožňuje uživatelům používat tuto funkci. |
| **[Konfigurace jednotného přihlašování správce certifikátů Sectigo](#configure-sectigo-certificate-manager-single-sign-on)** | Nakonfiguruje nastavení jednotného přihlašování v aplikaci. |
| **[Vytvoření testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** | Testuje jednotné přihlašování Azure AD pro uživatele s názvem Britta Simon. |
| **[Přiřazení testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** | Povolí službě Britta Simon používat jednotné přihlašování Azure AD. |
| **[Vytvoření testovacího uživatele správce certifikátů Sectigo](#create-a-sectigo-certificate-manager-test-user)** | Vytvoří protějšek Britta Simon ve Správci certifikátů Sectigo, který je propojený s reprezentacemi uživatele v Azure AD. |
| **[Test jednotného přihlašování](#test-single-sign-on)** | Ověřuje, že konfigurace funguje. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části nakonfigurujete jednotné přihlašování Azure AD pomocí Správce certifikátů Sectigo ve Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)v podokně integrace aplikace **Správce certifikátů Sectigo** vyberte **jednotné přihlašování** .

    ![Konfigurovat možnost jednotného přihlašování](common/select-sso.png)

1. V podokně **Vyberte metodu jednotného přihlašování** vyberte možnost **SAML** nebo **SAML/WS-nakrmený** režim pro povolení jednotného přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

1. V podokně **nastavit jednu Sign-On s** podoknem SAML vyberte **Upravit** (ikona tužky) a otevřete základní podokno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** proveďte následující kroky:

    1. V poli **identifikátor (ID entity)** pro hlavní instanci Sectigo správce certifikátů zadejte **https: \/ /CERT-Manager.com/Shibboleth** .

    1. V poli **Adresa URL odpovědi** pro hlavní instanci správce certifikátů Sectigo zadejte **https: \/ /CERT-Manager.com/Shibboleth.SSO/Saml2/post** .
        
    > [!NOTE]
    > I když obecně platí, že **přihlašovací adresa URL** je povinná pro *režim inicializovaný v režimu SP* ; není nutné se přihlašovat ze Správce certifikátů Sectigo.        

1. V části **základní konfigurační oddíl SAML** můžete v případě potřeby nakonfigurovat *režim iniciované IDP* a nechat **test** pracovat, a to provedením následujících kroků:

    1. Vyberte **nastavit další adresy URL** .

    1. Do pole **stav přenosu** zadejte adresu URL pro správce certifikátů Sectigo. V případě hlavní instance Správce certifikátů Sectigo zadejte **https: \/ /CERT-Manager.com/Customer/ \<customerURI\> /IDP** .

    ![Sectigo informace o jednotném přihlašování domén správce certifikátů a adres URL](common/idp-relay.png)

1. V části **atributy uživatele & deklarace identity** proveďte následující kroky:

    1. Odstranit všechny **Další deklarace identity** .
    
    1. Vyberte **Přidat novou deklaraci identity** a přidejte následující čtyři deklarace identity:
    
        | Name | Obor názvů | Zdroj | Zdrojový atribut | Popis |
        | --- | --- | --- | --- | --- |
        | eduPersonPrincipalName | empty | Atribut | User. userPrincipalName | Musí se shodovat s polem **ID osoby IDP** ve Správci certifikátů Sectigo pro správce. |
        | pošta | empty | Atribut | uživatel. pošta | Povinné |
        | givenName | empty | Atribut | User. křestní jméno | Volitelné |
        | sn | empty | Atribut | User. příjmení | Volitelné |

       ![Správce certifikátů Sectigo – přidejte čtyři nové deklarace identity.](media/sectigo-certificate-manager-tutorial/additional-claims.png)

1. V části **podpisový certifikát SAML** vyberte **Stáhnout** vedle **federačních metadat XML** . Uložte soubor XML do počítače.

    ![Možnost stažení XML federačních metadat](common/metadataxml.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Konfigurace jednotného přihlašování správce certifikátů Sectigo

Pokud chcete nakonfigurovat jednotné přihlašování na straně správce certifikátů Sectigo, odešlete stažený soubor XML federačních metadat do [týmu podpory správce certifikátů Sectigo](https://sectigo.com/support). Tým podpory správce certifikátů Sectigo používá informace, které odesíláte, aby bylo zajištěno, že připojení jednotného přihlašování pomocí protokolu SAML je správně nastaveno na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

V této části vytvoříte testovacího uživatele s názvem Britta Simon v Azure Portal.

1. V Azure Portal vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé** .

    ![Možnosti uživatelé a všichni uživatelé](common/users.png)

1. Vyberte **Nový uživatel** .

    ![Možnost Nový uživatel](common/new-user.png)

1. V podokně **uživatel** proveďte následující kroky:

    1. Do pole **název** zadejte **BrittaSimon** .
  
    1. Do pole **uživatelské jméno** zadejte **brittasimon \@ \<your-company-domain> . \<extension\>** . Například **brittasimon \@ contoso.com** .

    1. Zaškrtněte políčko **Zobrazit heslo** . Poznamenejte si hodnotu, která se zobrazí v poli **heslo** .

    1. Vyberte **Vytvořit** .

    ![Podokno uživatele](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části udělíte Britta Simon přístup k Sectigo Správci certifikátů, aby uživatel mohl používat jednotné přihlašování Azure.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**  >  **Sectigo Certificate Manager** .

    ![Podokno podnikové aplikace](common/enterprise-applications.png)

1. V seznamu aplikace vyberte **Sectigo správce certifikátů** .

    ![Správce certifikátů Sectigo v seznamu aplikací](common/all-applications.png)

1. V nabídce vyberte **Uživatelé a skupiny** .

    ![Možnost Uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte možnost **Přidat uživatele** . Pak v podokně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

1. V podokně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **Britta Simon** . Zvolte **Vybrat** .

1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v podokně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Zvolte **Vybrat** .

1. V podokně **Přidat přiřazení** vyberte **přiřadit** .

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Vytvoření testovacího uživatele správce certifikátů Sectigo

V této části vytvoříte uživatele s názvem Britta Simon ve Správci certifikátů Sectigo. Pokud chcete přidat uživatele na platformě Sectigo správce certifikátů, pracujte s [týmem podpory správce certifikátů Sectigo](https://sectigo.com/support) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD.

#### <a name="test-from-sectigo-certificate-manager-sp-initiated-single-sign-on"></a>Test z Sectigo Certificate Manageru (jednotné přihlašování iniciované v SP)

Přejděte na adresu URL specifickou pro zákazníka (pro hlavní instanci správce certifikátů Sectigo, https: \/ /CERT-Manager.com/Customer/ \<customerURI\> /a klikněte na tlačítko níže **nebo se přihlaste pomocí** .  Pokud je nakonfigurováno správně, budete automaticky přihlášeni k Sectigo správce certifikátů.

#### <a name="test-from-azure-single-sign-on-configuration-idp-initiated-single-sign-on"></a>Test z konfigurace jednotného přihlašování Azure (jednotné přihlašování iniciované IDP)

V podokně integrace aplikace **Správce certifikátů Sectigo** vyberte **jednotné přihlašování** a vyberte tlačítko **test** .  Pokud je nakonfigurováno správně, budete automaticky přihlášeni k Sectigo správce certifikátů.

#### <a name="test-by-using-the-my-apps-portal-idp-initiated-single-sign-on"></a>Testování pomocí portálu moje aplikace (IDP – jednotné přihlašování iniciované)

Na portálu moje aplikace vyberte **Sectigo Certificate Manager** .  Pokud jste správně nakonfigurovali, budete automaticky přihlášeni k Sectigo správce certifikátů. Další informace o portálu moje aplikace najdete v tématu věnovaném [přístupu a používání aplikací na portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc, přečtěte si tyto články:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)
- [Jednotné přihlašování k aplikacím v Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)