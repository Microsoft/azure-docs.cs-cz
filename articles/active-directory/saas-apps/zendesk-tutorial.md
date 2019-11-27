---
title: 'Kurz: Azure Active Directory integrace s Zendesk | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zendesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4887457f457ff533a0eaf10be7db1627a950f5bd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233271"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Kurz: Azure Active Directory integrace s Zendesk

V tomto kurzu se dozvíte, jak integrovat Zendesk s Azure Active Directory (Azure AD).
Integrace Zendesk s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Zendesk.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k Zendesk (jednotné přihlašování) pomocí svých účtů Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Zendesk potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným Zendeskm jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Zendesk podporuje jednotné přihlašování iniciované v **SP**

* Zendesk podporuje [ **automatizované** zřizování uživatelů](zendesk-provisioning-tutorial.md)

## <a name="adding-zendesk-from-the-gallery"></a>Přidání Zendesk z Galerie

Pokud chcete nakonfigurovat integraci Zendesk do služby Azure AD, musíte přidat Zendesk z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Zendesk z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zendesk**, vyberte **Zendesk** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Zendesk v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Zendesk na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Zendesk.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Zendesk, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Zendesk](#configure-zendesk-single-sign-on)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření Zendesk Test User](#create-zendesk-test-user)** – pro Britta Simon v Zendesk, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Zendesk, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Zendesk** vyberte **jednotné přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně Zendesk a adresách URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.zendesk.com`

   b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.zendesk.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta Zendesk](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Zendesk aplikace očekává kontrolní výrazy SAML v určitém formátu. Neexistují žádné povinné atributy SAML, ale Volitelně můžete spravovat z oddílu **atributy uživatele** na stránce integrace aplikací. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na tlačítko **Upravit** a otevřete dialog **uživatelské atributy** .

    ![image](common/edit-attribute.png)

6. V části **deklarace identity uživatelů** v dialogu **atributy uživatele** NAKONFIGURUJTE atribut tokenu SAML, jak je znázorněno na obrázku výše, a proveďte následující kroky:

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut**vyberte zdroj.

    e. V seznamu **zdrojový atribut** vyberte odpovídající hodnotu atributu.

    f. Klikněte na **OK** .

    g. Klikněte na možnost **Uložit**.

    > [!NOTE]
    > Pomocí atributů rozšíření ve výchozím nastavení přidáte atributy, které nejsou ve službě Azure AD. Klikněte na [atributy uživatele, které se dají nastavit v SAML](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) , aby se získal úplný seznam atributů SAML, které **Zendesk** akceptuje.

7. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** zkopírujte **kryptografický otisk** a uložte ho do svého počítače.

    ![Kopírovat hodnotu kryptografického otisku](common/copy-certificatethumbprint.png)

8. V části **Nastavení Zendesk** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

9. Existují dva způsoby, jak lze Zendesk nakonfigurovat – automatické a ruční.
  
10. Pokud chcete automatizovat konfiguraci v rámci Zendesk, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![image](./media/zendesk-tutorial/install_extension.png)

11. Po přidání rozšíření do prohlížeče klikněte na **Zendesk nastavení** a nasměrujte vás na aplikaci Zendesk. Odtud zadejte přihlašovací údaje správce, které se přihlásí k Zendesk. Rozšíření prohlížeče automaticky nakonfiguruje aplikaci za vás a automatizuje část **Konfigurace jednotného přihlašování Zendesk**.

    ![image](./media/zendesk-tutorial/d2_saml.png)

### <a name="configure-zendesk-single-sign-on"></a>Konfigurace jednotného přihlašování Zendesk

1. Pokud chcete nastavit Zendesk ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu Zendesk společnosti jako správce a proveďte následující kroky:

2. Klikněte na **správce**.

3. V levém navigačním podokně klikněte na **Nastavení**a pak klikněte na **zabezpečení**.

4. Na stránce **zabezpečení** proveďte následující kroky:

    ![Zabezpečení](././media/zendesk-tutorial/ic773089.png "Zabezpečení")

    ![Jednotné přihlašování](././media/zendesk-tutorial/ic773090.png "Jednotné přihlašování")

    a. Klikněte na kartu **správci & agenti** .

    b. Vyberte **jednotné přihlašování (SSO) a SAML**a pak vyberte **SAML**.

    c. Do textového pole **URL jednotného přihlašování SAML** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    d. Do textového pole **Adresa URL pro vzdálené odhlášení** vložte hodnotu **URL** pro odhlášení, kterou jste zkopírovali z Azure Portal.

    e. Do textového pole **otisku certifikátu** vložte hodnotu **kryptografického otisku** certifikátu, který jste zkopírovali z Azure Portal.

    f. Klikněte na možnost **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon\@yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Zendesk.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Zendesk**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **Zendesk**.

    ![Odkaz Zendesk v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-zendesk-test-user"></a>Vytvořit testovacího uživatele Zendesk

Cílem této části je vytvořit uživatele s názvem Britta Simon v Zendesk. Zendesk podporuje automatické zřizování uživatelů, které je ve výchozím nastavení povolené. Další podrobnosti najdete v [tématu](Zendesk-provisioning-tutorial.md) konfigurace automatického zřizování uživatelů.

**Pokud potřebujete ručně vytvořit uživatele, proveďte prosím následující kroky:**

> [!NOTE]
> Účty **koncových uživatelů** se při přihlašování automaticky zřídí. Účty **agenta** a **správce** je potřeba ručně zřídit v **Zendesk** , než se přihlásí.

1. Přihlaste se ke svému tenantovi **Zendesk** .

2. Vyberte kartu **seznam zákazníků** .

3. Vyberte kartu **uživatel** a klikněte na **Přidat**.

    ![Přidat uživatele](././media/zendesk-tutorial/ic773632.png "Přidání uživatele")
4. Zadejte **název** a **E-mail** existujícího účtu Azure AD, který chcete zřídit, a pak klikněte na **Uložit**.

    ![Nový uživatel](././media/zendesk-tutorial/ic773633.png "Nový uživatel")

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jiné nástroje pro vytváření uživatelských účtů Zendesk nebo rozhraní API poskytovaná Zendesk.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Zendesk, měli byste se automaticky přihlásit k Zendesk, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurace zřizování uživatelů](zendesk-provisioning-tutorial.md)