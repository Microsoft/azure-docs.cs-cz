---
title: 'Kurz: Integrace služby Azure Active Directory s SpringCM | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a SpringCM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4a42f797-ac58-4aca-a8e6-53bfe5529083
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: be4edfe3262ab74d903384eb00c0282acc7a3299
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867431"
---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>Kurz: Integrace služby Azure Active Directory s SpringCM

V tomto kurzu se dozvíte, jak integrovat SpringCM s Azure Active Directory (Azure AD).
Integrace SpringCM s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k SpringCM.
* Můžete povolit uživatelům, aby se automaticky přihlásili k SpringCM (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadované součásti

Chcete-li nakonfigurovat integraci Azure AD s SpringCM, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Předplatné s povoleným jedním přihlášením SpringCM

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SpringCM podporuje **sp** inicioval sso

## <a name="adding-springcm-from-the-gallery"></a>Přidání SpringCM z galerie

Chcete-li nakonfigurovat integraci SpringCM do Azure AD, musíte přidat SpringCM z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat SpringCM z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SpringCM**, z panelu výsledků vyberte **SpringCM** a pak klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![SpringCM v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí SpringCM na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v SpringCM.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí SpringCM, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace single sign-on SpringCM](#configure-springcm-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte springcm testovací ho uživatele](#create-springcm-test-user)** – mít protějšek Britta Simon v SpringCM, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí springcm, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **SpringCM** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Informace o doméně springcm a adresách URL s jedním přihlášením](common/sp-signonurl.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<identifier>`

    > [!NOTE]
    > Hodnota není skutečná. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Obraťte se na [tým podpory klienta SpringCM](https://knowledge.springcm.com/support) získat hodnotu. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Nezpracovaný)** z daných možností a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificateraw.png)

6. V části **Nastavit SpringCM** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-springcm-single-sign-on"></a>Konfigurace jednotného přihlášení SpringCM

1. V jiném okně webového prohlížeče se přihlaste na web společnosti **SpringCM** jako správce.

1. V nabídce v horní části klikněte na **PŘEJÍT na**, klikněte na **Předvolby**a potom v části **Předvolby účtu** klikněte na **SAML SSO**.
   
    ![SAML SSO](./media/spring-cm-tutorial/ic797051.png "SAML SSO")

1. V části Konfigurace zprostředkovatele identity proveďte následující kroky:
   
    ![Konfigurace zprostředkovatele identity](./media/spring-cm-tutorial/ic797052.png "Konfigurace zprostředkovatele identity")
    
    a. Chcete-li nahrát stažený certifikát služby Azure Active Directory, klepněte na tlačítko **Vybrat certifikát vystavitho služby** nebo **změnit certifikát vystavitho služby**.
    
    b. Do textového pole **Vystaviter** vložte hodnotu **identifikátoru Azure AD,** kterou jste zkopírovali z webu Azure Portal.
    
    c. V textovém poli **Iniciovaný koncový bod poskytovatele služeb (SP)** vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z portálu Azure.
            
    d. Jako povolit vyberte **možnost Povoleno saml.** **Enable**

    e. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu springcm.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak **springcm**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **SpringCM**.

    ![Odkaz SpringCM v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-springcm-test-user"></a>Vytvořit testovacího uživatele SpringCM

Chcete-li povolit uživatelům služby Azure Active Directory k přihlášení k SpringCM, musí být zřízena do SpringCM. V případě SpringCM zřizování je ruční úlohy.

> [!NOTE]
> Další informace naleznete v [tématu Vytvoření a úprava uživatele SpringCM](http://community.springcm.com/s/article/Create-and-Edit-a-SpringCM-User-1619481053). 

**Chcete-li zřídit uživatelský účet SpringCM, proveďte následující kroky:**

1. Přihlaste se k webu společnosti **SpringCM** jako správce.

1. Klepněte na **položku GOTO**a potom na **položku ADRESÁŘ**.
   
    ![Vytvořit uživatele](./media/spring-cm-tutorial/ic797054.png "Vytvořit uživatele")

1. Klepněte na **tlačítko Vytvořit uživatele**.

1. Vyberte **roli uživatele**.

1. Vyberte **Odeslat aktivační e-mail**.

1. Zadejte jméno, příjmení a e-mailovou adresu platného uživatelského účtu služby Azure Active Directory, který chcete zřídit do souvisejících textových polí.

1. Přidejte uživatele do **skupiny zabezpečení**.

1. Klikněte na **Uložit**.

   > [!NOTE]
   > Můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů SpringCM nebo rozhraní API poskytované SpringCM zřídit uživatelské účty Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici SpringCM na přístupovém panelu, můžete by měl být automaticky přihlášeni k SpringCM, u kterého nastavíte přisuzuje. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály a zdroje informací

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

