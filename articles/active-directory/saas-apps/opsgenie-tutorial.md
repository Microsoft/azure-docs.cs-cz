---
title: 'Kurz: Integrace služby Azure Active Directory s OpsGenie | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a OpsGenie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 446ac54d84f7b2b3bf3aaf6eaf5536f0dfb804fe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095752"
---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Kurz: Integrace služby Azure Active Directory s OpsGenie

V tomto kurzu se dozvíte, jak integrovat OpsGenie s Azure Active Directory (Azure AD).
Integrace OpsGenie s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k OpsGenie.
* Můžete povolit uživatelům, aby se automaticky přihlásili k OpsGenie (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s OpsGenie, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné opsGenie s povoleným jedním přihlášením

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* OpsGenie podporuje **SP** iniciované SSO

## <a name="adding-opsgenie-from-the-gallery"></a>Přidání OpsGenie z galerie

Chcete-li nakonfigurovat integraci OpsGenie do Azure AD, musíte přidat OpsGenie z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat OpsGenie z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **OpsGenie**, z panelu výsledků vyberte **OpsGenie** a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![OpsGenie v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí OpsGenie na základě testovacího uživatele s názvem **B. Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v OpsGenie.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí OpsGenie, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování OpsGenie](#configure-opsgenie-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s B. Simon.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li b. Simonovi povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu OpsGenie](#create-opsgenie-test-user)** – chcete-li mít protějšek B. Simon v OpsGenie, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí OpsGenie, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte na stránce integrace aplikací **OpsGenie** **možnost Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![OpsGenie Domény a adresy URL jednotné přihlašovací informace](common/sp-signonurl.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL:`https://app.opsgenie.com/auth/login`

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na tlačítko Kopírovat, chcete-li zkopírovat **adresu URL metadat federace aplikací** a uložit ji do počítače.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

6. V části **Nastavit OpsGenie** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-opsgenie-single-sign-on"></a>Konfigurace jednotného přihlašování OpsGenie

1. Otevřete jinou instanci prohlížeče a přihlaste se k OpsGenie jako správce.

2. Klikněte na **Nastavení**a potom klikněte na kartu **Jednotné přihlášení.**
   
    ![Jednotné přihlašování OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_06.png)

3. Chcete-li povolit službu SSO, vyberte **Možnost Povoleno**.
   
    ![Operační nastavení](./media/opsgenie-tutorial/tutorial_opsgenie_07.png) 

4. V části **Zprostředkovatel** klikněte na kartu **Azure Active Directory.**
   
    ![Operační nastavení](./media/opsgenie-tutorial/tutorial_opsgenie_08.png) 

5. Na stránce dialogového okna Služby Azure Active Directory proveďte následující kroky:
   
    ![Operační nastavení](./media/opsgenie-tutorial/tutorial_opsgenie_09.png)
    
    a. V textovém poli **koncový bod SAML 2.0** vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z portálu Azure.
    
    b. V textovém poli **Url metadat:** vložte hodnotu **url metadat federace aplikace,** kterou jste zkopírovali z portálu Azure.
    
    c. Klikněte na **Save Changes** (Uložit změny).

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem B. Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **B. Simon**.
  
    b. V poli **Uživatelské jméno** typ pole**bsimon@yourcompanydomain.extension**  
    Například BSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat Azure jednotné přihlašování udělením přístupu k OpsGenie.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **OpsGenie**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **OpsGenie**.

    ![Odkaz OpsGenie v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **b. Simon** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-opsgenie-test-user"></a>Vytvořit uživatele opsGenie testu

Cílem této části je vytvořit uživatele s názvem B. Simon v OpsGenie. 

1. V okně webového prohlížeče se přihlaste ke svému tenantovi OpsGenie jako správce.

2. Kliknutím na **Položku Uživatelé** v levém panelu přejděte do seznamu Uživatelé.
   
    ![Operační nastavení](./media/opsgenie-tutorial/tutorial_opsgenie_10.png) 

3. Klikněte na **Add User** (Přidat uživatele).

4. V dialogovém okně **Přidat uživatele** proveďte následující kroky:
   
    ![Operační nastavení](./media/opsgenie-tutorial/tutorial_opsgenie_11.png)
   
    a. Do textového pole **E-mail** zadejte e-mailovou adresu B. Simona adresovo v Azure Active Directory.
   
    b. Do textového pole **Celé jméno** zadejte **B. Simon**.
   
    c. Klikněte na **Uložit**. 

>[!NOTE]
>B. Simon dostane e-mail s pokyny pro nastavení jejich profilu.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici OpsGenie na přístupovém panelu, by měl být automaticky přihlášeni k OpsGenie, pro které nastavíte přihlašující. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

