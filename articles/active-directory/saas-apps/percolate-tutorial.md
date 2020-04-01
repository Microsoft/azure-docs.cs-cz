---
title: 'Kurz: Integrace služby Azure Active Directory s percolate | Dokumenty společnosti Microsoft'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Percolate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 355f9659-b378-44c9-aa88-236e9b529a53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: a6c1f893757baf1e6c85420b31997a5073cff684
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094600"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Kurz: Integrace služby Azure Active Directory s percolate

V tomto kurzu se dozvíte, jak integrovat Percolate s Azure Active Directory (Azure AD).

Tato integrace poskytuje tyto výhody:

* Azure AD můžete použít k řízení, kdo má přístup k Percolate.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k Percolate (jednotné přihlašování) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění: na portálu Azure.

Další informace o integraci aplikací SaaS s Azure AD najdete [v tématu Jednotné přihlašování k aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Percolate, musíte mít:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Percolate předplatné, které má jednotné přihlášení povoleno.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu budete konfigurovat a testovat jednotné přihlašování Azure AD v testovacím prostředí.

* Percolate podporuje sp-iniciované a IdP iniciované sso.

## <a name="add-percolate-from-the-gallery"></a>Přidat Percolate z galerie

Chcete-li nakonfigurovat integraci Percolate do Azure AD, musíte přidat Percolate z galerie do seznamu spravovaných aplikací SaaS.

1. Na [webu Azure Portal](https://portal.azure.com)v levém podokně vyberte **Službu Azure Active Directory**:

    ![Vyberte Azure Active Directory.](common/select-azuread.png)

2. Přejít na **podnikové aplikace** > **Všechny aplikace**:

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte v horní části okna možnost **Nová aplikace:**

    ![Vybrat novou aplikaci](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Percolate**. Ve výsledcích hledání vyberte **Prosakovat** a pak **vyberte Přidat**.

     ![Výsledky hledání](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí percolate pomocí testovacího uživatele s názvem Britta Simon.
Chcete-li povolit jednotné přihlašování, musíte vytvořit vztah mezi uživatelem Azure AD a odpovídajícím uživatelem v Percolate.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí percolate, musíte provést tyto kroky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** tak, aby byla tato funkce povolena pro vaše uživatele.
2. **[Nakonfigurujte percolate jednotné přihlašování na](#configure-percolate-single-sign-on)** straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlášení Azure AD.
4. **[Přiřaďte testovacímu uživateli Azure AD,](#assign-the-azure-ad-test-user)** aby uživatel povolil jednotné přihlašování Azure AD.
5. **[Vytvořte test percolate uživatele,](#create-a-percolate-test-user)** který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlášení](#test-single-sign-on)** a ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí percolate, postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce Integrace **percolate** aplikací vyberte **Jednotné přihlašování**:

    ![Vybrat jednotné přihlašování](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování:

    ![Výběr metody jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** vyberte ikonu **Upravit** a otevřete dialogové okno Základní **konfigurace SAML:**

    ![Ikona úprav](common/edit-urls.png)

4. V dialogovém okně **Základní konfigurace SAML** není nutné provádět žádnou akci ke konfiguraci aplikace v režimu iniciovaném protokolem IdP. Aplikace je už integrovaná s Azure.

    ![Percolate Doména a adresy URL jednotné přihlašovací informace](common/preintegrated.png)

5. Pokud chcete aplikaci nakonfigurovat v režimu iniciovaném pomocí aktualizace SP, vyberte **Nastavit další adresy URL** a do pole **Přihlásit se na adresu URL** zadejte **https://percolate.com/app/login**:

   ![Percolate Doména a adresy URL jednotné přihlašovací informace](common/metadata-upload-additional-signon.png)
6. Na stránce **Nastavit jednotné přihlašování pomocí saml** vyberte v části **Podpisový certifikát SAML** ikonu **Kopírovat,** chcete-li zkopírovat **adresu URL metadat federace aplikací**. Uložte tuto adresu URL.

    ![Kopírování adresy URL metadat federace aplikací](common/copy-metadataurl.png)

7. V části **Nastavit percolate** zkopírujte příslušné adresy URL na základě vašich požadavků.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    1. **Adresa URL přihlášení**.

    1. **Identifikátor služby Azure .**

    1. **Adresa URL pro odhlášení**.

### <a name="configure-percolate-single-sign-on"></a>Konfigurace jednotného přihlašování percolate

1. V novém okně webového prohlížeče se přihlaste jako správce.

2. Na levé straně domovské stránky vyberte **Nastavení**:
    
    ![Nastavení](./media/percolate-tutorial/configure01.png)

3. V levém podokně vyberte v části **Organizace**vyberte **sso SSO** :

    ![Vyberte sso v části Organizace](./media/percolate-tutorial/configure02.png)

    1. Do pole **Přihlašovací adresa URL** vložte hodnotu přihlašovací adresy **URL,** kterou jste zkopírovali z webu Azure Portal.

    1. Do pole **ID entity** vložte hodnotu **identifikátoru Azure AD,** kterou jste zkopírovali z webu Azure Portal.

    1. V poznámkovém bloku otevřete certifikát kódovaný base-64, který jste stáhli z webu Azure Portal. Zkopírujte jeho obsah a vložte jej do pole **certifikáty x509.**

    1. Do pole **Atribut E-mail** zadejte **e-mailovou adresu**.

    1. Pole **URL metadat poskytovatele identity** je volitelné pole. Pokud jste zkopírovali **adresu URL metadat federace aplikací** z webu Azure Portal, můžete ji vložit do tohoto pole.

    1. V seznamu **Má být AuthNRequests podepsán?** **No**

    1. V seznamu **Povolit automatické zřizování při suso** vyberte **ne**.

    1. Vyberte **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal vyberte v levém podokně **Službu Azure Active Directory,** vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**:

    ![Vyberte Všichni uživatelé.](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel:**

    ![Vybrat nového uživatele](common/new-user.png)

3. V dialogovém okně **Uživatel** proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    1. Do pole **Název** zadejte **BrittaSimon**.
  
    1. Do pole **Uživatelské jméno** zadejte **BrittaSimon@\<\<> vaší firemní domény. prodloužení>**. (Například BrittaSimon@contoso.com.)

    1. Vyberte **Zobrazit heslo**a poznamenejte si hodnotu, která je v poli **Heslo.**

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure AD jednotné přihlášení tím, že jí přístup k Percolate.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Percolate**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Percolate**.

    ![Seznam žádostí](common/all-applications.png)

3. V levém podokně vyberte **Uživatelé a skupiny**:

    ![Vyberte Uživatelé a skupiny.](common/users-groups-blade.png)

4. V **dialogovém okně Přidat** **přiřazení** vyberte Přidat uživatele a pak vyberte **Uživatelé a skupiny.**

    ![Vyberte Uživatelé a skupiny.](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu Uživatelé **Brittu Simonovou** a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu. Klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** vyberte **Přiřadit**.

### <a name="create-a-percolate-test-user"></a>Vytvoření testovacího uživatele percolate

Chcete-li povolit uživatelům Azure AD k přihlášení k Percolate, musíte je přidat do Percolate. Musíte je přidat ručně.

Chcete-li vytvořit uživatelský účet, postupujte takto:

1. Přihlaste se jako správce.

2. V levém podokně vyberte **položku Uživatelé v** části **Organizace**. Vybrat **nové uživatele**:

    ![Vybrat nové uživatele](./media/percolate-tutorial/configure03.png)

3. Na stránce **Vytvořit uživatele** postupujte takto.

    ![Stránka Vytvořit uživatele](./media/percolate-tutorial/configure04.png)

    1. Do pole **E-mail** zadejte e-mailovou adresu uživatele. Například, brittasimon@contoso.com.

    1. Do pole **Celé jméno** zadejte jméno uživatele. Například **Brittasimon**.

    1. Vyberte **Vytvořit uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Teď je potřeba otestovat konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když vyberete dlaždici Percolate na přístupovém panelu, měli byste být automaticky přihlášeni k instanci Percolate, pro kterou nastavíte přispojené k zámečník. Další informace najdete v [tématu Přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Kurzy integrace aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)