---
title: 'Kurz: Integrace Azure Active Directory se Zendeskem | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zendeskem.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233271"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Kurz: Integrace Azure Active Directory se Zendeskem

V tomto kurzu se dozvíte, jak integrovat Zendesk s Azure Active Directory (Azure AD).
Integrace Zendesku s Azure AD vám přináší následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Zendesk.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke Zendesku (jednotné přihlašování) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD se Zendeskem, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné zendesk s povoleným jedním přihlášením

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Zendesk podporuje **sp** inicioval SSO

* Zendesk podporuje [ **automatické** zřizování uživatelů](zendesk-provisioning-tutorial.md)

## <a name="adding-zendesk-from-the-gallery"></a>Přidání Zendesku z galerie

Pokud chcete nakonfigurovat integraci Zendesku do Azure AD, musíte zendesk přidat z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li zendesk přidat z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zendesk**, z panelu výsledků vyberte **Zendesk** a kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Zendesk v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD se Zendeskem na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Zendesku.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD se zendeskem, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlášení Zendesk](#configure-zendesk-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte testovacího uživatele Zendesk](#create-zendesk-test-user)** – chcete-li mít protějšek Britta Simon v Zendesk, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí Zendesku, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Zendesk** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Zendesk Domény a adresy URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.zendesk.com`

   b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.zendesk.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Obraťte se na [tým podpory klienta Zendesk](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) a získejte tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Aplikace Zendesk očekává kontrolní výrazy SAML v určitém formátu. Neexistují žádné povinné atributy SAML, ale volitelně můžete spravovat z části **Atributy uživatele** na stránce integrace aplikace. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na **tlačítko Upravit** otevřete dialogové okno **Atributy uživatele.**

    ![image](common/edit-attribute.png)

6. V části **Deklarace identity uživatelů** v dialogovém okně **Atributy uživatele** nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku výše, a proveďte následující kroky:

    a. Kliknutím na **Přidat novou deklaraci** otevřete dialogové okno **Spravovat deklarace identity uživatelů.**

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **Název** zadejte název atributu zobrazený pro daný řádek.

    c. Ponechejte **obor názvů** prázdný.

    d. Vyberte zdroj jako **atribut**.

    e. V seznamu **atributů Zdroj** vyberte příslušnou hodnotu atributu.

    f. Klikněte na **Ok.**

    g. Klikněte na **Uložit**.

    > [!NOTE]
    > Atributy rozšíření se používají k přidání atributů, které ve výchozím nastavení nejsou ve službě Azure AD. Kliknutím na [Atributy uživatele, které lze nastavit v saml,](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) získáte úplný seznam atributů SAML, které **Zendesk** přijímá.

7. Na stránce **Nastavit jednotné přihlašování pomocí saml** zkopírujte v části **Podpisový certifikát SAML** **kryptografický otisk** a uložte jej do počítače.

    ![Kopírovat kryptografický otisk, hodnota](common/copy-certificatethumbprint.png)

8. V části **Nastavit Zendesk** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

9. Zendesk lze konfigurovat dvěma způsoby - Automatické a Manuální.
  
10. Chcete-li automatizovat konfiguraci v rámci Zendesku, musíte nainstalovat **rozšíření prohlížeče My Apps Secure Sign-in** kliknutím na nainstalovat **rozšíření**.

    ![image](./media/zendesk-tutorial/install_extension.png)

11. Po přidání rozšíření do prohlížeče, klikněte na **nastavení Zendesk** vás nasměruje do aplikace Zendesk. Odtud zadejte přihlašovací údaje správce pro přihlášení do Zendesk. Rozšíření prohlížeče automaticky nakonfiguruje aplikaci pro vás a automatizuje sekci **Konfigurace Zendesk u jednoho přihlášení**.

    ![image](./media/zendesk-tutorial/d2_saml.png)

### <a name="configure-zendesk-single-sign-on"></a>Konfigurace jednotného přihlášení Zendesku

1. Chcete-li zendesk nastavit ručně, otevřete nové okno webového prohlížeče a přihlaste se na firemní web Zendesk jako správce a proveďte následující kroky:

2. Klikněte na **Správce**.

3. V levém navigačním podokně klepněte na tlačítko **Nastavení**a potom na **položku Zabezpečení**.

4. Na stránce **Zabezpečení** proveďte následující kroky:

    ![Zabezpečení](././media/zendesk-tutorial/ic773089.png "Zabezpečení")

    ![Jednotné přihlašování](././media/zendesk-tutorial/ic773090.png "Jednotné přihlašování")

    a. Klikněte na kartu **Správci & agenti.**

    b. Vyberte **Jednotné přihlašování (SSO) a SAML**a pak vyberte **SAML**.

    c. V textovém poli **URL přisychovaného při správě SAML** vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z webu Azure Portal.

    d. V textovém poli **Adresa URL vzdáleného odhlášení** vložte hodnotu **adresy URL odhlášení,** kterou jste zkopírovali z webu Azure Portal.

    e. V textovém poli **Otisk prstu certifikátu** vložte hodnotu **kryptografický otisk** certifikátu, který jste zkopírovali z webu Azure Portal.

    f. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** zadejte **\@brittasimon vašecompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k Zendesk.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Zendesk**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací zadejte a vyberte **Zendesk**.

    ![Odkaz Zendesk v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-zendesk-test-user"></a>Vytvořit testovacího uživatele Zendesk

Cílem této sekce je vytvořit uživatele s názvem Britta Simon v Zendesk. Zendesk podporuje automatické zřizování uživatelů, které je ve výchozím nastavení povoleno. Další podrobnosti najdete [zde](Zendesk-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

**Pokud potřebujete vytvořit uživatele ručně, proveďte následující kroky:**

> [!NOTE]
> **Účty koncových uživatelů** se automaticky zřaží při přihlášení. **Účty agentů** a **správců** je třeba ručně zřídit v **Zendesku** před přihlášením.

1. Přihlaste se ke svému tenantovi **Zendesk.**

2. Vyberte kartu **Seznam zákazníků.**

3. Vyberte kartu **Uživatel** a klepněte na tlačítko **Přidat**.

    ![Přidat uživatele](././media/zendesk-tutorial/ic773632.png "Přidání uživatele")
4. Zadejte **název** a **e-mail** existujícího účtu Azure AD, který chcete zřídit, a klikněte na **Uložit**.

    ![Nový uživatel](././media/zendesk-tutorial/ic773633.png "Nový uživatel")

> [!NOTE]
> Ke zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů Zendesk nebo rozhraní API, která poskytuje Zendesk.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Zendesk na přístupovém panelu, můžete by měl být automaticky přihlášeni k Zendesk, u kterého nastavíte přistupující služby. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurace zřizování uživatelů](zendesk-provisioning-tutorial.md)