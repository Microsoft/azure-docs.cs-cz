---
title: 'Kurz: Integrace jednotného přihlašování (SSO) služby Azure Active Directory s bodem Catchpoint'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Catchpointem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab3eead7-8eb2-4c12-bb3a-0e46ec899d37
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b19e286d299811a950df05f93d221bd710676ea
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743490"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-catchpoint"></a>Kurz: Integrace jednotného přihlašování služby Azure Active Directory s catchpointem

V tomto kurzu se dozvíte, jak integrovat Catchpoint s Azure Active Directory (Azure AD). Když integrujete Catchpoint s Azure AD, můžete:

* Řízení přístupu uživatelů k Catchpoint z Azure AD.
* Povolte automatické přihlášení catchpointu pro uživatele s účty Azure AD.
* Spravujte své účty na jednom centrálním místě: na portálu Azure.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte předplatné, můžete získat bezplatný [účet](https://azure.microsoft.com/free/).
* Catchpoint předplatné s jedním přihlášením (SSO) povolena.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete přisypná ní služby Azure AD v testovacím prostředí.

* Catchpoint podporuje sp inicioval a IDP inicioval saso.
* Catchpoint podporuje zřizování uživatelů za čas (JIT).
* Po konfiguraci Catchpoint, můžete vynutit řízení relace. Toto opatření chrání před exfiltrací a infiltrací citlivých dat vaší organizace v reálném čase. Řízení relace je rozšíření podmíněného přístupu. [Přečtěte si, jak vynutit řízení relací pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-catchpoint-from-the-gallery"></a>Přidat Catchpoint z galerie

Pokud chcete nakonfigurovat integraci Catchpointu do Azure AD, přidejte Catchpoint do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí pracovního, školního nebo osobního účtu Microsoft.
1. V levém podokně vyberte službu **Azure Active Directory.**
1. Přejděte do **podnikových aplikací** a vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte **možnost Nová aplikace**.
1. V části **Přidat z galerie** zadejte do vyhledávacího pole **catchpoint.**
1. Z panelu výsledků vyberte **Catchpoint** a přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-catchpoint"></a>Konfigurace a testování jednotného přihlášení Azure AD pro Catchpoint

Aby služby Přihlašovalo, je třeba propojit uživatele Azure AD s uživatelem v Catchpointu. Pro účely tohoto kurzu nakonfigurujeme testovacího uživatele s názvem **B.Simon**. 

Vyplňte následující oddíly:

1. [Nakonfigurujte služby Přizpůsobované ho sapozičisti Azure](#configure-azure-ad-sso)a umožňují uživatelům tuto funkci.
    * [Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user), chcete-li otestovat jednotné přihlašování Azure AD pomocí B.Simon.
    * [Přiřaďte uživateli testu Azure AD](#assign-the-azure-ad-test-user), aby b.Simon mohl používat jednotné přihlašování Azure AD.
1. [Konfigurace catchpoint jednotného](#configure-catchpoint-sso)přihlašování , ke konfiguraci nastavení jednotného přihlášení na straně aplikace.
    * [Vytvořte catchpoint test uživatele](#create-a-catchpoint-test-user), chcete-li povolit propojení b.Simon Azure AD testovací účet na podobný uživatelský účet v Catchpoint.
1. [Otestujte sso](#test-sso), chcete-li ověřit, že konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Podle následujících kroků na webu Azure Portal povolíte přisazožení azure ad:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
1. Na stránce integrace aplikace **Catchpoint** najděte oddíl **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vybrat metodu jednotného přihlašování** vyberte **možnost SAML**.
1. Na stránce **Nastavit jednotné přihlašování pomocí saml** vyberte ikonu pera a upravte základní nastavení **konfigurace SAML.**

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Nakonfigurujte inicializovat režim catchpointu:
   - V režimu iniciovaném idiciovně **iniciovaného z idiciace IDP**zadejte hodnoty pro následující pole:
     - Pro **identifikátor**:`https://portal.catchpoint.com/SAML2`
     - Pro **adresu URL odpovědi**:`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`
   - V režimu iniciovaném **sp**vyberte **Nastavit další adresy URL** a zadejte následující hodnotu:
     - Pro **přihlašovací adresu URL**:`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Aplikace Catchpoint očekává kontrolní výrazy SAML v určitém formátu. Přidejte vlastní mapování atributů do konfigurace atributů tokenů SAML. Následující tabulka obsahuje seznam výchozích atributů:

    | Name (Název) | Atribut zdroje|
    | ------------ | --------- |
    | Křestní jméno | user.givenneame |
    | příjmení | user.příjmení |
    | Emailaddress | user.mail |
    | Name (Název) | user.userprincipalname |
    | Jedinečný identifikátor uživatele | user.userprincipalname |

    ![Snímek obrazovky se seznamem Atributy uživatelů & deklarací](common/default-attributes.png)

1. Také catchpoint aplikace očekává, že jiný atribut, který má být předán v odpovědi SAML. Podívejte se na následující tabulku. Tento atribut je také předem vyplněný, ale můžete jej zkontrolovat a aktualizovat tak, aby vyhovoval vašim požadavkům.

    | Name (Název) | Atribut zdroje|
    | ------------ | --------- |
    | namespace | user.assignedrole |

    > [!NOTE]
    > Deklarace `namespace` musí být mapována s názvem účtu. Tento název účtu by měl být nastaven s rolí ve službě Azure AD, která má být předána zpět v odpovědi SAML. Další informace o rolích ve službě Azure AD najdete v [tématu Konfigurace deklarace role vydané v tokenu SAML pro podnikové aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management).

1. Přejděte na stránku **Nastavení jednotného přihlášení pomocí saml.** V části **podpisový certifikát SAML** vyhledejte **certifikát (Base64).** Výběrem **možnosti Stáhnout** uložte certifikát do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavit catchpoint** zkopírujte adresy URL, které potřebujete, v pozdějším kroku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části použijete portál Azure k vytvoření uživatele testu Azure AD s názvem B.Simon.

1. V levém podokně na webu Azure Portal vyberte**Možnost Uživatelé služby** >  **Azure Active Directory** > **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel.**
1. Ve vlastnostech **Uživatele** postupujte takto:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **Uživatelské jméno** username@companydomain.extensionzadejte . Zadejte například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo.** Poznamenejte si zobrazenou hodnotu hesla.
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B.Simon používat Azure jednotné přihlašování udělením přístupu catchpoint.

1. Na portálu Azure vyberte **Podnikové aplikace** > **Všechny aplikace**.
1. V seznamu aplikací vyberte **Catchpoint**.
1. Na stránce s přehledem aplikace najděte část **Spravovat** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

1. V **dialogovém okně Přidat** **přiřazení** vyberte Přidat uživatele a pak vyberte **Uživatelé a skupiny.**

    ![Odkaz "Přidat uživatele"](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte ze seznamu uživatelů **b.simon.** V dolní části obrazovky klikněte na **Vybrat.**
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, podívejte se do dialogového okna **Vybrat roli** a zvolte roli uživatele ze seznamu. Klepněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** vyberte **Přiřadit**.

## <a name="configure-catchpoint-sso"></a>Konfigurace szabezpečení místa catchpoint

1. V jiném okně webového prohlížeče se přihlaste k aplikaci Catchpoint jako správce.

1. Vyberte ikonu **Nastavení** a potom **zprostředkovatele identity přisuzušovacího zabezpečení**.

    ![Snímek obrazovky s vybraným zprostředkovatelem identity služby ASO](./media/catchpoint-tutorial/configuration1.png)

1. Na stránce **Jednotné přihlášení** zadejte následující pole:

   ![Snímek obrazovky stránky Catchpoint Single Sign On](./media/catchpoint-tutorial/configuration2.png)

   Pole | Hodnota
   ----- | ----- 
   **Namespace** | Platná hodnota oboru názvů.
   **Vydavatel zprostředkovatele identity** | Hodnota `Azure AD Identifier` z portálu Azure.
   **Adresa URL jednotného přihlášení** | Hodnota `Login URL` z portálu Azure.
   **Certifikát** | Obsah staženého `Certificate (Base64)` souboru z webu Azure Portal. Pomocí poznámkového bloku můžete zobrazit a zkopírovat.

   Xml **metadat federace** můžete také nahrát výběrem možnosti **Nahrát metadata.**

1. Vyberte **Uložit**.

### <a name="create-a-catchpoint-test-user"></a>Vytvoření testovacího uživatele catchpointu

Catchpoint podporuje zřizování uživatelů just-in-time, které je ve výchozím nastavení povoleno. V této části nejsou žádné položky akce. Pokud B.Simon již neexistuje jako uživatel v Catchpoint, je vytvořen po ověření.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí portálu Moje aplikace.

Když vyberete dlaždici Catchpoint na portálu Moje aplikace, měli byste být automaticky přihlášeni k aplikaci Catchpoint s nakonfigurovaným přihlašovaným přihlašovaným. Další informace o portálu Moje aplikace najdete [v tématu Přihlášení a spouštění aplikací z portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

> [!NOTE]
> Když jste přihlášeni k aplikaci Catchpoint prostřednictvím přihlašovací stránky, po zadání **pověření catchpointu**zadejte platnou hodnotu **oboru názvů** do pole Pověření **společnosti (SSO)** a vyberte **Přihlásit**se .
> 
> ![Konfigurace catchpointu](./media/catchpoint-tutorial/loginimage.png)

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte Catchpoint pomocí Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
