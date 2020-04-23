---
title: 'Kurz: Integrace služby Azure Active Directory s hr2day společností Merces | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a HR2day od společnosti Merces.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 853d08c9-27b1-48d4-b8e7-3705140eb67f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 5b9de6c2b8d9f7b11b686e820aab54fdd8e8a02f
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866803"
---
# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>Kurz: Integrace azure active directory s HR2day by Merces

V tomto kurzu se dozvíte, jak integrovat HR2day od Merces s Azure Active Directory (Azure AD).
Integrace HR2day společností Merces s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k HR2day od Merces.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k HR2day společností Merces (Jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadované součásti

Chcete-li nakonfigurovat integraci Azure AD s HR2day společností Merces, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* HR2day by Merces single sign-on enabled subscription HR2day by Merces single sign-on enabled subscription HR2day by Merces single sign-on enabled subscription HR2

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* HR2day by Merces podporuje **sp** inicioval SSO

## <a name="adding-hr2day-by-merces-from-the-gallery"></a>Přidání HR2day od Merces z galerie

Chcete-li nakonfigurovat integraci HR2day společností Merces do Azure AD, musíte přidat HR2day by Merces z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat HR2day od Merces z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Ve vyhledávacím poli zadejte **HR2day by Merces**, zpanelovacího panelu výsledků vyberte **HR2day by Merces** a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![HR2day od Merces v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí hr2day společností Merces na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v HR2day by Merces.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD s HR2day by Merces, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace HR2day by Merces Single Sign-On](#configure-hr2day-by-merces-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte HR2day uživatele testu Merces](#create-hr2day-by-merces-test-user)** – chcete-li mít protějšek Britta Simon v HR2day od Merces, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí hr2day od společnosti Merces, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **HR2day by Merces** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![HR2day by Merces Domain and URLs single sign-on information HR2day by Merces Domain and URLs single sign-on information HR2day by Merces Domain and URLs single sign-on information HR2day by Merc](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<tenantname>.force.com/<instancename>`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://hr2day.force.com/<companyname>`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Kontaktujte [hr2day tým emitované hod.S. Hr2day by Merces Client support team](mailto:servicedesk@merces.nl) to get these values. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Vaše aplikace HR2day by Merces očekává kontrolní výrazy SAML v určitém formátu, který vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky zobrazuje seznam výchozích atributů. Kliknutím na ikonu **Upravit** otevřete dialogové okno **Atributy uživatele.**

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Před konfigurací kontrolního výrazu SAML musíte kontaktovat [tým podpory HR2day by Merces Client](mailto:servicedesk@merces.nl) a požádat o hodnotu atributu jedinečného identifikátoru pro vašeho tenanta. Tuto hodnotu potřebujete k dokončení kroků v další části.

6. V části **Deklarace identity uživatelů** v dialogovém okně **Atributy uživatele** upravte deklarace pomocí **ikony Upravit** nebo přidejte deklarace pomocí funkce Přidat **novou deklaraci** pro konfiguraci atributu tokenu SAML, jak je znázorněno na obrázku výše, a proveďte následující kroky:

    | Název |  Atribut zdroje |
    | ---------- | ----------- |
    | ATTR_LOGINCLAIM | `join([mail],"102938475Z","@"` |
    | | |

    a. Kliknutím na **Přidat novou deklaraci** otevřete dialogové okno **Spravovat deklarace identity uživatelů.**

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **Název** zadejte název atributu zobrazený pro daný řádek.

    c. Ponechejte **obor názvů** prázdný.

    d. Vyberte zdroj jako **atribut**.

    e. Ze seznamu **atributů Zdroj** zadejte hodnotu atributu zobrazenou pro daný řádek.

    f. Klikněte na **Ok.**

    g. Klikněte na **Uložit**.

7. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

8. V části **Nastavit HR2day by Merces** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-hr2day-by-merces-single-sign-on"></a>Konfigurace hr2day podle Merces Single Sign-On

Chcete-li nakonfigurovat jednotné přihlašování na **STRANĚ HR2day podle merces,** musíte odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z portálu Azure do [hr2day týmem podpory Společnosti Merces](mailto:servicedesk@merces.nl). Toto nastavení nastaví tak, aby bylo připojení s přizasazené k samovazbě SAML správně nastaveno na obou stranách.

> [!NOTE]
> Uveďte týmu Merces, že tato integrace potřebuje ID entity, které má být nastaveno se vzorem **\/https: /hr2day.force.com/INSTANCENAME**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** typ pole`brittasimon@yourcompanydomain.extension`  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k HR2day merces.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **HR2day by Merces**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **HR2day by Merces**.

    ![Odkaz HR2day by Merces v seznamu Aplikací](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-hr2day-by-merces-test-user"></a>Vytvořit HR2day od uživatele testu Merces

V této části vytvoříte uživatele s názvem Britta Simon v HR2day od Merces. Spolupracujte s [týmem podpory HR2day by Merces](mailto:servicedesk@merces.nl) a přidejte uživatele na platformě HR2day by Merces. Uživatelé musí být vytvořena a aktivována před použitím jednotného přihlášení.

> [!NOTE]
> Pokud potřebujete vytvořit uživatele ručně, obraťte se na [hr2day by Merces týmu podpory klienta](mailto:servicedesk@merces.nl).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici HR2day by Merces na přístupovém panelu, měli byste být automaticky přihlášeni k HR2day od Merces, pro které nastavíte sso. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
