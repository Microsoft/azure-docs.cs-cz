---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s TeamzSkill | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TeamzSkill.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/17/2020
ms.author: jeedes
ms.openlocfilehash: 490bce5ba39b374d54dc9a3f5d76e7ddcdc4e99a
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92504554"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-teamzskill"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s TeamzSkill

V tomto kurzu se dozvíte, jak integrovat TeamzSkill s Azure Active Directory (Azure AD). Když integrujete TeamzSkill s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k TeamzSkill.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k TeamzSkill svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* TeamzSkill odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* TeamzSkill podporuje jednotné přihlašování (SSO) **a IDP** .
* TeamzSkill podporuje zřizování uživatelů **jenom v čase** .

## <a name="adding-teamzskill-from-the-gallery"></a>Přidání TeamzSkill z Galerie

Pokud chcete nakonfigurovat integraci TeamzSkill do služby Azure AD, musíte přidat TeamzSkill z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **TeamzSkill** .
1. Na panelu výsledků vyberte **TeamzSkill** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-teamzskill"></a>Konfigurace a testování jednotného přihlašování Azure AD pro TeamzSkill

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí TeamzSkill pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v TeamzSkill.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí TeamzSkill postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte TEAMZSKILL SSO](#configure-teamzskill-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte TeamzSkill Test User](#create-teamzskill-test-user)** -to, abyste měli protějšek B. Simon v TeamzSkill, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **TeamzSkill** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<CUSTOMER_SUBDOMAIN>.teamzskill.com/login/callback`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<CUSTOMER_SUBDOMAIN>.teamzskill.com/login/callback`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<CUSTOMER_SUBDOMAIN>.teamzskill.com/login/callback`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta TeamzSkill](mailto:support@teamzskill.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. TeamzSkill aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace TeamzSkill několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.
    
    | Name |  Zdrojový atribut|
    | ------------ | --------- |
    | FirstName | User. křestní jméno |
    | Polím | User. příjmení |
    | jobtitle | User. jobtitle |
    | Oddělení | User. Department |
    | zaměstnance | User. ČísloZaměstnance |
    | ovládacím | User. PostalCode |
    | country | uživatel. Country |
    | role | User. assignedroles |

    > [!NOTE]
    > TeamzSkill očekává role pro uživatele přiřazené k aplikaci. Nastavte prosím tyto role ve službě Azure AD, aby bylo možné uživatelům přiřadit příslušné role. Informace o tom, jak nakonfigurovat role v Azure AD, najdete [tady](../develop/active-directory-enterprise-app-role-management.md).

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení TeamzSkill** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k TeamzSkill.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **TeamzSkill**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-teamzskill-sso"></a>Konfigurace jednotného přihlašování TeamzSkill

1. V jiném okně webového prohlížeče se přihlaste k TeamzSkill jako správce.

1. klikněte na ikonu profil uživatele a pak vyberte **nastavení společnosti**.

    ![Nastavení společnosti v Teamzskill](./media/teamzskill-tutorial/settings.png)

1. Na stránce **Nastavení** proveďte následující kroky.

    ![nastavení v Teamzskill](./media/teamzskill-tutorial/metadata.png)

    a. Přejděte na **společnost > jednotné přihlašování**a pak vyberte kartu **nahrání metadat** .

    b. Vložte hodnotu **XML metadat federace** , kterou jste zkopírovali z Azure Portal do pole **metadat XML** .
     
    c. Potom klikněte na **Uložit**.

### <a name="create-teamzskill-test-user"></a>Vytvořit testovacího uživatele TeamzSkill

V této části se v TeamzSkill vytvoří uživatel s názvem B. Simon. TeamzSkill podporuje zřizování za běhu, které je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel už v TeamzSkill neexistuje, vytvoří se nový, když se pokusíte o přístup k TeamzSkill.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k TeamzSkill, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlášení k TeamzSkill přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k TeamzSkill, pro které jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít panel Microsoft Access. Když kliknete na dlaždici TeamzSkill na přístupovém panelu, pokud se nakonfiguruje v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k TeamzSkill, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování TeamzSkill můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).