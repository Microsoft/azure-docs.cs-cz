---
title: 'Kurz: Integrace Azure Active Directory s TOPdesk - Secure | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a TOPdesk – Zabezpečené.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8e06ee33-18f9-4c05-9168-e6b162079d88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 393557531fe69a494a16e0f4c49ac07891e490ec
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233405"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Kurz: Integrace Azure Active Directory s TOPdeskem – zabezpečení

V tomto kurzu se dozvíte, jak integrovat TOPdesk – zabezpečit pomocí Azure Active Directory (Azure AD).
Integrace TOPdesk – zabezpečení s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k TOPdesk – Secure.
* Můžete povolit, aby vaši uživatelé byli automaticky přihlášeni k TOPdesk – Secure (Jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s TOPdesk – secure, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* TOPdesk – zabezpečené předplatné s povoleným jedním přihlášením

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* TOPdesk - Secure podporuje **sp** iniciované sso

## <a name="adding-topdesk---secure-from-the-gallery"></a>Přidání TOPdesk - Bezpečné z galerie

Chcete-li nakonfigurovat integraci TOPdesk – secure do Azure AD, je třeba přidat TOPdesk – Secure z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat TOPdesk - Secure from the gallery, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **PŘÍKAZ TOPdesk - Secure**, vyberte **TOPdesk - Secure** from result panel a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![TOPdesk - Zabezpečit v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí topdesk – zabezpečené na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v TOPdesk – zabezpečení.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD pomocí TOPdesk – secure, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace topdesku – zabezpečeného jednotného přihlašování](#configure-topdesk---secure-single-sign-on)** – pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte TOPdesk – zabezpečené testování uživatele](#create-topdesk---secure-test-user)** – mít protějšek Britta Simon v TOPdesk – Zabezpečené, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí topdesk – zabezpečené, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce **INTEGRACE APLIKACÍ TOPdesk – zabezpečení** vyberte jednotné **přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![TOPdesk – zabezpečené domény a adresy URL jednotné přihlašovací informace](common/sp-identifier-reply.png)

    a. Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.topdesk.net`

    b. V poli **Adresa URL identifikátoru** vyplňte adresu URL metadat TOPdesk, kterou můžete načíst z konfigurace TOPdesk. Měl by používat následující vzor:`https://<companyname>.topdesk.net/saml-metadata/<identifier>`

    c. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.topdesk.net/tas/secure/login/verify`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL, identifikátorem a adresou URL pro odpověď. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory zabezpečeného klienta.](https://www.topdesk.com/us/support/) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. V části **Nastavit TOPdesk – Zabezpečit** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-topdesk---secure-single-sign-on"></a>Konfigurace topdesku – zabezpečené jednotné přihlašování

1. Přihlaste se na svůj **topdesk - Zabezpečit** firemní web jako správce.

2. V nabídce **TOPdesk** klepněte na **tlačítko Nastavení**.

    ![Nastavení](./media/topdesk-secure-tutorial/ic790598.png "Nastavení")

3. Klepněte na **položku Nastavení přihlášení**.

    ![Nastavení přihlášení](./media/topdesk-secure-tutorial/ic790599.png "Nastavení přihlášení")

4. Rozbalte nabídku **Nastavení přihlášení** a klepněte na tlačítko **Obecné**.

    ![Obecné](./media/topdesk-secure-tutorial/ic790600.png "Obecné")

5. V části **Zabezpečení** v části **konfigurace přihlášení SAML** proveďte následující kroky:

    ![Technická nastavení](./media/topdesk-secure-tutorial/ic790855.png "Technická nastavení")

    a. Kliknutím na **Stáhnout** stáhněte soubor veřejných metadat a pak jej uložte místně do počítače.

    b. Otevřete soubor metadat a vyhledejte uzel **AssertionConsumerService.**

    ![Assertion Consumer Service](./media/topdesk-secure-tutorial/ic790856.png "Assertion Consumer Service")

    c. Zkopírujte hodnotu **AssertionConsumerService,** vložte tuto hodnotu do textového pole Adresa URL odpovědi v části **TOPdesk – zabezpečená doména a adresy URL.**

6. Chcete-li vytvořit soubor certifikátu, proveďte následující kroky:

    ![Certifikát](./media/topdesk-secure-tutorial/ic790606.png "Certifikát")

    a. Otevřete stažený soubor metadat z webu Azure Portal.

    b. Rozbalte uzel **RoleDescriptor,** který má **xsi:typ** **fed:ApplicationServiceType**.

    c. Zkopírujte hodnotu uzlu **X509Certificate.**

    d. Zkopírované hodnoty **X509Certificate** uložte místně do počítače do souboru.

7. V části **Veřejné** klikněte na **Přidat**.

    ![Přidat](./media/topdesk-secure-tutorial/ic790607.png "Přidat")

8. Na stránce s **konfiguračním asistentem SAML** proveďte následující kroky:

    ![Pomocník pro konfiguraci SAML](./media/topdesk-secure-tutorial/ic790608.png "Pomocník pro konfiguraci SAML")

    a. Pokud chcete nahrát stažený soubor metadat z webu Azure Portal, klikněte v části **Metadata federace**na **Procházet**.

    b. Chcete-li nahrát soubor certifikátu, klepněte v části **Certifikát (RSA)** na **tlačítko Procházet**.

    c. Pro **private key (RSA, PKCS8, DER)** můžete nahrát vlastní soukromý klíč nebo se můžete obrátit na [tým podpory TOPdesk - Secure Client,](https://www.topdesk.com/us/support) abyste získali soukromý klíč.

    d. Chcete-li nahrát soubor s logem, který jste získali od týmu podpory TOPdesk, klepněte pod **ikonou Logo**na **tlačítko Procházet**.

    e. Do textového pole Atribut `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` **uživatelského jména** zadejte .

    f. Do textového pole **Zobrazované jméno** zadejte název konfigurace.

    g. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** zadejte **\@brittasimon vašecompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k TOPdesk – secure.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **TOPdesk – Secure**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací zadejte a vyberte **TOPdesk - Secure**.

    ![ToPdesk - Zabezpečený odkaz v seznamu aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-topdesk---secure-test-user"></a>Vytvořit TOPdesk - Zabezpečený testovací uživatel

Aby bylo možné uživatelům Azure AD přihlásit se k TOPdesk – zabezpečené, musí být zřízeny do TOPdesk – secure.  
V případě TOPdesk – zabezpečený, zřizování je ruční úloha.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Chcete-li konfigurovat zřizování uživatelů, proveďte následující kroky:

1. Přihlaste se k webu **TOPdesk - Zabezpečte** firemní web jako správce.

2. V nabídce nahoře klikněte na **TOPdesk \> New \> Support Files \> Operator**.

    ![Operátor](./media/topdesk-secure-tutorial/ic790610.png "Operátor")

3. V dialogovém okně **Nový operátor** proveďte následující kroky:

    ![Nový operátor](./media/topdesk-secure-tutorial/ic790611.png "Operátor new")

    a. Klikněte na kartu **Obecné.**

    b. Do textového pole **Příjmení** zadejte Příjmení uživatele, jako je **Simon**.

    c. V části **Umístění** vyberte **web** pro účet.

    d. Do textového pole **Přihlašovací jméno** v části **TOPdesk Login** zadejte přihlašovací jméno uživatele.

    e. Klikněte na **Uložit**.

> [!NOTE]
> Můžete použít jakékoli jiné TOPdesk – zabezpečené nástroje pro vytváření uživatelských účtů nebo rozhraní API poskytované TOPdesk – secure pro zřízení uživatelských účtů Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici TOPdesk – zabezpečení na přístupovém panelu, měli byste být automaticky přihlášeni k TOPdesk – zabezpečit, pro které nastavíte služby Zabezpečení. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

