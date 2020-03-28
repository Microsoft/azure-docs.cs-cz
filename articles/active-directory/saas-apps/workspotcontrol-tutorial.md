---
title: 'Kurz: Integrace služby Azure Active Directory s řízením pracovních míst | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování pro Azure Active Directory a Workspot Control.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: 086ec95531b01477be56d4b1a19d189f167a020f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086687"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Kurz: Integrace služby Azure Active Directory s ovládacím prvkem Workspot

V tomto kurzu se dozvíte, jak integrovat workspot control s Azure Active Directory (Azure AD). Když integrujete Workspot Control s Azure AD, můžete:

* Azure AD slouží k řízení, kdo má přístup k workspot control.
* Umožněte uživatelům automatické přihlášení k workspotovému řízení (jednotné přihlašování [Jednotné přihlašování]) pomocí jejich účtů Azure AD.
* Spravujte své účty na jednom centrálním místě: na portálu Azure.

Další informace o integraci aplikací SaaS s Azure AD najdete v [tématu jednotné přihlašování k aplikacím ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí workspot control, potřebujete následující věci:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

* Předplatné s povolenou funkcí jednotného přihlášení Workspot Control.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

> [!Note]
> Workspot Control podporuje sp-inicioval a IDP-inicioval sso.


## <a name="adding-workspot-control-from-the-gallery"></a>Přidání ovládacího prvku Workspot z galerie

Chcete-li nakonfigurovat integraci workspot control do Azure AD, musíte přidat Workspot Control z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat ovládací prvek Workspot control z galerie, postupujte takto:**

1. V levém podokně [portálu Azure](https://portal.azure.com)vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **Podnikové aplikace** a vyberte **Všechny aplikace**.

    ![Podokno Podnikové aplikace](common/enterprise-applications.png)

3. V horní části okna vyberte **Nová aplikace.**

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Ovládací prvek pracovních bodů**, vpanelu S výsledky vyberte Ovládací prvek **pracovních bodů** a pak vyberte **Přidat**.

     ![Okno "Přidat z galerie"](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí workspotového řízení pro testovacího uživatele Brittu Simonovou.
Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v workspotovém řízení.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí workspotového řízení, musíte provést následující úkoly:

1. [Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on) tak, aby uživatelé mohli tuto funkci používat.
2. [Nakonfigurujte jednotné přihlašování workspot control](#configure-workspot-control-single-sign-on) a nakonfigurujte nastavení jednotného přihlášení na straně aplikace.
3. [Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user) pro testování jednotného přihlášení Azure AD pro Brittu Simon.
4. [Přiřaďte uživateli testu Azure AD,](#assign-the-azure-ad-test-user) aby britta Simon mohla používat jednotné přihlašování Azure AD.
5. [Vytvořte uživatele testu Workspot Control](#create-a-workspot-control-test-user) a vytvořte protějšek Britta Simon v workspotovém řízení, který je propojený s reprezentací Azure AD uživatele.
6. [Otestujte jednotné přihlášení](#test-single-sign-on) a ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí workspotového řízení, postupujte takto:

1. Na stránce integrace aplikací **Workspot Control** na [webu Azure Portal](https://portal.azure.com/)vyberte Jedno **přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V okně **Vybrat metodu jednotného přihlášení** vyberte režim **SAML,** chcete-li povolit jednotné přihlašování.

    ![Výběr okna metody výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** vyberte ikonu **Upravit** (tužka) pro přístup k **základní konfiguraci SAML**.

    ![Ikona Upravit zvýrazněná v části Základní konfigurace SAML](common/edit-urls.png)

4. Pokud chcete aplikaci konfigurovat v režimu iniciovaném protokolem IDP, postupujte takto v části **Základní konfigurace SAML:**

    ![Informace o jednotném přihlašování dodomény Workspot Control a adres URL](common/idp-intiated.png)

    1. Do textového pole **identifikátoru** zadejte adresu URL v následujícím vzoru:<br/>
    ***https://<<i> </i>INSTANCENAME>-saml.workspot.com/saml/metadata***

    1. Do textového pole **url odpovědi** zadejte adresu URL v následujícím vzoru:<br/>
    ***https://<<i> </i>INSTANCENAME>-saml.workspot.com/saml/assertion***

5. Pokud chcete aplikaci nakonfigurovat v režimu iniciovaném službou SP, vyberte **nastavit další adresy URL**.

    ![Informace o jednotném přihlašování dodomény Workspot Control a adres URL](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL v následujícím vzoru:<br/>
    ***https://<<i> </i>INSTANCENAME>-saml.workspot.com/***

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Nahraďte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Obraťte se na [tým podpory klienta Workspot Control a](mailto:support@workspot.com) získejte tyto hodnoty. Nebo můžete také odkazovat na vzory v části **Základní konfigurace SAML** na webu Azure Portal.

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** v části **Podpisový certifikát SAML** vyberte **Stáhnout** a stáhněte si **certifikát (Base64)** z dostupných možností podle vašich požadavků. Uložte jej do počítače.

    ![Odkaz ke stažení certifikátu (Base64)](common/certificatebase64.png)

7. V části **Nastavit ovládací prvek pracovních míst** zkopírujte příslušné adresy URL podle vašich požadavků:

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    - **Přihlašovací adresa URL**

    - **Identifikátor azure reklamy**

    - **Adresa URL odhlášení**

### <a name="configure-workspot-control-single-sign-on"></a>Konfigurace jednotného přihlašování ovládacího prvku Workspot Control

1. V jiném okně webového prohlížeče se přihlaste k workspotcontrol jako správce zabezpečení.

2. Na panelu nástrojů v horní části stránky vyberte **Instalační program** a potom **saml**.

    ![Možnosti nastavení](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. V okně **Konfigurace jazyka kontrolního výrazu zabezpečení** postupujte takto:
 
    ![Okno Konfigurace jazyka ražby kontrolního výrazu zabezpečení](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. Do pole **ID entity** vložte **identifikátor Azure Ad,** který jste zkopírovali z webu Azure Portal.

    1. Do pole **Adresa URL přihlašovací služby** vložte **přihlašovací adresu URL,** kterou jste zkopírovali z webu Azure Portal.

    1. Do pole **Adresa URL služby Odhlášení** vložte **adresu URL odhlášení,** kterou jste zkopírovali z webu Azure Portal.

    1. Vyberte **Aktualizovat soubor** a nahrajte do certifikátu X.509 kódovaný certifikát base-64, který jste stáhli z webu Azure Portal.

    1. Vyberte **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele na webu Azure Portal.

1. V levém podokně portálu Azure vyberte **Azure Active Directory**, **Users**a potom **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části okna vyberte **Nový uživatel.**

    ![Tlačítko "Nový uživatel"](common/new-user.png)

3. Ve vlastnostech pro uživatele postupujte takto:

    ![Okno Vlastnosti uživatele](common/user-properties.png)

    1. Do pole **Název** zadejte **BrittaSimon**.
  
    1. Do pole **Uživatelské jméno** zadejte **brittasimon@* vaší domény.extension***. Zadejte například ** BrittaSimon@contoso.<i> </i> com**.

    1. Zaškrtněte políčko **Zobrazit heslo.** Pak poznamenejte hodnotu, která je zobrazena v poli **Heslo.**

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části udělíte Britta Simon přístup k Workspot Control povolit jí používat Azure jednotné přihlašování.

1. Na portálu Azure vyberte **Podnikové aplikace**, **Všechny aplikace**a potom **Workspot Control**.

    ![Podokno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Ovládací prvek pracovního bodu**.

    ![Odkaz Řízení pracovních míst v seznamu Aplikace](common/all-applications.png)

3. V nabídce na levé straně vyberte **možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Vyberte tlačítko **Přidat uživatele.** Pak v okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Okno Přidat přiřazení](common/add-assign-user.png)

5. V okně **Uživatelé a skupiny** vyberte **brittu Simonovou** ze seznamu **Uživatelé.** Pak klikněte na **Vybrat**.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte příslušnou roli pro uživatele ze seznamu v okně **Vybrat roli.** Potom dole klikněte na **Vybrat.**

7. V okně **Přidat přiřazení** vyberte **Přiřadit**.

### <a name="create-a-workspot-control-test-user"></a>Vytvoření testovacího uživatele workspotcontrolu

Chcete-li povolit uživatelům Azure AD k přihlášení k workspot control, musí být zřízena do workspot řízení. Zřizování je ruční úloha.

**Chcete-li zřídit uživatelský účet, postupujte takto:**

1. Přihlaste se k ovládacímu prvku Workspot jako správce zabezpečení.

2. Na panelu nástrojů v horní části stránky vyberte **Uživatelé** a pak **Přidejte uživatele**.

    ![Možnosti "Uživatelé"](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. V okně **Přidat nového uživatele** postupujte takto:

    ![Okno "Přidat nového uživatele"](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. Do pole **Křestní jméno** zadejte křestní jméno uživatele, například **Britta**.

    1. Do pole **Příjmení** zadejte příjmení uživatele, například **Simona**.

    1. Do pole **E-mail** zadejte e-mailovou adresu uživatele, například ** Brittasimon@contoso.<i> </i> com**.

    1. V rozevíracím seznamu **Role** vyberte příslušnou roli uživatele.

    1. V rozevíracím seznamu **Skupina** vyberte příslušnou skupinu uživatelů.

    1. Vyberte **Přidat uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujeme naši konfiguraci jednotného přihlášení Azure AD prostřednictvím *přístupového panelu*.

Po kliknutí na dlaždici **Ovládací prvek pracovního bodu** na přístupovém panelu, můžete by měl být automaticky přihlášeni k ovládacímu prvku workspot, u kterého nastavíte přihlašující. Další informace najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="additional-resources"></a>Další zdroje

- [Kurzy integrace aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

- [Jednotné přihlašování k aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
