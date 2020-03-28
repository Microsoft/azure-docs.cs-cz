---
title: 'Kurz: Integrace služby Azure Active Directory s přenosem MOVEit – integrace Azure AD | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a MOVEit Transfer – integrace Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 43383e82d983c998d159728997da4757cc364999
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161323"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Kurz: Integrace Azure Active Directory s přenosem MOVEit – integrace Azure AD

V tomto kurzu se dozvíte, jak integrovat MOVEit Transfer – integrace Azure AD s Azure Active Directory (Azure AD).
Integrace přenosu MOVEit – integrace Azure AD s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k integraci MOVEit Transfer – Azure AD.
* Můžete povolit uživatelům, aby se automaticky přihlásili k MOVEit Transfer – integrace Azure AD (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s moveit transfer – integrace Azure AD, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* PŘENOS MOVEIT – předplatné s povoleným jedním přihlášením integrace Azure AD

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* PŘENOS MOVEIT – integrace Azure AD **podporuje** sp iniciované přiřazovat služby při microsoftf

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Přidání přenosu MOVEit – integrace Azure AD z galerie

Chcete-li nakonfigurovat integraci integrace MOVEit Transfer – integrace Azure AD do Azure AD, je třeba přidat MOVEit Transfer – Integrace Azure AD z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat přenos MOVEit – integraci Azure AD z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Ve vyhledávacím poli zadejte **MOVEit Transfer – integrace Azure AD**, vyberte **moveit transfer - azure ad integrace** z panelu výsledků a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![PŘENOS MOVEIT – integrace Azure AD v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí integrace MOVEit Transfer – Azure AD na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v rámci integrace MOVEit Transfer – Integrace Azure AD.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD pomocí integrace MOVEit Transfer – Azure AD, je třeba dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace přenosu MOVEit – integrace Azure AD jednotné přihlášení](#configure-moveit-transfer---azure-ad-integration-single-sign-on)** – ke konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte PŘENOS MOVEIT – uživatel testu integrace Azure AD](#create-moveit-transfer---azure-ad-integration-test-user)** – chcete-li mít protějšek Britta Simon v MOVEit Transfer – integrace Azure AD, která je propojená s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí integrace MOVEit Transfer – Azure AD, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **integrace MOVEit Transfer – Azure AD** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud máte **soubor metadat poskytovatele služeb**, proveďte v části Základní konfigurace **SAML** následující kroky:

    a. Klikněte na **Nahrát soubor metadat**.

    ![Nahrát soubor metadat](common/upload-metadata.png)

    b. Kliknutím na **logo složky** vyberte soubor metadat a klepněte na tlačítko **Nahrát**.

    ![výběr souboru metadat](common/browse-upload-metadata.png)

    c. Po úspěšném nahrání souboru metadat se hodnota **adresy URL identifikátoru** a **odpovědi** automaticky naplní v části Základní **konfigurace SAML:**

    ![PŘENOS MOVEIT – integrace azure ad domény a adresy URL jednotné přihlašovací informace](common/sp-identifier-reply.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://contoso.com`

    > [!NOTE]
    > Hodnota **přihlašovací adresy URL** není skutečná. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Obraťte se na [MOVEit Transfer – integrace Azure AD tým podpory klienta](https://community.ipswitch.com/s/support) získat hodnotu. Soubor metadat **poskytovatele služeb** si můžete stáhnout z **adresy URL metadat poskytovatele služeb,** který je vysvětlen dále v části **Konfigurace přenosu MOVEit – integrace Azure AD jednotného přihlášení** v kurzu. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. V **části Nastavit přenos MOVEit – integrace Azure AD** zkopírujte příslušné adresy URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-moveit-transfer---azure-ad-integration-single-sign-on"></a>Konfigurace přenosu MOVEit – jednotné přihlašování do integrace Azure AD

1. Přihlaste se ke svému klientovi přenosu MOVEit jako správce.

2. V levém navigačním podokně klepněte na tlačítko **Nastavení**.

    ![Sekce Nastavení na straně aplikace](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

3. Klikněte na odkaz **Jednotné přihlášení,** který se nachází v části **Zásady zabezpečení -> uživatelské ověření**.

    ![Zásady zabezpečení na straně aplikace](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

4. Kliknutím na odkaz URL metadat stáhnete dokument metadat.

    ![Adresa URL metadat poskytovatele služeb](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
   * Ověřte, zda se identifikátor **entityID** shoduje **s identifikátorem** v části **Základní konfigurace SAML** .
   * Ověřte, zda adresa URL umístění **služby AssertionConsumerService** odpovídá **adrese URL odpovědi** v části Základní konfigurace **saml.**
    
     ![Konfigurace jednotného přihlašování na straně aplikace](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

5. Kliknutím na **tlačítko Přidat zprostředkovatele identity** přidejte nového poskytovatele federované identity.

    ![Přidat zprostředkovatele identity](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

6. Klikněte na **Procházet...** vyberte soubor metadat, který jste stáhli z webu Azure Portal, a kliknutím na **Přidat zprostředkovatele identity** nahrajte stažený soubor.

    ![Poskytovatel identity SAML](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

7. Na stránce Upravit nastavení **zprostředkovatele federované identity** vyberte možnost**Ano**jako **povoleno** a klepněte na tlačítko **Uložit**.

    ![Nastavení federovaného zprostředkovatele identity](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

8. Na stránce **Upravit nastavení uživatele poskytovatele federované identity** proveďte následující akce:
    
    ![Upravit nastavení federovaného zprostředkovatele identity](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Jako **přihlašovací jméno**vyberte název **SAML** .
    
    b. Vyberte **Jiné** jako **Celé jméno** a v textovém poli **Název atributu** vložte hodnotu: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Vyberte **Jiný** jako **e-mail** a v `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`textovém poli **Název atributu** vložte hodnotu: .
    
    d. Vyberte **Ano** jako **automaticky vytvořit účet při přihlášení**.
    
    e. Klikněte na **tlačítko Uložit.**

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k integraci MOVEit Transfer – Azure AD.

1. Na webu Azure Portal vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **MOVEit Transfer – integrace Azure AD**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **MOVEit Transfer - integrace Azure AD**.

    ![Odkaz integrace MOVEit Transfer – Azure AD integrace v seznamu aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Vytvořit přenos MOVEit – uživatel testu integrace Azure AD

Cílem této části je vytvořit uživatele s názvem Britta Simon v MOVEit Transfer – Integrace Azure AD. PŘENOS MOVEIT – integrace Azure AD podporuje zřizování just-in-time, které jste povolili. V této části pro vás není žádná položka akce. Nový uživatel se vytvoří během pokusu o přístup k přenosu MOVEit – integrace Azure AD, pokud ještě neexistuje.

>[!NOTE]
>Pokud potřebujete vytvořit uživatele ručně, musíte kontaktovat [tým podpory klienta integrace MOVEit – Azure AD](https://community.ipswitch.com/s/support).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici integrace PŘENOS MOVEit – Azure AD integrace na přístupovém panelu, můžete by měl být automaticky přihlášeni k moveit transfer – integrace Azure AD, které nastavíte přisuzujete. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

