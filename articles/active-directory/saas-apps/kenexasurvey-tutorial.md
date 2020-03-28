---
title: 'Kurz: Integrace služby Azure Active Directory s IBM Kenexa Survey Enterprise | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a IBM Kenexa Survey Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c7aac6da-f4bf-419e-9e1a-16b460641a52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: jeedes
ms.openlocfilehash: c649b966b3e210f6b026b06a9654761e0f97aea1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67099061"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Kurz: Integrace Azure Active Directory s IBM Kenexa Survey Enterprise

V tomto kurzu se dozvíte, jak integrovat IBM Kenexa Survey Enterprise s Azure Active Directory (Azure AD).
Integrace IBM Kenexa Survey Enterprise s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, která má přístup k IBM Kenexa Survey Enterprise.
* Uživatelům můžete povolit automatické přihlášení k IBM Kenexa Survey Enterprise (Jednotné přihlášení) pomocí jejich účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s IBM Kenexa Survey Enterprise, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením IBM Kenexa Survey Enterprise

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* IBM Kenexa Survey Enterprise podporuje sso iniciované **idp**

## <a name="adding-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Přidání produktu IBM Kenexa Survey Enterprise z galerie

Chcete-li nakonfigurovat integraci IBM Kenexa Survey Enterprise do Azure AD, musíte přidat IBM Kenexa Survey Enterprise z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat produkt IBM Kenexa Survey Enterprise z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ibm Kenexa Survey Enterprise**, z panelu výsledků vyberte ibm **Kenexa Survey Enterprise** a klepnutím na tlačítko **Přidat** aplikaci přidejte.

     ![IBM Kenexa Survey Enterprise v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí IBM Kenexa Survey Enterprise na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v IBM Kenexa Survey Enterprise.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD pomocí ibm Kenexa Survey Enterprise, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Nakonfigurujte ibm Kenexa Survey Enterprise Single Sign-On](#configure-ibm-kenexa-survey-enterprise-single-sign-on)** – pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte testovacího uživatele IBM Kenexa Survey Enterprise](#create-ibm-kenexa-survey-enterprise-test-user)** – chcete-li mít protějšek Britty Simonové v IBM Kenexa Survey Enterprise, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí IBM Kenexa Survey Enterprise, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **IBM Kenexa Survey Enterprise** vyberte možnost Jednotné **přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** proveďte následující kroky:

    ![Ibm Kenexa Survey Enterprise Domain a adresy URL jednotné přihlašovací informace](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://surveys.kenexa.com/<companycode>`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou adresou URL identifikátoru a odpovědi. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory IBM Kenexa Survey Enterprise Client.](https://www.ibm.com/support/home/?lnk=fcw) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Aplikace IBM Kenexa Survey Enterprise očekává, že obdrží kontrolní výrazy jazyka rozpoznávání zabezpečení (SAML) v určitém formátu, který vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Hodnota deklarace identifikátoru uživatele v odpovědi musí odpovídat ID přihlašovaného pomocí služby SSO, které je nakonfigurované v systému Kenexa. Chcete-li namapovat příslušný identifikátor uživatele ve vaší organizaci jako protokol IDP (SSO Internet Datagram Protocol), spolupracujte s [týmem podpory IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw).

    Ve výchozím nastavení Azure AD nastaví identifikátor uživatele jako hlavní název uživatele (UPN) hodnotu. Tuto hodnotu můžete změnit na kartě **Atributy uživatele,** jak je znázorněno na následujícím snímku obrazovky. Integrace funguje až po dokončení mapování správně.

    ![image](common/edit-attribute.png)

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavit IBM Kenexa Survey Enterprise** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-ibm-kenexa-survey-enterprise-single-sign-on"></a>Konfigurace jednotného přihlašování ibm Kenexa Survey Enterprise

Chcete-li nakonfigurovat jednotné přihlašování na straně **IBM Kenexa Survey Enterprise,** musíte odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z portálu Azure do [týmu podpory IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw). Toto nastavení nastaví tak, aby bylo připojení s přizasazené k samovazbě SAML správně nastaveno na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** typ pole**brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k IBM Kenexa Survey Enterprise.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **IBM Kenexa Survey Enterprise**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **položku IBM Kenexa Survey Enterprise**.

    ![Odkaz IBM Kenexa Survey Enterprise v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-ibm-kenexa-survey-enterprise-test-user"></a>Vytvoření testovacího uživatele IBM Kenexa Survey Enterprise

V této části vytvoříte uživatele s názvem Britta Simon v IBM Kenexa Survey Enterprise.

Chcete-li vytvořit uživatele v systému IBM Kenexa Survey Enterprise a namapovat pro ně ID spřimit správě osobních a soc, můžete spolupracovat s [týmem podpory IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw). Tato hodnota ID služby Při šití při microsoftmuž by měla být také mapována na hodnotu identifikátoru uživatele z Azure AD. Toto výchozí nastavení můžete změnit na kartě **Atribut.**

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici IBM Kenexa Survey Enterprise na přístupovém panelu, měli byste být automaticky přihlášeni k IBM Kenexa Survey Enterprise, pro které nastavíte sociální přihlašující. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

