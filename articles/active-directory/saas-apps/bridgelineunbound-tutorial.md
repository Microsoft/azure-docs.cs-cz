---
title: 'Kurz: Integrace služby Azure Active Directory s bridgeline unbound | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a bridgeline unbound.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b018472f-c8b3-403d-ae66-9ed26a35f413
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 641b3ee0dd2f6e232d2f36d08bda5b1f2cf80102
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73157592"
---
# <a name="tutorial-azure-active-directory-integration-with-bridgeline-unbound"></a>Kurz: Integrace služby Azure Active Directory s bridgeline bez vázaných

V tomto kurzu se dozvíte, jak integrovat Bridgeline unbound s Azure Active Directory (Azure AD).
Integrace Bridgeline Unbound s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Bridgeline bez vázaných.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke službě Bridgeline Unbound (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Bridgeline Unbound, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením Bridgeline Unbound

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Bridgeline podporuje **sp a IDP** inicioval a so
* Bridgeline Unbound podporuje zřizování uživatelů **just in time**

## <a name="adding-bridgeline-unbound-from-the-gallery"></a>Přidání bridgeline bez vázané z galerie

Chcete-li nakonfigurovat integraci Bridgeline Unbound do Azure AD, musíte přidat Bridgeline Unbound z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat bridgeline unbound z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Příkaz Bridgeline Unbound**, vyberte **Bridgeline Unbound** from result panel a pak klepnutím na **tlačítko Přidat** aplikaci přidejte.

     ![Bridgeline Unbound v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí aplikace Bridgeline Unbound na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v aplikaci Bridgeline Unbound.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby Bridgeline Unbound, je třeba provést následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace bezvázaného jednotného přihlašování Bridgeline](#configure-bridgeline-unbound-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte bridgeline bezvázaný testovací uživatel](#create-bridgeline-unbound-test-user)** – mít protějšek Britta Simon v Bridgeline bez vázaných, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování azure ad pomocí programu Bridgeline Unbound, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Bridgeline Unbound** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** Pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující kroky:

    ![Informace o jednotném přihlášení k nevázané doméně bridgeline a adresurl](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`iApps_UPSTT_<ENVIRONMENTNAME>`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<SUBDOMAIN>.iapps.com/SAMLAssertionService.aspx`

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![Informace o jednotném přihlášení k nevázané doméně bridgeline a adresurl](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<SUBDOMAIN>.iapps.com/CommonLogin/login?<INSTANCENAME>`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL pro odpověď a přihlašovací adresou URL. Obraťte se na [tým podpory klienta Bridgeline Unbound,](mailto:support@iapps.com) abyste získali tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavit bridgeline unbound** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-bridgeline-unbound-single-sign-on"></a>Konfigurace bezvázaného jednotného přihlašování bridgeline

Chcete-li nakonfigurovat jednotné přihlašování na straně **Bridgeline Unbound,** musíte odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z portálu Azure do [týmu podpory Bridgeline Unbound](mailto:support@iapps.com). Toto nastavení nastaví tak, aby bylo připojení s přizasazené k samovazbě SAML správně nastaveno na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. Do pole **Uživatelské jméno** zadejte **\@brittasimon yourcompanydomain.extension .**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k Bridgeline bez vázaných.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Bridgeline Unbound**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **možnost Bridgeline Unbound**.

    ![Odkaz Bridgeline Unbound v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-bridgeline-unbound-test-user"></a>Vytvořit uživatele bezvázaného testu bridgeline

V této části je vytvořen uživatel s názvem Britta Simon v Bridgeline Unbound. Bridgeline Unbound podporuje zřizování uživatelů just-in-time, které je ve výchozím nastavení povoleno. V této části pro vás není žádná položka akce. Pokud uživatel ještě neexistuje v aplikaci Bridgeline Unbound, vytvoří se po ověření nový uživatel.

> [!Note]
> Pokud potřebujete vytvořit uživatele ručně, obraťte se na [tým podpory Bridgeline Unbound](mailto:support@iapps.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Bridgeline Unbound na přístupovém panelu, můžete by měl být automaticky přihlášeni k Bridgeline unbound, u kterého nastavíte přihlašovací ho. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
