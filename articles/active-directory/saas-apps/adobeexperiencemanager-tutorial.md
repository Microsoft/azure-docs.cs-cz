---
title: 'Kurz: Integrace služby Azure Active Directory se správcem prostředí Adobe Experience Manager | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Aplikací Adobe Experience Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f39751f40b32c5da24e13d75d2607d7da0a57ad3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73154111"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Kurz: Integrace služby Azure Active Directory se správcem prostředí Adobe Experience Manager

V tomto kurzu se dozvíte, jak integrovat Adobe Experience Manager s Azure Active Directory (Azure AD).
Integrace Správce prostředí Adobe s Azure AD vám přináší následující výhody:

* Ve službě Azure AD můžete řídit, kdo má přístup ke Správci adobe experience manageru.
* Uživatelům můžete povolit automatické přihlášení ke správci Adobe Experience Manager (jednotné přihlašování) pomocí jejich účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí aplikace Adobe Experience Manager, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením aplikace Adobe Experience Manager

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Adobe Experience Manager podporuje **sp a IDP** inicioval sso

* Aplikace Adobe Experience Manager podporuje zřizování uživatelů **just in time**

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Přidání správce prostředí Adobe z galerie

Chcete-li nakonfigurovat integraci aplikace Adobe Experience Manager do služby Azure AD, je třeba přidat aplikaci Adobe Experience Manager z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Správce prostředí Adobe z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Adobe Experience Manager**, z panelu výsledků vyberte Adobe Experience **Manager** a pak klepnutím na **tlačítko Přidat** aplikaci přidejte.

     ![Správce prostředí Adobe v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s [Název aplikace] na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v [Název aplikace].

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí [Název aplikace], musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování aplikace Adobe Experience Manager](#configure-adobe-experience-manager-single-sign-on)** – pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte testovacího uživatele aplikace Adobe Experience Manager](#create-adobe-experience-manager-test-user)** – chcete-li mít ve Správci Adobe Experience Manager protějšek Britty Simonové, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlášení služby Azure AD pomocí [Název aplikace], proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Adobe Experience Manager** vyberte Jednotné **přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** Pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující kroky:

    ![Informace o jednotném přihlášení správce prostředí Adobe Experience Manager](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte jedinečnou hodnotu, kterou definujete také na serveru AEM.

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > Hodnota adresy URL odpovědi není skutečná. Aktualizovat hodnotu adresy URL odpovědi pomocí skutečné adresy URL odpovědi Chcete-li získat tuto hodnotu, obraťte se na [tým podpory klienta Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) získat tuto hodnotu. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![Informace o jednotném přihlášení správce prostředí Adobe Experience Manager](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL serveru Adobe Experience Manager.

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavit Správce prostředí Adobe** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-adobe-experience-manager-single-sign-on"></a>Konfigurace jednotného přihlášení správce prostředí Adobe Experience Manager

1. V jiném okně prohlížeče otevřete portál pro **správu Správce adobe experience manageru.**

2. Vyberte **možnost** > **Nastavení uživatelé****zabezpečení** > .

    ![Konfigurace tlačítka pro uložení jednotného přihlášení](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

3. Vyberte **správce** nebo jiného relevantního uživatele.

    ![Tlačítko Konfigurovat ukládání jednotného přihlášení](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

4. Vyberte **Nastavení** > účtu**Správa truststore**.

    ![Tlačítko Konfigurovat ukládání jednotného přihlášení](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

5. V části **Přidat certifikát ze souboru CER**klepněte na **položku Vybrat soubor certifikátu**. Vyhledejte a vyberte soubor certifikátu, který jste už stáhli z webu Azure Portal.

    ![Konfigurovat tlačítko pro uložení jednotného přihlášení](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

6. Certifikát je přidán do úložiště trustů. Poznamenejte si alias certifikátu.

    ![Tlačítko Konfigurovat ukládání jednotného přihlášení](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

7. Na stránce **Uživatelé** vyberte **ověřovací službu**.

    ![Tlačítko Konfigurovat ukládání jednotného přihlášení](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

8. Vyberte **Nastavení** > účtu**Vytvořit nebo spravovat keystore**. Vytvořte KeyStore zadáním hesla.

    ![Tlačítko Konfigurovat ukládání jednotného přihlášení](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

9. Vraťte se na obrazovku správce. Potom vyberte **možnost Nastavení** > **operační** > **webové konzoly**.

    ![Tlačítko Konfigurovat ukládání jednotného přihlášení](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Tím se otevře konfigurační stránka.

    ![Konfigurace tlačítka pro uložení jednotného přihlášení](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

10. Vyhledejte **obslužnou rutinu ověřování aplikace Adobe Granite SAML 2.0**. Pak vyberte ikonu **Přidat.**

    ![Tlačítko Konfigurovat ukládání jednotného přihlášení](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

11. Na této stránce prováďte následující akce.

    ![Tlačítko Konfigurovat ukládání jednotného přihlášení](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. Do pole **Cesta** **/** zadejte .

    b. Do pole **Adresa URL IDP** zadejte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z webu Azure Portal.

    c. Do pole **Alias certifikátu IDP** zadejte hodnotu **Alias certifikátu,** kterou jste přidali do úložiště důvěryhodnosti.

    d. Do pole **ID entity poskytnutézabezpečení** zadejte jedinečnou hodnotu **identifikátoru Azure Ad,** kterou jste nakonfigurovali na webu Azure Portal.

    e. Do pole **Adresa URL služby Assertion Consumer Service** zadejte hodnotu adresy URL pro **odpověď,** kterou jste nakonfigurovali na webu Azure Portal.

    f. Do pole **Heslo úložiště klíčů** zadejte **heslo,** které jste nastavili v Úložišti klíčů.

    g. Do pole **ID atributu uživatele** zadejte **ID jména** nebo jiné ID uživatele, které je ve vašem případě relevantní.

    h. Vyberte **možnost Automaticky vytvářet uživatele crx**.

    i. Do pole **Adresa URL odhlášení** zadejte jedinečnou hodnotu **adresy URL odhlášení,** kterou jste získali z webu Azure Portal.

    j. Vyberte **Uložit**.

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

V této části umožníte Brittě Simonové používat jednotné přihlašování Azure udělením přístupu ke Správci adobe experience manageru.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte Adobe **Experience Manager**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Správce prostředí Adobe**.

    ![Odkaz Správce prostředí Adobe v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-adobe-experience-manager-test-user"></a>Vytvoření testovacího uživatele aplikace Adobe Experience Manager

V této části vytvoříte uživatele s názvem Britta Simon ve Správci adobe experience manageru. Pokud jste vybrali možnost **Automaticky vytvářet crx uživatele,** uživatelé se vytvoří automaticky po úspěšném ověření.

Chcete-li uživatele vytvořit ručně, spolupracujte s [týmem](https://helpx.adobe.com/support/experience-manager.html) podpory aplikace Adobe Experience Manager a přidejte je na platformu Adobe Experience Manager.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Správce prostředí Adobe na přístupovém panelu byste měli být automaticky přihlášeni ke Správci prostředí Adobe Experience, pro který nastavíte službu přiřazování služeb. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
