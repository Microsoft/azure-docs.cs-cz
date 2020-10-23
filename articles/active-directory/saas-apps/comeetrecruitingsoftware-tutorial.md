---
title: 'Kurz: Azure Active Directory integrace s doplněním náborového softwaru | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a spoluvyhovují náborový software.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2019
ms.author: jeedes
ms.openlocfilehash: 7f992969cf50bb03df7091e0015c936a794946a6
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455574"
---
# <a name="tutorial-azure-active-directory-integration-with-comeet-recruiting-software"></a>Kurz: Azure Active Directory integrace s doplněním náborového softwaru

V tomto kurzu se dozvíte, jak integrovat integrovaný software pro nábor pomocí Azure Active Directory (Azure AD).
Integrací spolusplňující náborový software s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k předplatnému náborového softwaru.
* Můžete uživatelům povolit, aby se automaticky přihlásili k předplatnému náborovému softwaru (jednotné přihlašování) s účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s využitím nástroje pro přijetí softwaru budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Dodržet si odběr povoleného jednotného přihlašování k softwaru

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Vyhovující náborový software podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

## <a name="adding-comeet-recruiting-software-from-the-gallery"></a>Přidání spoluvyhovujícího náborového softwaru z Galerie

Pokud chcete nakonfigurovat integraci předvyhovujícího náborového softwaru do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat do seznamu spravovaných aplikací pro příjem náboru z galerie.

**Pokud chcete do galerie přidat vyhovující náborový software, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte text **dodržet náborový software**, vyberte možnost **přeplnit náborový software** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Vyhovující náborový software v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí souběžného náborového softwaru na základě testovacího uživatele s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, je potřeba zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci naplnění náborového softwaru.

Pokud chcete konfigurovat a testovat jednotné přihlašování pomocí služby Azure AD, musíte provést následující stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování v rámci náborového softwaru](#configure-comeet-recruiting-software-single-sign-on)** , abyste na straně aplikace nakonfigurovali nastavení jednoho Sign-On.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte si tohoto uživatele testovacího náborového softwaru](#create-comeet-recruiting-software-test-user)** , abyste měli protějšek Britta Simon, který je v souladu s předstupujem náborového softwaru Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování pomocí služby Azure AD, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **koschůze pro integraci softwarových** aplikací vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Snímek obrazovky s oddílem konfigurace "základní S A M L", který obsahuje zvýrazněné pole Uložit, "identifikátor" a "odpovědět U R L".](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://app.comeet.co/adfs_auth/acs/<UNIQUEID>/`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://app.comeet.co/adfs_auth/acs/<UNIQUEID>/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory náborového softwarového klienta](https://support.comeet.co/knowledgebase/adfs-single-sign-on/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Doplnění informací o jednotném přihlašování v doméně softwaru a adresách URL](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://app.comeet.co`

5. Vyhovující náborový software aplikace očekává kontrolní výrazy SAML v určitém formátu. Pro tuto aplikaci nakonfigurujte následující deklarace identity. Hodnoty těchto atributů můžete spravovat z oddílu **atributy uživatele** na stránce integrace aplikací. Na stránce **nastavit jeden Sign-On se** stránkou SAML kliknutím na tlačítko **Upravit** otevřete dialogové okno **atributy uživatele** .

    ![Snímek obrazovky, který zobrazuje oddíl "atributy uživatele" s vybraným tlačítkem Upravit.](common/edit-attribute.png)

6. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** upravte deklarace pomocí **ikony upravit** nebo přidejte deklarace pomocí **Přidat novou deklaraci identity** , jak je znázorněno na obrázku výše, a proveďte následující kroky: 

    | Name |  Zdrojový atribut|
    | ---------------| --------------- |
    | nameidentifier | uživatel. pošta |
    | comeet_id | User. userPrincipalName |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![Snímek obrazovky, který zobrazuje oddíl deklarace identity uživatele se zvýrazněnými akcemi přidat nové deklarace identity a uložit](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut**vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na **OK** .

    například Klikněte na **Uložit**.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení vyhovujícího náborového softwaru** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-comeet-recruiting-software-single-sign-on"></a>Konfigurace vyhovujícího náborového softwaru po jednom Sign-On

Ke konfiguraci jednotného přihlašování na straně souběžného **náborového softwaru** je potřeba odeslat stažený **soubor XML federačních metadat** a příslušné zkopírované adresy URL z Azure Portal pro [spoluuspokojení týmu podpory náborového softwaru](https://support.comeet.co/knowledgebase/adfs-single-sign-on/). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon \@ yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k předplatnému náborového softwaru.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **dodržet náborový software**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **dodržet náborový software**.

    ![Odkaz na vyhovující náborový software v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-comeet-recruiting-software-test-user"></a>Vytvoření vyhovujícího uživatele testovacího náborového softwaru

V této části vytvoříte uživatele s názvem Britta Simon ve vyhovujícím náborovém softwaru. Spoluvyhovují [týmu podpory náborového softwaru](mailto:support@comeet.co) , aby mohli přidávat uživatele v rámci vyhovujících náborových softwarových platforem. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici dodržet náborový software na přístupovém panelu byste se měli automaticky přihlášeni k předplatnému náborového softwaru, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)