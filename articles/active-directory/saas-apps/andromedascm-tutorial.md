---
title: 'Kurz: Azure Active Directory integrace s Andromeda | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Andromeda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: f3fcad14ae0c448ee2a41cddf56f5ea64c8e08d2
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916129"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Kurz: Azure Active Directory integrace s Andromeda

V tomto kurzu se dozvíte, jak integrovat Andromeda s Azure Active Directory (Azure AD).
Integrace Andromeda s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Andromeda.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k Andromeda (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Andromeda potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné s povoleným Andromedam jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Andromeda podporuje jednotné přihlašování (SSO) **a IDP** .
* Andromeda podporuje zřizování uživatelů **jenom v čase** .

## <a name="adding-andromeda-from-the-gallery"></a>Přidání Andromeda z Galerie

Pokud chcete nakonfigurovat integraci Andromeda do služby Azure AD, musíte přidat Andromeda z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Andromeda** .
1. Na panelu výsledků vyberte **Andromeda** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-andromeda"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Andromeda

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Andromeda pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Andromeda.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí Andromeda postupujte takto:


1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
2. **[Nakonfigurujte Andromeda SSO](#configure-andromeda-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
    1. **[Vytvoření Andromeda Test User](#create-andromeda-test-user)** – pro Britta Simon v Andromeda, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Andromeda** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<tenantURL>.ngcxpress.com/`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Snímek obrazovky s nastavením další U R ls, kde můžete zadat přihlášení U R L.](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Hodnotu aktualizujete skutečným identifikátorem, adresou URL odpovědi a adresou URL Sign-On, která je vysvětlena dále v tomto kurzu.

6. Andromeda aplikace očekává kontrolní výrazy SAML v určitém formátu. Pro tuto aplikaci nakonfigurujte následující deklarace identity. Hodnoty těchto atributů můžete spravovat z oddílu **atributy uživatele** na stránce integrace aplikací. Na stránce **nastavit jeden Sign-On se** stránkou SAML kliknutím na tlačítko **Upravit** otevřete dialogové okno **atributy uživatele** .

    ![Snímek obrazovky ukazuje atributy uživatele, jako je například daný uživatel. jméno a EmailAddress User. mail.](common/edit-attribute.png)

    > [!Important]
    > Při nastavování vymažte definice oboru názvů.

7. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** upravte deklarace pomocí **ikony upravit** nebo přidejte deklarace pomocí **Přidat novou deklaraci identity** , jak je znázorněno na obrázku výše, a proveďte následující kroky: 

    | Název | Zdrojový atribut|
    | ------ | -----------|
    | role        | Role specifická pro aplikaci |
    | typ        | Typ aplikací |
    | company       | CompanyName |

    > [!NOTE]
    > Andromeda očekává role pro uživatele přiřazené k aplikaci. Nastavte prosím tyto role ve službě Azure AD, aby bylo možné uživatelům přiřadit příslušné role. Informace o tom, jak nakonfigurovat role v Azure AD, najdete [tady](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui).

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![Snímek obrazovky zobrazuje deklarace identity uživatelů s možnostmi přidání nové deklarace identity a uložení.](common/new-save-attribute.png)

    ![Snímek obrazovky ukazuje spravovat deklarace identity uživatelů, kde můžete zadat hodnoty popsané I v tomto kroku.](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut** vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na **OK** .

    například Klikněte na **Uložit**.

8. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

9. V části **Nastavení Andromeda** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Andromeda.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Andromeda**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud jste nastavili role, jak je vysvětleno výše, můžete je vybrat v rozevíracím seznamu **Vybrat roli** .
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="configure-andromeda-sso"></a>Konfigurace jednotného přihlašování Andromeda

1. Přihlaste se k webu Andromeda společnosti jako správce.

2. V horní části panelu nabídek klikněte na **správce** a přejděte na **Správa**.

    ![Správce Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

3. Na levé straně panelu nástrojů v části **rozhraní** klikněte na **Konfigurace SAML**.

    ![Andromeda SAML](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

4. Na stránce **konfigurační oddíl SAML** proveďte následující kroky:

    ![Andromeda konfigurace](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Zaškrtněte **Povolit jednotné přihlašování pomocí SAML**.

    b. V části **informace o Andromeda** Zkopírujte hodnotu **identity SP** a vložte ji do textového pole **identifikátor** **základního konfiguračního oddílu SAML** .

    c. Zkopírujte hodnotu **adresy URL příjemce** a vložte ji do textového pole **Adresa URL odpovědi** **základního konfiguračního oddílu SAML** .

    d. Zkopírujte hodnotu **URL pro přihlášení** a vložte ji do pole **přihlašovací adresa URL** pro **základní konfigurační oddíl SAML** .

    e. V části **poskytovatel identity SAML** zadejte svůj název IDP.

    f. V textovém poli **jednotné přihlašování koncového bodu** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    například Otevřete stažený **certifikát kódovaný v kódování Base64** z Azure Portal v programu Poznámkový blok, vložte jej do textového pole **certifikátu X 509** .
    
    h. Namapujte následující atributy s příslušnou hodnotou pro usnadnění přihlášení SSO ze služby Azure AD. Pro přihlášení je vyžadován atribut **ID uživatele** . Pro zřizování jsou vyžadovány **e-maily**, **společnosti**, **usertype** a **role** . V této části definujeme mapování atributů (název a hodnoty), které koreluje s definicemi definovanými v rámci Azure Portal

    ![Andromeda attbmap](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Klikněte na **Uložit**.


### <a name="create-andromeda-test-user"></a>Vytvořit testovacího uživatele Andromeda

V této části se v Andromeda vytvoří uživatel s názvem Britta Simon. Andromeda podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v Andromeda neexistuje, vytvoří se po ověření nový. Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory Andromeda Client](https://www.ngcsoftware.com/support/).

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k Andromeda, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlášení k Andromeda přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k Andromeda, pro které jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Andromeda v nabídce Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k Andromeda, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Andromeda můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).