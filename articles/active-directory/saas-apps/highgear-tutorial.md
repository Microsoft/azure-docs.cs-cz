---
title: 'Kurz: Integrace služby Azure Active Directory s HighGear | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a HighGear.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 55dcd2fb-96b7-46ec-9e69-eee71c535773
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed06586435315935566ca0b1519b182d4fc47d39
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159038"
---
# <a name="tutorial-azure-active-directory-integration-with-highgear"></a>Kurz: Integrace Azure Active Directory s HighGear

V tomto kurzu se dozvíte, jak integrovat HighGear s Azure Active Directory (Azure AD).
Integrace HighGearu s Azure AD vám přináší následující výhody:

* Můžete řídit ve službě Azure AD, kdo má přístup k HighGear.
* Uživatelům můžete povolit automatické přihlášení k HighGearu (jednotné přihlašování) pomocí jejich účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s HighGear, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Systém HighGear s licencí Enterprise nebo Unlimited

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu se dozvíte, jak nakonfigurovat a otestovat jednotné přihlašování Azure AD v testovacím prostředí.

* HighGear podporuje **SP a IdP** inicioval SSO

## <a name="adding-highgear-from-the-gallery"></a>Přidání HighGear z galerie

Chcete-li nakonfigurovat integraci HighGearu do Azure AD, musíte přidat HighGear z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat HighGear z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **HighGear**, z panelu výsledků vyberte **HighGear** a pak kliknutím na tlačítko **Přidat** aplikaci přidejte.

     ![HighGear v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části se dozvíte, jak nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí systému HighGear na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem ve vašem systému HighGear.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí systému HighGear, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování HighGear](#configure-highgear-single-sign-on)** – pro konfiguraci nastavení jednotného přihlášení na straně aplikace HighGear.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu HighGear](#create-highgear-test-user)** – chcete-li mít protějšek Britta Simon v HighGear, který je propojen s reprezentací Azure AD uživatele. 
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části se dozvíte, jak povolit jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí systému HighGear, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **HighGear** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** otevřete klepnutím na ikonu **Upravit** dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlášení domény HighGear a adres URL](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** vložte hodnotu pole **ID entity poskytovatele služeb,** které je na stránce Nastavení jednotného přihlášení v systému HighGear.

    ![Pole ID entity poskytovatele služeb](media/highgear-tutorial/service-provider-entity-id-field.png)
    
    > [!NOTE]
    > Abyste měli přístup na stránku Nastavení jednotného přihlášení, musíte se přihlásit do systému HighGear. Po přihlášení přesuňte ukazatel myši na kartu Správa v zařízení HighGear a klikněte na položku nabídky Nastavení jednotného přihlášení.
    
    ![Položka nabídky Nastavení jednotného přihlášení](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

    b. Do textového pole **Adresa URL pro odpověď** vložte hodnotu adresy URL **služby Assertion Consumer Service (ACS)** ze stránky Nastavení jednotného přihlášení v systému HighGear.

    ![Pole URL služby Assertion Consumer Service (ACS)](media/highgear-tutorial/assertion-consumer-service-url-field.png)

    c. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

     ![Informace o jednotném přihlášení domény HighGear a adres URL](common/metadata-upload-additional-signon.png)

     Do textového pole **Přihlašovací adresa URL** vložte hodnotu pole **ID entity poskytovatele služeb,** které je na stránce Nastavení jednotného přihlášení v systému HighGear. (Toto ID entity je také základní adresou URL systému HighGear, která má být použita pro přihlášení iniciované sp.)

    ![Pole ID entity poskytovatele služeb](media/highgear-tutorial/service-provider-entity-id-field.png)

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL pro odpověď a adresou URL pro přihlášení na stránce **Nastavení jednotného přihlášení** v systému HighGear. Pokud potřebujete pomoc, obraťte se na [tým podpory HighGear](mailto:support@highgear.com).

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** a uložte jej do počítače. Budete ji potřebovat v pozdějším kroku konfigurace jednotného přihlášení.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení highgear** uvažte umístění následujících adres URL.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Adresa URL přihlášení. Tuto hodnotu budete potřebovat v krokové #2 v části **Konfigurace jednotného přihlášení HighGear** níže.

    b. Identifikátor ad Azure. Tuto hodnotu budete potřebovat v #3 krok v části **Konfigurace jednotného přihlášení HighGear** níže.

    c. Adresa URL pro odhlášení. Tuto hodnotu budete potřebovat v #4 krok v části **Konfigurace jednotného přihlášení HighGear** níže.

### <a name="configure-highgear-single-sign-on"></a>Konfigurace jednotného přihlašování HighGear

Chcete-li nakonfigurovat zařízení HighGear pro jednotné přihlašování, přihlaste se do systému HighGear. Po přihlášení přesuňte ukazatel myši na kartu Správa v zařízení HighGear a klikněte na položku nabídky Nastavení jednotného přihlášení.

![Položka nabídky Nastavení jednotného přihlášení](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

1. Do **pole Název poskytovatele identity**zadejte krátký popis, který se zobrazí v tlačítku jednotného přihlašování zařízení HighGear na stránce Přihlášení. Například: Azure AD

2. V poli **Adresa URL jednotného přihlášení (SSO)** v HighGearu vložte hodnotu z pole **Přihlašovací adresa URL,** které je v části **Nastavení HighGear** v Azure.

3. V poli **ID entity zprostředkovatele identity** v HighGearu vložte hodnotu z pole **Identifikátor Azure AD,** které je v části **Nastavení HighGear** v Azure.

4. V poli **Adresa URL jednotného odhlášení (SLO)** v HighGearu vložte hodnotu z pole **Url odhlášení,** které je v části **Nastavit HighGear** v Azure.

5. Poznámkový blok slouží k otevření certifikátu, který jste stáhli z části **podpisový certifikát SAML** v Azure. Měli jste si stáhnout formát **certifikátu (Base64).** Zkopírujte obsah certifikátu z poznámkového bloku a vložte jej do pole **Certifikát poskytovatele identity** v zařízení HighGear.

6. Pošlete e-mail [emoten týmu podpory HighGear](mailto:support@highgear.com) a požádejte o certifikát HighGear. Podle pokynů, které od nich obdržíte, vyplňte pole **HighGear Certificate** a **HighGear Certificate Password.**

7. Kliknutím na tlačítko **Uložit** uložte konfiguraci jednotného přihlášení zařízení HighGear.

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

    c. Zaškrtněte políčko **Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k HighGear.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **HighGear**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **HighGear**.

    ![Odkaz HighGear v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-highgear-test-user"></a>Vytvoření testovacího uživatele HighGear

Chcete-li vytvořit testovacího uživatele HighGear, který otestuje konfiguraci jednotného přihlášení, přihlaste se do systému HighGear.

1. Klepněte na tlačítko **Vytvořit nový kontakt.**

    ![Tlačítko Vytvořit nový kontakt](media/highgear-tutorial/create-new-contact-button.png)

    Zobrazí se nabídka, která vám umožní zvolit druh kontaktu, který chcete vytvořit.

2. Kliknutím na položku **individuální** nabídky vytvořte uživatele HighGear.

    Podokno se posune vpravo, takže můžete zadat informace o novém uživateli.  
    ![Nový kontaktní formulář](media/highgear-tutorial/new-contact-form.png)

3. Do pole **Název** zadejte název kontaktu. Například: Britta Simon

4. Klepněte na nabídku **Další možnosti** a vyberte položku nabídky **Informace o účtu.**

    ![Kliknutí na položku nabídky Informace o účtu](media/highgear-tutorial/account-info-menu-item.png)

5. Nastavte pole **Může se přihlásit** na Ano.

    Pole **Povolit jednotné přihlašování** bude automaticky nastaveno na ano.

6. Do pole **Id uživatele jednotného přihlášení** zadejte id uživatele. Příklad: BrittaSimon@contoso.com

    Část Informace o účtu by nyní měla vypadat nějak takto:  
    ![Oddíl Informace o dokončeném účtu](media/highgear-tutorial/finished-account-info-section.png)

7. Chcete-li kontakt uložit, klepněte na tlačítko **Uložit** v dolní části podokna.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici HighGear na přístupovém panelu, měli byste být automaticky přihlášeni k HighGear, pro které nastavíte přiřazovat. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

