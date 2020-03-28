---
title: 'Kurz: Integrace služby Azure Active Directory s InsideView | Dokumenty společnosti Microsoft'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a InsideView.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: jeedes
ms.openlocfilehash: 2149b8410104b39652b176895a31b42e094265f5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67100090"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Kurz: Integrace služby Azure Active Directory s InsideView

V tomto kurzu se dozvíte, jak integrovat InsideView s Azure Active Directory (Azure AD).
Tato integrace poskytuje tyto výhody:

* Azure AD můžete použít k řízení, kdo má přístup k InsideView.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k InsideView (jednotné přihlašování) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění: na portálu Azure.

Další informace o integraci aplikací SaaS s Azure AD najdete [v tématu Jednotné přihlašování k aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s InsideView, musíte mít:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné InsideView, které má povoleno jednotné přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu budete konfigurovat a testovat jednotné přihlašování Azure AD v testovacím prostředí.

* InsideView podporuje řízení přihlašované protokolem IdP.

## <a name="add-insideview-from-the-gallery"></a>Přidat InsideView z galerie

Chcete-li nastavit integraci InsideView do Azure AD, musíte přidat InsideView z galerie do seznamu spravovaných aplikací SaaS.

1. Na [webu Azure Portal](https://portal.azure.com)v levém podokně vyberte **Službu Azure Active Directory**:

    ![Vyberte Azure Active Directory.](common/select-azuread.png)

2. Přejít na **podnikové aplikace** > **Všechny aplikace**:

    ![Okno podnikových aplikací](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte v horní části okna možnost **Nová aplikace:**

    ![Vybrat novou aplikaci](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **InsideView**. Ve výsledcích hledání vyberte **InsideView** a pak vyberte **Přidat**.

    ![Výsledky hledání](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí InsideView pomocí testovacího uživatele s názvem Britta Simon.
Chcete-li povolit jednotné přihlašování, musíte vytvořit vztah mezi uživatelem Azure AD a odpovídajícím uživatelem v InsideView.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Aplikace InsideView, je třeba provést tyto kroky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** tak, aby byla tato funkce povolena pro vaše uživatele.
2. **[Konfigurace InsideView jednotné přihlášení na](#configure-insideview-single-sign-on)** straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlášení Azure AD.
4. **[Přiřaďte testovacímu uživateli Azure AD,](#assign-the-azure-ad-test-user)** aby uživatel povolil jednotné přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele InsideView,](#create-an-insideview-test-user)** který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlášení](#test-single-sign-on)** a ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí InsideView, postupujte takto:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací InsideView vyberte **Jedno přihlášení**:

    ![Vybrat jednotné přihlašování](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování:

    ![Výběr metody jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** vyberte ikonu **Upravit** a otevřete dialogové okno Základní **konfigurace SAML:**

    ![Ikona úprav](common/edit-urls.png)

4. V dialogovém okně **Základní konfigurace SAML** proveďte následující kroky.

    ![Dialogové okno Základní konfigurace SAML](common/idp-reply.png)

    Do pole **Adresa URL pro odpověď** zadejte adresu URL v tomto vzoru:

    `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > Tato hodnota je zástupný symbol. Musíte použít skutečnou adresu URL odpovědi. Obraťte se na [tým podpory InsideView](mailto:support@insideview.com) získat hodnotu. Můžete také odkazovat na vzory zobrazené v dialogovém okně **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** vyberte v části **Podpisový certifikát SAML** odkaz **Ke stažení** vedle **certifikátu (Nezpracovaný)** a uložte certifikát do počítače:

    ![Odkaz ke stažení certifikátu](common/certificateraw.png)

6. V části **Nastavit InsideView** zkopírujte příslušné adresy URL na základě vašich požadavků:

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    1. **Adresa URL přihlášení**.

    1. **Identifikátor služby Azure .**

    1. **Adresa URL pro odhlášení**.

### <a name="configure-insideview-single-sign-on"></a>Konfigurace jednotného přihlašování InsideView

1. V novém okně webového prohlížeče se přihlaste na web společnosti InsideView jako správce.

1. V horní části okna vyberte **Admin**, **SingleSignOn Settings**a potom **Add SAML**.
   
   ![Saml nastavení jednotného přihlášení](./media/insideview-tutorial/ic794135.png "Saml nastavení jednotného přihlášení")

1. V části **Přidat nový SAML** postupujte podle následujících kroků.

    ![Přidání nového oddílu SAML](./media/insideview-tutorial/ic794136.png "Přidání nového oddílu SAML")

    1. Do pole **Název služby STS** zadejte název konfigurace.

    1. V poli **Nevyžádaný koncový bod SamlP/WS-Fed** vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z portálu Azure.

    1. Otevřete nezpracovaný certifikát, který jste stáhli z webu Azure Portal. Zkopírujte obsah certifikátu do schránky a vložte jej do pole **Certifikát STS.**

    1. Do pole **Mapování ID uživatele aplikace Crm** zadejte **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    1. Do pole **Mapování e-mailů crm** zadejte **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    1. Do pole **Mapování křestního jména crm** zadejte **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    1. Do pole **Crm lastName** **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**Mapping zadejte .  

    1. Vyberte **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal vyberte v levém podokně **Službu Azure Active Directory,** vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**:

    ![Vyberte Všichni uživatelé.](common/users.png)

2. V horní části okna vyberte **Nového uživatele:**

    ![Vybrat nového uživatele](common/new-user.png)

3. V dialogovém okně **Uživatel** proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    1. Do pole **Název** zadejte **BrittaSimon**.
  
    1. Do pole **Uživatelské jméno** zadejte **BrittaSimon@\<\<> vaší firemní domény. prodloužení>**. (Například BrittaSimon@contoso.com.)

    1. Vyberte **Zobrazit heslo**a poznamenejte si hodnotu, která je v poli **Heslo.**

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení tím, že jí přístup k InsideView.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **InsideView**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **InsideView**.

    ![Seznam žádostí](common/all-applications.png)

3. V levém podokně vyberte **Uživatelé a skupiny**:

    ![Vyberte Uživatelé a skupiny.](common/users-groups-blade.png)

4. V **dialogovém okně Přidat** **přiřazení** vyberte Přidat uživatele a pak vyberte **Uživatelé a skupiny.**

    ![Výběr možnosti Přidat uživatele](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu Uživatelé **Brittu Simonovou** a v dolní části okna klikněte na tlačítko **Vybrat.**

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu. Klepněte na tlačítko **Vybrat** v dolní části okna.

7. V dialogovém okně **Přidat přiřazení** vyberte **Přiřadit**.

### <a name="create-an-insideview-test-user"></a>Vytvoření testovacího uživatele InsideView

Chcete-li povolit uživatelům Azure AD k přihlášení k InsideView, je nutné je přidat do InsideView. Musíte je přidat ručně.

Chcete-li vytvořit uživatele nebo kontakty v InsideView, obraťte se na [tým podpory InsideView](mailto:support@insideview.com).

> [!NOTE]
> Můžete použít libovolný nástroj pro vytváření uživatelských účtů nebo rozhraní API poskytované InsideView zřídit uživatelské účty Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Teď je potřeba otestovat konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když vyberete insideview dlaždice na přístupovém panelu, měli byste být automaticky přihlášeni k InsideView instance, pro které nastavíte přistupující služby. Další informace o přístupovém panelu najdete v [tématu Přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Kurzy integrace aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
