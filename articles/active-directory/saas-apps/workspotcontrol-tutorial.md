---
title: 'Kurz: Azure Active Directory integrace s ovládacím prvkem Workspot | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování pro Azure Active Directory a ovládací prvek Workspot.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: bfbecc71638e6feaaf29809f09dda752dd29b2ae
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "88526512"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Kurz: Azure Active Directory integrace s ovládacím prvkem Workspot

V tomto kurzu se naučíte integrovat řízení Workspot s využitím Azure Active Directory (Azure AD). Když integrujete ovládací prvek Workspot s Azure AD, můžete:

* Pomocí Azure AD můžete řídit, kdo má přístup k ovládacímu prvku Workspot.
* Umožněte uživatelům, aby se automaticky přihlásili k ovládacímu prvku Workspot (jednotné přihlašování [SSO]) pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [jednotné přihlašování k aplikacím v Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s ovládacím prvkem Workspot potřebujete tyto věci:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

* Odběr s podporou jednotného přihlašování Workspot ovládacího prvku.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

> [!Note]
> Ovládací prvek Workspot podporuje jednotné přihlašování iniciované v SP a IDP.


## <a name="adding-workspot-control-from-the-gallery"></a>Přidání ovládacího prvku Workspot z Galerie

Pokud chcete nakonfigurovat integraci ovládacího prvku Workspot do služby Azure AD, musíte přidat řízení Workspot z Galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat ovládací prvek Workspot z Galerie, postupujte podle následujících kroků:**

1. V levém podokně [Azure Portal](https://portal.azure.com)vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejít na **podnikové aplikace** a vyberte **všechny aplikace**.

    ![Podokno podnikové aplikace](common/enterprise-applications.png)

3. V horní části okna vyberte **Nová aplikace** .

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ovládací prvek Workspot**, na panelu výsledků vyberte **ovládací prvek Workspot** a pak vyberte **Přidat**.

     ![Okno Přidat z Galerie](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí ovládacího prvku Workspot pro testovacího uživatele Britta Simon.
Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v ovládacím prvku Workspot.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí ovládacího prvku Workspot, musíte provést následující úlohy:

1. [Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on) tak, aby vaši uživatelé mohli používat tuto funkci.
2. [Nakonfigurujte jednotné přihlašování ovládacího prvku Workspot](#configure-workspot-control-single-sign-on) ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. [Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user) pro testování jednotného přihlašování Azure AD pro Britta Simon.
4. [Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user) , aby mohl Britta Simon používat jednotné přihlašování Azure AD.
5. [Vytvořte testovacího uživatele ovládacího prvku Workspot](#create-a-workspot-control-test-user) , který vytvoří protějšek Britta Simon v ovládacím prvku Workspot, který je propojený s Předprezentací služby Azure AD.
6. [Otestujte jednotné přihlašování](#test-single-sign-on) a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí ovládacího prvku Workspot, postupujte takto:

1. Na stránce pro integraci aplikace **ovládacího prvku Workspot** v [Azure Portal](https://portal.azure.com/)vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V okně **Vyberte metodu jednotného přihlašování** vyberte režim **SAML** a povolte jednotné přihlašování.

    ![Vyberte okno pro výběr metody jednotného přihlašování.](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte ikonu **Upravit** (tužka) pro přístup ke **základní konfiguraci SAML**.

    ![Ikona úprav zvýrazněná v základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované v IDP, postupujte podle následujících kroků v části **základní konfigurace SAML** :

    ![Informace o jednotném přihlašování v doméně řízení Workspot a adresách URL](common/idp-intiated.png)

    1. Do textového pole **identifikátor** zadejte adresu URL v následujícím vzoru:<br/>
    **_https://<<i></i> InstanceName> – saml.workspot.com/saml/metadata_*_

    1. Do _textového pole *Adresa URL odpovědi** zadejte adresu URL v následujícím vzoru: <br/> 
    * *_https://<<i></i> InstanceName>-SAML.workspot.com/SAML/Assertion_*_

5. Pokud chcete nakonfigurovat aplikaci v režimu iniciované v rámci SP, vyberte _ * nastavit další adresy URL * *.

    ![Informace o jednotném přihlašování v doméně řízení Workspot a adresách URL](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL v následujícím vzoru:<br/>
    **_https://<<i></i> InstanceName> – saml.workspot.com/_*_

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Nahraďte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory Workspot Control Client](mailto:support@workspot.com) . Případně můžete také odkazovat na vzory v části*základní konfigurace SAML** v Azure Portal.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte v části **podpisový certifikát SAML** možnost **Stáhnout** a Stáhněte si **certifikát (Base64)** z dostupných možností podle vašich požadavků. Uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu (Base64)](common/certificatebase64.png)

7. V části **nastavení ovládacího prvku Workspot** zkopírujte příslušné adresy URL podle vašich požadavků:

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    - **Přihlašovací adresa URL**

    - **Identifikátor Azure AD**

    - **Odhlašovací adresa URL**

### <a name="configure-workspot-control-single-sign-on"></a>Konfigurace jednotného přihlašování ovládacího prvku Workspot

1. V jiném okně webového prohlížeče se přihlaste k ovládacímu prvku Workspot jako správce zabezpečení.

2. Na panelu nástrojů v horní části stránky vyberte **Nastavení** a potom **SAML**.

    ![Možnosti nastavení](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. V okně **konfigurace Security Assertion Markup Language** postupujte podle následujících kroků:
 
    ![Okno Konfigurace Security Assertion Markup Language](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. Do pole **ID entity** vložte **identifikátor Azure AD** , který jste zkopírovali z Azure Portal.

    1. Do pole **Adresa URL služby jednotného** vložte **přihlašovací adresu URL** , kterou jste zkopírovali z Azure Portal.

    1. Do pole **Adresa URL odhlašovací služby** vložte **adresu URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    1. Vyberte **aktualizovat soubor** , který se odešle do 64 certifikátu X. 509, který jste stáhli z Azure Portal.

    1. Vyberte **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele v Azure Portal.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, **Uživatelé**a potom **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části okna vyberte **Nový uživatel** .

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnostech pro uživatele postupujte podle těchto kroků:

    ![Okno Vlastnosti uživatele](common/user-properties.png)

    1. Do pole **název** zadejte **BrittaSimon**.
  
    1. Do pole **uživatelské jméno** zadejte **brittasimon@* yourcompanydomain. extension * * *. Zadejte například ** BrittaSimon@contoso . <i></i> model COM**.

    1. Zaškrtněte políčko **Zobrazit heslo** . Pak zapište hodnotu, která se zobrazí v poli **heslo** .

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části udělíte Britta Simon přístup k ovládacímu prvku Workspot, aby bylo možné používat jednotné přihlašování Azure.

1. V Azure Portal vyberte **podnikové aplikace**, **všechny aplikace**a pak **ovládací prvek Workspot**.

    ![Podokno podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **ovládací prvek Workspot**.

    ![Odkaz na ovládací prvek Workspot v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** . Pak v okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Okno Přidat přiřazení](common/add-assign-user.png)

5. V okně **Uživatelé a skupiny** vyberte v seznamu **uživatelů** položku **Britta Simon** . Pak klikněte na **Vybrat**.

6. Pokud v kontrolním výrazu SAML očekáváte jakoukoli hodnotu role, vyberte příslušnou roli pro uživatele ze seznamu v okně **Vybrat roli** . Pak klikněte na tlačítko **Vybrat** v dolní části.

7. V okně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-workspot-control-test-user"></a>Vytvoření testovacího uživatele ovládacího prvku Workspot

Aby se uživatelé Azure AD mohli přihlašovat k ovládacímu prvku Workspot, musí se zřídit do řízení Workspot. Zřizování je ruční úloha.

**Pokud chcete zřídit uživatelský účet, postupujte podle těchto kroků:**

1. Přihlaste se k ovládacímu prvku Workspot jako správce zabezpečení.

2. Na panelu nástrojů v horní části stránky vyberte **Uživatelé** a pak **Přidat uživatele**.

    ![Možnosti uživatelů](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. V okně **Přidat nového uživatele** postupujte podle těchto kroků:

    ![Okno Přidat nového uživatele](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. Do pole **jméno** zadejte jméno uživatele, například **Britta**.

    1. Do textového pole **poslední jméno** zadejte příjmení uživatele, například **Simon**.

    1. Do pole **e-mail** zadejte e-mailovou adresu uživatele, například ** Brittasimon@contoso . <i></i> model COM**.

    1. V rozevíracím seznamu **role** vyberte příslušnou roli uživatele.

    1. V rozevíracím seznamu **Skupina** vyberte příslušnou skupinu uživatelů.

    1. Vyberte **Přidat uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujeme konfiguraci jednotného přihlašování Azure AD prostřednictvím *přístupového panelu*.

Když na přístupovém panelu kliknete na dlaždici **ovládacího prvku Workspot** , měli byste být automaticky přihlášeni k ovládacímu prvku Workspot, pro který jste nastavili jednotné přihlašování. Další informace najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="additional-resources"></a>Další zdroje informací

- [Kurzy integrace aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

- [Jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
