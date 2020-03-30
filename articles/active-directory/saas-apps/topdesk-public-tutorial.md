---
title: 'Kurz: Integrace služby Azure Active Directory s TOPdesk – veřejné | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a TOPdesk – veřejné.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.openlocfilehash: e5575a2e8f776e87fcd4e6f4a7a9244752ebfd9a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "71950423"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Kurz: Integrace Azure Active Directory s TOPdeskem – veřejná

V tomto kurzu se dozvíte, jak integrovat TOPdesk – veřejné s Azure Active Directory (Azure AD).
Integrace TOPdesk – veřejné s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k TOPdesk – veřejné.
* Můžete povolit, aby vaši uživatelé byli automaticky přihlášeni k TOPdesk – Public (Single Sign-On) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s TOPdesk – veřejné, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* TOPdesk – veřejné předplatné s povoleným jednorázovým přihlášením

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* TOPdesk - Veřejná podporuje **sp** inicioval sso

## <a name="adding-topdesk---public-from-the-gallery"></a>Přidání TOPdesk - Veřejné z galerie

Chcete-li nakonfigurovat integraci TOPdesk – veřejné do Azure AD, je třeba přidat TOPdesk – veřejné z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat TOPdesk - Public z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **PŘÍKAZ TOPdesk - Public**, vyberte **TOPdesk - Public** from result panel a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![TOPdesk - Veřejné v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí topdesk – veřejné na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v TOPdesk – veřejné.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD pomocí TOPdesk – veřejné, je třeba dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace topdesku – veřejného jednotného přihlašování](#configure-topdesk---public-single-sign-on)** – pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte TOPdesk – veřejný testovací uživatel](#create-topdesk---public-test-user)** – chcete-li mít protějšek Britta Simon v TOPdesk – veřejné, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí TOPdesk – veřejné, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce **INTEGRACE TOPdesk – veřejná** aplikace vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4.  Pokud máte **soubor metadat poskytovatele služeb**, proveďte v části Základní konfigurace **SAML** následující kroky:

    >[!NOTE]
    >Soubor metadat **poskytovatele služeb** získáte z části **Konfigurace topdesku – veřejné jednotné přihlašování,** která je vysvětlena dále v kurzu.

    a. Klikněte na **Nahrát soubor metadat**.
    
    ![Nahrát soubor metadat](common/upload-metadata.png)

    b. Kliknutím na **logo složky** vyberte soubor metadat a klepněte na tlačítko **Nahrát**.

    ![výběr souboru metadat](common/browse-upload-metadata.png)

    c. Po úspěšném nahrání souboru metadat se hodnoty **adresy URL identifikátorů** a **odpovědí** automaticky naplní v části Základní konfigurace SAML.

    ![TOPdesk - Public Domain a URL jednotné přihlašovací informace](common/sp-identifier-reply.png)

    d. Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.topdesk.net`

    e. V textovém poli **Adresa URL identifikátoru** vyplňte adresu URL metadat TOPdesk, kterou lze načíst z konfigurace TOPdesk. Měl by používat následující vzor:`https://<companyname>.topdesk.net/saml-metadata/<identifier>`
    
    f. Do textového pole **Adresa URL pro odpověď** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > Pokud se hodnoty **adresy URL** **identifikátorů** a odpovědí automaticky nevyplňují, je třeba je zadat ručně. Pro identifikátor postupujte podle vzoru, jak je uvedeno výše, a získáte hodnotu URL odpovědi z **konfigurovat TOPdesk - veřejné jednotné přihlášení** sekce, která je vysvětlena dále v kurzu. Hodnota **přihlašovací adresy URL** není skutečná, takže je třeba hodnotu aktualizovat skutečnou přihlašovací adresou URL. Chcete-li získat hodnotu, obraťte se na [tým podpory veřejného klienta.](https://help.topdesk.com/saas/enterprise/user/) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. V části **Nastavit TOPdesk – veřejné** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-topdesk---public-single-sign-on"></a>Konfigurace topdesku – veřejné jednotné přihlašování

1. Přihlaste se k webu **TOPdesk - Veřejná** společnost jako správce.

2. V nabídce **TOPdesk** klepněte na **tlačítko Nastavení**.
   
    ![Nastavení](./media/topdesk-public-tutorial/ic790598.png "Nastavení")

3. Klepněte na **položku Nastavení přihlášení**.
   
    ![Nastavení přihlášení](./media/topdesk-public-tutorial/ic790599.png "Nastavení přihlášení")

4. Rozbalte nabídku **Nastavení přihlášení** a klepněte na tlačítko **Obecné**.
   
    ![Obecné](./media/topdesk-public-tutorial/ic790600.png "Obecné")

5. V části **Veřejné** přihlášení **saml přihlašovací** konfigurace části proveďte následující kroky:
   
    ![Technická nastavení](./media/topdesk-public-tutorial/ic790601.png "Technická nastavení")
   
    a. Kliknutím na **Stáhnout** stáhněte soubor veřejných metadat a pak jej uložte místně do počítače.
   
    b. Otevřete stažený soubor metadat a vyhledejte uzel **AssertionConsumerService.**

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Zkopírujte hodnotu **AssertionConsumerService** a vložte tuto hodnotu do textového pole **Adresa URL odpovědi** v části Základní konfigurace **SAML.**      
   
6. Chcete-li vytvořit soubor certifikátu, proveďte následující kroky:
    
    ![Certifikát](./media/topdesk-public-tutorial/ic790606.png "Certifikát")
    
    a. Otevřete stažený soubor metadat z webu Azure Portal.
    
    b. Rozbalte uzel **RoleDescriptor,** který má **xsi:typ** **fed:ApplicationServiceType**.
    
    c. Zkopírujte hodnotu uzlu **X509Certificate.**
    
    d. Zkopírované hodnoty **X509Certificate** uložte místně do počítače do souboru.

7. V části **Veřejné** klikněte na **Přidat**.
    
    ![Přihlášení saml](./media/topdesk-public-tutorial/ic790625.png "Přihlášení saml")

8. Na stránce s **konfiguračním asistentem SAML** proveďte následující kroky:
    
    ![Pomocník pro konfiguraci SAML](./media/topdesk-public-tutorial/ic790608.png "Pomocník pro konfiguraci SAML")
    
    a. Pokud chcete nahrát stažený soubor metadat z webu Azure Portal, klikněte v části **Metadata federace**na **Procházet**.

    b. Chcete-li nahrát soubor certifikátu, klepněte v části **Certifikát (RSA)** na **tlačítko Procházet**.

    c. Chcete-li nahrát soubor s logem, který jste získali od týmu podpory TOPdesk, klepněte pod **ikonou Logo**na **tlačítko Procházet**.

    d. Do textového pole Atribut `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` **uživatelského jména** zadejte .

    e. Do textového pole **Zobrazované jméno** zadejte název konfigurace.

    f. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k TOPdesk – veřejné.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **TOPdesk – veřejné**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **TOPdesk - Public**.

    ![ToPdesk - Veřejný odkaz v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-topdesk---public-test-user"></a>Vytvořit TOPdesk - Veřejný testovací uživatel

Aby bylo možné uživatelům Azure AD přihlásit do TOPdesk – veřejné, musí být zřízena do TOPdesk – Veřejné. V případě TOPdesk – veřejné zřizování je ruční úloha.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Chcete-li konfigurovat zřizování uživatelů, proveďte následující kroky:

1. Přihlaste se ke svému **webu TOPdesk - Veřejný** web společnosti jako správce.

2. V nabídce v horní části klikněte na **TOPdesk \> New \> Support Files \> Person**.
   
    ![Person (Osoba)](./media/topdesk-public-tutorial/ic790628.png "Person (Osoba)")

3. V dialogovém okně Nová osoba proveďte následující kroky:
   
    ![Nová osoba](./media/topdesk-public-tutorial/ic790629.png "Nová osoba")
   
    a. Klikněte na kartu Obecné.

    b. Do textového pole **Příjmení** zadejte Příjmení uživatele, jako je Simon
 
    c. Vyberte **web** pro účet.
 
    d. Klikněte na **Uložit**.

> [!NOTE]
> Můžete použít jakékoli jiné TOPdesk – nástroje pro vytváření veřejných uživatelských účtů nebo rozhraní API poskytované TOPdesk – veřejné zřídit uživatelské účty Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na topdesk – veřejné dlaždice na přístupovém panelu, měli byste být automaticky přihlášeni k TOPdesk – veřejné, pro které nastavíte přihlašovací služby. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
