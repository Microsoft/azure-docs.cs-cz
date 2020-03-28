---
title: 'Kurz: Integrace Azure Active Directory s SAP Business ByDesign | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP Business ByDesign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b830f0760b768826b2d937b4a8b2ffbd8e9e2a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091669"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Kurz: Integrace Azure Active Directory se službou SAP Business ByDesign

V tomto kurzu se dozvíte, jak integrovat SAP Business ByDesign s Azure Active Directory (Azure AD).
Integrace SAP Business ByDesign s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, kdo má přístup k SAP Business ByDesign.
* Uživatelům můžete povolit automatické přihlášení k SAP Business ByDesign (Jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s SAP Business ByDesign, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Předplatné s podporou jednotného přihlášení SAP Business ByDesign

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SAP Business ByDesign podporuje **sp** inicioval SSO

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Přidání SAP Business ByDesign z galerie

Chcete-li nakonfigurovat integraci SAP Business ByDesign do Azure AD, musíte přidat SAP Business ByDesign z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat SAP Business ByDesign z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SAP Business ByDesign**, z panelu výsledků vyberte **SAP Business ByDesign** a pak klepněte na **tlačítko Přidat** a přidejte aplikaci.

    ![Sap Business ByDesign v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí sap business bydesign na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v sap business bydesign.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD pomocí SAP Business ByDesign, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování SAP Business ByDesign](#configure-sap-business-bydesign-single-sign-on)** – pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu SAP Business ByDesign](#create-sap-business-bydesign-test-user)** – chcete-li mít protějšek Britty Simonové v sap business bydesignu, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí sap business bydesign, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **SAP Business ByDesign** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Sap Business ByDesign Domény a adresy URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<servername>.sapbydesign.com`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory sap business bydesign klienta.](https://www.sap.com/products/cloud-analytics.support.html) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Aplikace SAP Business ByDesign očekává kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Hodnoty těchto atributů můžete spravovat v části **Atributy uživatele** na stránce integrace aplikace. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na **tlačítko Upravit** otevřete dialogové okno **Atributy uživatele.**

    ![image](common/edit-attribute.png)

6. Kliknutím na ikonu **Upravit** upravte **hodnotu identifikátoru názvu**.

    ![image](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. V části **Spravovat deklarace identity** ![uživatelů proveďte následující kroky: obrázek](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Vyberte **transformaci** jako **zdroj**.

    b. V rozevíracím seznamu **Transformace** vyberte **ExtractMailPrefix()**.

    c. V rozevíracím seznamu **Parametr 1** vyberte atribut uživatele, který chcete použít pro implementaci. Chcete-li například použít id zaměstnance jako jedinečný identifikátor uživatele a uložili jste hodnotu atributu v atributu ExtensionAttribute2, vyberte user.extensionattribute2.

    d. Klikněte na **Uložit**.

8. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

9. V části **Nastavit sap business bydesign** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-sap-business-bydesign-single-sign-on"></a>Konfigurace jednotného přihlášení sap business bydesign

1. Přihlaste se na portál SAP Business ByDesign s právy správce.

2. Přejděte na **běžný úkol správy aplikací a uživatelů** a klikněte na kartu Zprostředkovatel **identity.**

3. Klikněte na **Nový zprostředkovatel identity** a vyberte soubor XML metadat, který jste stáhli z webu Azure Portal. Importem metadat systém automaticky nahraje požadovaný podpisový certifikát a šifrovací certifikát.

    ![Konfigurace jednotného přihlašování](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Chcete-li do požadavku SAML zahrnout **adresu URL služby Assertion Consumer Service,** vyberte **možnost Zahrnout adresu URL služby Assertion Consumer Service**.

5. Klepněte **na tlačítko Aktivovat jednotné přihlašování**.

6. Uložte provedené změny.

7. Klikněte na kartu **Můj systém.**

    ![Konfigurace jednotného přihlašování](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. V textovém poli **Adresy URL přihlášení k službě Azure AD Přihlásit** **se** vložte, kterou jste zkopírovali z webu Azure Portal.

    ![Konfigurace jednotného přihlašování](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Určete, zda zaměstnanec může ručně vybrat mezi přihlášením pomocí ID uživatele a hesla nebo s nastavením uživatelského přihlašování výběrem **ručního výběru zprostředkovatele identity**.

10. V části **Adresa URL služby ASO** zadejte adresu URL, kterou má zaměstnanec použít k přihlášení do systému.
    V rozevíracím seznamu URL odeslaná zaměstnanci můžete zvolit mezi následujícími možnostmi:

    **Adresa URL jiného než služby SSO**

    Systém odešle zaměstnanci pouze normální systémovou adresu URL. Zaměstnanec se nemůže přihlásit pomocí přihlašování a místo toho musí použít heslo nebo certifikát.

    **Adresa URL při stezace**

    Systém odešle pouze adresu URL služby SSO zaměstnanci. Zaměstnanec se může přihlásit pomocí služby Přihlašování. Požadavek na ověření je přesměrován prostřednictvím protokolu IdP.

    **Automatický výběr**

    Pokud sso není aktivní, systém odešle normální adresu URL systému zaměstnance. Pokud je aktivní spřijit, systém zkontroluje, zda má zaměstnanec heslo. Pokud je k dispozici heslo, adresu URL služby SSO i adresu URL nesouvisející s přihlašuje se zaměstnanci. Pokud však zaměstnanec nemá žádné heslo, je zaměstnanci odeslána pouze adresa URL služby SSO.

11. Uložte provedené změny.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské** `brittasimon@yourcompanydomain.extension`jméno typ pole . Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k SAP Business ByDesign.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte SAP **Business ByDesign**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **SAP Business ByDesign**.

    ![Odkaz SAP Business ByDesign v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-sap-business-bydesign-test-user"></a>Vytvořit testovacího uživatele SAP Business ByDesign

V této části vytvoříte uživatele s názvem Britta Simon v SAP Business ByDesign. Pro přidání uživatelů do platformy SAP Business ByDesign můžete spolupracovat s [týmem podpory SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) Client. 

> [!NOTE]
> Ujistěte se, že hodnota NameID by se měla shodovat s polem uživatelského jména na platformě SAP Business ByDesign.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když klepnete na dlaždici SAP Business ByDesign na přístupovém panelu, měli byste být automaticky přihlášeni k sap business bydesign, pro který nastavíte přispojené k zámečník. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
