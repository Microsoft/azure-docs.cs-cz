---
title: 'Kurz: Integrace služby Azure Active Directory s AnswerHubem | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a AnswerHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a124832bd42a0a144ebc6000b818fb825aa422ef
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73152984"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Kurz: Integrace Azure Active Directory s AnswerHubem

V tomto kurzu se dozvíte, jak integrovat AnswerHub s Azure Active Directory (Azure AD).
Integrace AnswerHubu s Azure AD poskytuje tyto výhody:

* Azure AD můžete použít k řízení, kdo má přístup k AnswerHub.
* Uživatelům můžete automaticky přihlašovat k AnswerHubu pomocí jejich účtů Azure AD (jednotné přihlašování).
* Své účty můžete spravovat z centrálního umístění: na webu Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete [v tématu Jednotné přihlašování k aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí AnswerHubu, potřebujete následující:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete začít [měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Předplatné AnswerHub, které má povoleno jednotné přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* AnswerHub podporuje sp-iniciované přidopravních.

## <a name="add-answerhub-from-the-gallery"></a>Přidání AnswerHubu z galerie

Pokud chcete nastavit integraci AnswerHubu do Azure AD, musíte přidat AnswerHub z galerie do spravovaných aplikací SaaS.

**Přidání AnswerHubu z galerie:**

1. Na [webu Azure Portal](https://portal.azure.com)v levém podokně vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **položku Podnikové aplikace**a vyberte **možnost Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte v horní části okna možnost **Nová aplikace.**

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **AnswerHub**. V seznamu výsledků vyberte **AnswerHub** a pak vyberte **Přidat**.

     ![AnswerHub v seznamu výsledků](common/search-new-app.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Nastavení a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí AnswerHub pomocí testovacího uživatele s názvem Britta Simon.
Pro jednotné přihlašování, musíte vytvořit propojení mezi uživatelem Azure AD a odpovídající uživatel v AnswerHub.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí AnswerHubu, musíte dokončit tyto úkoly:

1. [Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on) tak, aby uživatelé mohli tuto funkci používat.
2. [Nakonfigurujte jednotné přihlašování AnswerHub](#configure-answerhub-single-sign-on) k nastavení nastavení jednotného přihlášení na straně aplikace.
3. [Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user) s názvem Britta Simon.
4. [Přiřaďte uživateli testu Azure AD,](#assign-the-azure-ad-test-user) aby britta Simon mohla používat jednotné přihlašování Azure AD.
5. Vytvořte uživatele testu AnswerHub, který odpovídá a je propojený s uživatelem testu Azure AD.
6. [Otestujte jednotné přihlášení](#test-single-sign-on) a ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části nastavíte jednotné přihlašování Azure AD na webu Azure Portal.

**Konfigurace jednotného přihlašování Azure AD pomocí Služby AnswerHub:**

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **AnswerHub** vyberte **Jednotné přihlašování**.

    ![Tlačítko pro jednotné přihlašování](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Dialogové okno Vybrat metodu jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** vyberte ikonu úprav a otevřete dialogové okno **Základní konfigurace SAML.**

    ![Nastavení stránky jednotného přihlášení pomocí saml](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Základní oddíl Konfigurace SAML](common/sp-identifier.png)

    a. Do pole **Přihlásit se na adresu URL** zadejte adresu URL, která má tento vzor:`https://<company>.answerhub.com`

    b. Do pole **Identifikátor (ID entity)** zadejte adresu URL s tímto vzorem:`https://<company>.answerhub.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Obraťte se na [tým podpory AnswerHub](mailto:success@answerhub.com) získat hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** vyberte v části **Podpisový certifikát SAML** odkaz **Ke stažení** vedle **certifikátu (Base64)** podle vašich požadavků a uložte certifikát do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit AnswerHub** zkopírujte příslušnou adresu URL nebo adresy URL na základě vašich požadavků.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

   Tyto adresy URL můžete zkopírovat:
    - Přihlašovací adresa URL

    - Identifikátor azure reklamy

    - Adresa URL odhlášení

### <a name="configure-answerhub-single-sign-on"></a>Konfigurace jednotného přihlašování AnswerHub

V této části nastavíte jednotné přihlašování pro AnswerHub.  

**Konfigurace jednotného přihlašování AnswerHub:**

1. V jiném okně webového prohlížeče se přihlaste k webu společnosti AnswerHub jako správce.

    > [!NOTE]
    > Pokud potřebujete pomoc s konfigurací AnswerHub, obraťte se na [tým podpory AnswerHub](mailto:success@answerhub.com.).

2. Přejděte na **správu**.

3. Na kartě **Uživatel a skupiny** vyberte v levém podokně v části **Nastavení sociálních sítí** nastavení **SAML**.

4. Na kartě **Konfigurace protokolu IDP** proveďte tyto kroky:

    ![Karta Uživatelé & skupiny](./media/answerhub-tutorial/ic785172.png "Nastavení SAML")  
  
    a. Do pole **Adresa URL přihlášení idp** vložte **přihlašovací adresu URL,** kterou jste zkopírovali z webu Azure Portal.
  
    b. Do pole **Adresa URL odhlášení idp** vložte **adresu URL odhlášení,** kterou jste zkopírovali z webu Azure Portal.

    c. Do pole **Formát identifikátoru názvu IDP** zadejte hodnotu **identifikátoru** vybranou v části **Atributy uživatele** na webu Azure Portal.
  
    d. Vyberte **klíče a certifikáty**.

5. V části **Klíče a certifikáty** postupujte takto:

    ![Oddíl Klíče a certifikáty](./media/answerhub-tutorial/ic785173.png "Klíče a certifikáty")  

    a. Otevřete certifikát kódovaný base64, který jste stáhli z portálu Azure v poznámkovém bloku, zkopírujte jeho obsah a vložte obsah do pole **IDP Public Key (x509 Format).**
  
    b. Vyberte **Uložit**.

6. Na kartě **Konfigurace protokolu IDP** vyberte **Uložit** znovu.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovací ho uživatele s názvem Britta Simon na webu Azure Portal.

**Vytvoření testovacího uživatele Azure AD:**

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Vyberte Službu Azure Active Directory, Uživatelé, Všichni uživatelé.](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko Nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte tyto kroky.

    ![Uživatelské vlastnosti](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. Do pole **Uživatelské jméno** zadejte **\@ brittasimon<>companydomain.extension **.  
    Například, BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli **Heslo.**

    d. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části nastavíte uživatele Britta Simon používat Azure AD jednotné přihlášení udělením přístupu uživatele AnswerHub.

**Přiřazení testovacího uživatele Azure AD:**

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **AnswerHub**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **AnswerHub**.

    ![Seznam aplikací](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Vyberte Uživatelé a skupiny.](common/users-groups-blade.png)

4. V **dialogovém okně Přidat** **přiřazení** vyberte Přidat uživatele a pak vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** **Brittu Simonovou** a v dolní části obrazovky vyberte tlačítko **Vybrat.**

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu. 

7. V dolní části obrazovky vyberte tlačítko **Vybrat.**

8. V dialogovém okně **Přidat přiřazení** vyberte **Přiřadit**.

### <a name="create-an-answerhub-test-user"></a>Vytvoření testovacího uživatele AnswerHub

Chcete-li povolit uživatelům Azure AD k přihlášení k AnswerHub, musíte je přidat v AnswerHub. V AnswerHub, tento úkol se provádí ručně.

**Nastavení uživatelského účtu:**

1. Přihlaste se k webu společnosti **AnswerHub** jako správce.

2. Přejděte na **správu**.

3. Vyberte kartu **Uživatelé & skupiny.**

4. V levém podokně vyberte v části **Spravovat uživatele** možnost Vytvořit **nebo importovat uživatele**a pak vyberte **Uživatelé & skupiny**.

   ![Karta Uživatelé & skupiny](./media/answerhub-tutorial/ic785175.png "Uživatelé & skupiny")

5. Do příslušných polí zadejte **e-mailovou adresu**, **uživatelské jméno**a **heslo** platného účtu Azure AD, který chcete přidat, a pak vyberte **Uložit**.

> [!NOTE]
> K nastavení uživatelských účtů Azure AD můžete použít libovolný jiný nástroj pro vytváření uživatelských účtů nebo rozhraní API poskytované answerhubem.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když vyberete dlaždici AnswerHub na přístupovém panelu, měli byste být automaticky přihlášeni k AnswerHub, pro které nastavíte přistupující připojení. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Kurzy pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

