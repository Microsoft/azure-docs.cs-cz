---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s záchytný bod'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a záchytný bod.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: a8515920985c569df74b1e328d6bfe1c4ec97195
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735310"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-catchpoint"></a>Kurz: Azure Active Directory integrace s jednotným přihlašováním pomocí záchytný bod

V tomto kurzu se dozvíte, jak integrovat záchytný bod s Azure Active Directory (Azure AD). Když integrujete záchytný bod s Azure AD, můžete:

* Řízení přístupu uživatelů k záchytný bod ze služby Azure AD.
* Povolte pro uživatele s účty Azure AD automatické přihlašování záchytný bod.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné záchytný bod s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Záchytný bod podporuje jednotné přihlašování iniciované v SP a IDP.
* Záchytný bod podporuje zřizování uživatelů JIT (just-in-time).

## <a name="add-catchpoint-from-the-gallery"></a>Přidání záchytný bod z Galerie

Pokud chcete nakonfigurovat integraci záchytný bod do služby Azure AD, přidejte záchytný bod do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovní, školní nebo osobní účet Microsoft.
1. V levém podokně vyberte službu **Azure Active Directory** .
1. Přejít na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **záchytný bod** .
1. Na panelu výsledků vyberte **záchytný bod** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-catchpoint"></a>Konfigurace a testování jednotného přihlašování Azure AD pro záchytný bod

Aby jednotné přihlašování fungovalo, musíte propojit uživatele Azure AD s uživatelem v záchytný bod. Pro tento kurz nakonfigurujeme testovacího uživatele s názvem **B. Simon**. 

Dokončete následující části:

1. [NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso), abyste tuto funkci povolili pro vaše uživatele.
    * [Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user), abyste mohli otestovat jednotné přihlašování Azure AD pomocí B. Simon.
    * Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, [přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user).
1. [Nakonfigurujte](#configure-catchpoint-sso)jednotné přihlašování záchytný bod, abyste na straně aplikace nakonfigurovali nastavení jednotného přihlašování.
    * [Vytvořte testovacího uživatele záchytný bod](#create-a-catchpoint-test-user), aby bylo možné propojit účet testovacího prostředí Azure AD B. Simon k podobnému uživatelskému účtu v záchytný bod.
1. [Otestujte jednotné přihlašování](#test-sso), abyste ověřili, že konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pokud chcete povolit jednotné přihlašování Azure AD, postupujte podle těchto kroků Azure Portal:

1. Přihlaste se k webu Azure Portal.
1. Na stránce integrace aplikací **záchytný bod** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte ikonu tužky a upravte základní nastavení **Konfigurace SAML** .

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Nakonfigurujte inicializovaný režim pro záchytný bod:
   - V případě režimu iniciované **IDP** zadejte hodnoty pro následující pole:
     - **Identifikátor**:`https://portal.catchpoint.com/SAML2`
     - Pro **adresu URL odpovědi**: `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`
   - Pro režim inicializovaný v režimu **SP** vyberte **nastavit další adresy URL** a zadejte následující hodnotu:
     - **Přihlašovací adresa URL**:`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Aplikace záchytný bod očekává kontrolní výrazy SAML v určitém formátu. Přidejte mapování vlastních atributů do vaší konfigurace atributů tokenu SAML. Následující tabulka obsahuje seznam výchozích atributů:

    | Name | Zdrojový atribut|
    | ------------ | --------- |
    | GivenName | User. givenneame |
    | příjmení | User. příjmení |
    | EmailAddress | uživatel. pošta |
    | Name | User. userPrincipalName |
    | Jedinečný identifikátor uživatele | User. userPrincipalName |

    ![Uživatelské atributy & snímku seznamu deklarací identity](common/default-attributes.png)

1. Také aplikace záchytný bod očekává, že se do odpovědi SAML předává jiný atribut. Podívejte se na následující tabulku. Tento atribut je také předem vyplněný, ale můžete ho zkontrolovat a aktualizovat tak, aby vyhovoval vašim požadavkům.

    | Name | Zdrojový atribut|
    | ------------ | --------- |
    | namespace | User. assignedrole |

    > [!NOTE]
    > `namespace`Deklarace identity musí být namapovaná s názvem účtu. Tento název účtu by měl být nastavený s rolí v Azure AD, aby se zpátky v odpovědi SAML. Další informace o rolích ve službě Azure AD najdete v tématu [konfigurace deklarace identity role vydané v tokenu SAML pro podnikové aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Přejít na stránku **nastavit jeden Sign-On se** stránkou SAML. V části **podpisový certifikát SAML** Najděte **certifikát (Base64)**. Vyberte **Stáhnout** a uložte certifikát do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení záchytný bod** zkopírujte adresy URL, které budete potřebovat v pozdějším kroku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části použijete Azure Portal k vytvoření testovacího uživatele Azure AD s názvem B. Simon.

1. V levém podokně Azure Portal vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Zadejte například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** . Všimněte si zobrazované hodnoty hesla.
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k záchytný bod.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu aplikace vyberte **záchytný bod**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **B. Simon** . V dolní části obrazovky klikněte na **Vybrat** .
1. Pokud jste nastavili role, jak je vysvětleno výše, můžete je vybrat v rozevíracím seznamu **Vybrat roli** .
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

## <a name="configure-catchpoint-sso"></a>Konfigurace jednotného přihlašování záchytný bod

1. V jiném okně webového prohlížeče se přihlaste k aplikaci záchytný bod jako správce.

1. Vyberte ikonu **Nastavení** a pak **zprostředkovatele identity SSO**.

    ![Snímek obrazovky s nastavením záchytný bod s vybraným poskytovatelem identity SSO](./media/catchpoint-tutorial/configuration1.png)

1. Na stránce **jednotného přihlašování** zadejte následující pole:

   ![Snímek obrazovky se stránkou jednotného přihlašování záchytný bod](./media/catchpoint-tutorial/configuration2.png)

   Pole | Hodnota
   ----- | ----- 
   **Obor názvů** | Platná hodnota oboru názvů.
   **Vystavitel zprostředkovatele identity** | `Azure AD Identifier`Hodnota z Azure Portal.
   **Adresa URL jednotného přihlašování** | `Login URL`Hodnota z Azure Portal.
   **Certifikát** | Obsah staženého `Certificate (Base64)` souboru z Azure Portal. K zobrazení a kopírování použijte Poznámkový blok.

   Můžete také nahrát **XML federačních metadat** , a to tak, že vyberete možnost **nahrát metadata** .

1. Vyberte **Uložit**.

### <a name="create-a-catchpoint-test-user"></a>Vytvořit testovacího uživatele v záchytný bod

Záchytný bod podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části nemáte žádné položky akcí. Pokud B. Simon ještě neexistují jako uživatel v záchytný bod, vytvoří se po ověření.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k záchytný bod, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlášení k záchytný bod přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k záchytný bod, pro které jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici záchytný bod v nabídce Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k záchytný bod, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


> [!NOTE]
> Když jste se přihlásili k aplikaci záchytný bod prostřednictvím přihlašovací stránky, po zadání **přihlašovacích údajů záchytný bod** zadejte platnou hodnotu **oboru názvů** do pole **pověření společnosti (SSO)** a vyberte **přihlašovací jméno**.
> 
> ![Konfigurace záchytný bod](./media/catchpoint-tutorial/loginimage.png)

## <a name="next-steps"></a>Další kroky

Po nakonfigurování záchytný bod můžete vynutili řízení relace. Tato preventivní opatření chrání před exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace je rozšíření podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
