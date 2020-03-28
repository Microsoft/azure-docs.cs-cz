---
title: 'Kurz: Integrace služby Azure Active Directory s veritas Enterprise Vault.cloud Přisostyrek | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a jednotném přihlašování Veritas Enterprise Vault.cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 43094cabab3cfc93e0dffa59a15867d01b036d38
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67087620"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Kurz: Integrace služby Azure Active Directory s připojou k úložišti Veritas Enterprise Vault.cloud

V tomto kurzu se dozvíte, jak integrovat veritas Enterprise Vault.cloud přisuzu je s Azure Active Directory (Azure AD).
Integrace služby Veritas Enterprise Vault.cloud s připomene ním Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Veritas Enterprise Vault.cloud přiřazovat.
* Uživatelům můžete povolit automatické přihlášení k jednotnému přihlašování Veritas Enterprise Vault.cloud pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí služby Veritas Enterprise Vault.cloud SSO, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Veritas Enterprise Vault.cloud Jednotné přihlašování s povoleným předplatným

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Veritas Enterprise Vault.cloud SSO podporuje **sp** inicioval sso

## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Přidání služby Veritas Enterprise Vault.cloud z galerie

Pokud chcete nakonfigurovat integraci služby Veritas Enterprise Vault.cloud SSO do Azure AD, musíte přidat přidružit k automatickému připonutím Veritas Enterprise Vault.cloud z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat služby Veritas Enterprise Vault.cloud s připomenete z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Veritas Enterprise Vault.cloud SSO**, vyberte **veritas Enterprise Vault.cloud SSO** z panelu výsledků a pak klikněte na **tlačítko Přidat** a přidejte aplikaci.

     ![Veritas Enterprise Vault.cloud SSO v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí jednotného přihlašování Veritas Enterprise Vault.cloud na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v jednotném přihlašování Veritas Enterprise Vault.cloud.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí jednotného přihlašování Veritas Enterprise Vault.cloud, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace služby Veritas Enterprise Vault.cloud Jednotné přihlašování](#configure-veritas-enterprise-vaultcloud-sso-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte veritas Enterprise Vault.cloud testovacího uživatele s](#create-veritas-enterprise-vaultcloud-sso-test-user)** přihlašování k úložišti – chcete-li mít protějšek Britta Simon ve Veritas Enterprise Vault.cloud SSO, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí jednotného přihlašování Veritas Enterprise Vault.cloud, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Veritas Enterprise Vault.cloud Vyberte** **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Veritas Enterprise Vault.cloud Informace o jednotném přihlášení do domény a adresy URL](common/sp-identifier-reply.png)

    a. Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. V poli **Identifikátor** použijte adresu URL podle datacentra:

    | Datové centrum| zprostředkovatele identity |
    |----------|----|
    | Severní Amerika| `https://auth.lax.archivecloud.net` |
    | Evropa | `https://auth.ams.archivecloud.net` |
    | Asie a Tichomoří| `https://auth.syd.archivecloud.net`|

    c. V textovém poli **Adresa URL pro odpověď** použijte adresu URL podle datacentra:

    | Datové centrum| zprostředkovatele identity |
    |----------|----|
    | Severní Amerika| `https://auth.lax.archivecloud.net` |
    | Evropa | `https://auth.ams.archivecloud.net` |
    | Asie a Tichomoří| `https://auth.syd.archivecloud.net`|

    > [!NOTE]
    > Tato hodnota není skutečná. Aktualizujte tuto hodnotu skutečnou přihlašovací adresou URL. Obraťte se na [veritas Enterprise Vault.cloud SSO tým podpory klienta](https://www.veritas.com/support/.html) získat tuto hodnotu. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení služby Přisuzovaného k štoru Veritas Enterprise Vault.cloud** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-veritas-enterprise-vaultcloud-sso-single-sign-on"></a>Konfigurace jednotného přihlašování veritas Enterprise Vault.cloud

Chcete-li nakonfigurovat jednotné přihlašování na straně **jednotného přihlašování Veritas Enterprise Vault.cloud,** musíte odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z portálu Azure do [týmu podpory jednotného přihlašování Veritas Enterprise Vault.cloud](https://www.veritas.com/support/.html). Toto nastavení nastaví tak, aby bylo připojení s přizasazené k samovazbě SAML správně nastaveno na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské** brittasimon@yourcompanydomain.extensionjméno typ pole . Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k Veritas Enterprise Vault.cloud jednotné přihlašování.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Veritas Enterprise Vault.cloud SSO**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Veritas Enterprise Vault.cloud SSO**.

    ![Odkaz přihlašování k řešení Datas Enterprise Vault.cloud v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-veritas-enterprise-vaultcloud-sso-test-user"></a>Vytvoření testovacího uživatele automatického přiznaového řešení Veritas Enterprise Vault.cloud

V této části vytvoříte uživatele s názvem Britta Simon ve Veritas Enterprise Vault.cloud sso. Spolupracujte s [týmem podpory služby Veritas Enterprise Vault.cloud pro správě a zabezpečení](https://www.veritas.com/support/.html) a přidejte uživatele na platformu Veritas Enterprise Vault.cloud. Uživatelé musí být vytvořena a aktivována před použitím jednotného přihlášení.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Veritas Enterprise Vault.cloud spřipínání na přístupovém panelu, můžete by měl být automaticky přihlášeni k Veritas Enterprise Vault.cloud sso, pro které nastavíte přisazující služby. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

