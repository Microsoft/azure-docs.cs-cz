---
title: 'Kurz: Integrace služby Azure Active Directory se službou Vidyard | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Vidyardem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bed7df23-6e13-4e7c-b4cc-53ed4804664d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: a55ec7afc94440ea8b6a48ed1507476d362df6c0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67087422"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Kurz: Integrace služby Azure Active Directory se službou Vidyard

V tomto kurzu se dozvíte, jak integrovat Vidyard s Azure Active Directory (Azure AD).
Integrace Vidyardu s Azure AD vám přináší následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Vidyard.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k Vidyardu (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Vidyard, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Předplatné s povoleným jedním přihlášením Vidyard

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Vidyard podporuje **SP** a **IDP** inicioval SSO

* Vidyard podporuje zřizování uživatelů **Just In Time**

## <a name="adding-vidyard-from-the-gallery"></a>Přidání Vidyard z galerie

Pokud chcete nakonfigurovat integraci Vidyardu do Azure AD, musíte přidat Vidyard z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Vidyard z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Vidyard**, z panelu výsledků vyberte **Vidyard** a klepnutím na tlačítko **Přidat** přidejte aplikaci.

     ![Vidyard v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Vidyardu na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem ve Vidyardu.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby Vidyard, je třeba provést následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednorázového přihlašování Vidyard](#configure-vidyard-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte vidyard test uživatele](#create-vidyard-test-user)** – mít protějšek Britta Simon ve Vidyard, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí služby Vidyard, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Vidyard** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující kroky:

    ![Vidyard Doména a adresy URL jednotné přihlašovací informace](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://secure.vidyard.com/sso/saml/<unique id>/consume`

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![Vidyard Doména a adresy URL jednotné přihlašovací informace](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujete pomocí skutečného identifikátoru, adresy URL odpovědi a adresy URL přihlášení, což je vysvětleno dále v kurzu. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavit Vidyard** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-vidyard-single-sign-on"></a>Konfigurace jednotného přihlašování Vidyard

1. V jiném okně webového prohlížeče se přihlaste na web společnosti Vidyard Software jako správce.

2. Na řídicím panelu Vidyard vyberte**Zabezpečení** **skupiny** > 

    ![Konfigurace Vidyard](./media/vidyard-tutorial/configure1.png)

3. Klikněte na kartu **Nový profil.**

    ![Konfigurace Vidyard](./media/vidyard-tutorial/configure2.png)

4. V části **Konfigurace SAML** proveďte následující kroky:

    ![Konfigurace Vidyard](./media/vidyard-tutorial/configure3.png)

    a. Do textového pole **Název profilu** zadejte obecný název profilu.

    b. Zkopírujte hodnotu **přihlašovací stránky uživatele při přihlašování k přihlašování** a vložte ji do textového pole **Přihlásit se na adresu URL** v části Základní konfigurace **SAML** na webu Azure Portal.

    c. Zkopírujte hodnotu **adresy URL služby ACS** a vložte ji do textového pole **pro odpověď** v části Základní **konfigurace SAML** na webu Azure Portal.

    d. Zkopírujte hodnotu **adresy URL vystavitele/metadat** a vložte ji do textového pole **Identifikátor** v části Základní **konfigurace SAML** na webu Azure Portal.

    e. Otevřete stažený soubor certifikátu z portálu Azure v poznámkovém bloku a vložte ho do textového pole **Certifikát X.509.**

    f. V textovém poli **adresy URL koncového bodu SAML** vložte hodnotu **přihlašovací adresy URL** zkopírované z webu Azure Portal.

    g. Klikněte na **Confirm** (Potvrdit).

5. Na kartě Jednotné přihlášení vyberte **Přiřadit** vedle existujícího profilu.

    ![Konfigurace Vidyard](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > Jakmile vytvoříte profil přistupujícího objektu, přiřaďte ho všem skupinám, pro které budou uživatelé vyžadovat přístup prostřednictvím Azure. Pokud uživatel ve skupině, ke které byl přiřazen, neexistuje, Vidyard automaticky vytvoří uživatelský účet a přiřadí svou roli v reálném čase.

6. Vyberte skupinu organizace, která je viditelná ve **skupinách, které jsou k dispozici pro přiřazení**.

    ![Konfigurace Vidyard](./media/vidyard-tutorial/configure5.png)

7. Přiřazené skupiny můžete zobrazit v části **Aktuálně přiřazené skupiny**. Vyberte roli pro skupinu podle vaší organizace a klepněte na **tlačítko Potvrdit**.

    ![Konfigurace Vidyard](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Další informace naleznete v [tomto dokumentu](https://knowledge.vidyard.com/hc/articles/360009990033-SAML-based-Single-Sign-On-SSO-in-Vidyard).

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k Vidyard.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Vidyard**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Vidyard**.

    ![Odkaz Vidyard v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-vidyard-test-user"></a>Vytvořit testovacího uživatele Vidyard

V této části je uživatel s názvem Britta Simon vytvořen ve Vidyardu. Vidyard podporuje zřizování uživatelů just-in-time, které je ve výchozím nastavení povoleno. V této části pro vás není žádná položka akce. Pokud uživatel ve Vidyardu ještě neexistuje, vytvoří se po ověření nový uživatel.

>[!Note]
>Pokud potřebujete vytvořit uživatele ručně, obraťte se na [tým podpory společnosti Vidyard](mailto:support@vidyard.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Vidyard na přístupovém panelu, měli byste být automaticky přihlášeni k Vidyard, pro které nastavíte přiřazovací příkaz. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

