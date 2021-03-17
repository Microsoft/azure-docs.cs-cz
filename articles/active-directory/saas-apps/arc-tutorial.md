---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s obloukem publikováním – SSO | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a publikováním ARC – jednotné přihlašování.
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
ms.openlocfilehash: 0b1be3aa251b0248d3bbd859a183c7483fd9e38c
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735358"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-arc-publishing---sso"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s obloukem publikováním – jednotné přihlašování

V tomto kurzu se dozvíte, jak integrovat publikování ARC – jednotné přihlašování pomocí Azure Active Directory (Azure AD). Při integraci ARC publikování – SSO s Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k publikování ARC – jednotné přihlašování
* Umožněte, aby se vaši uživatelé automaticky přihlásili k publikování pomocí jednotného přihlašování (SSO) s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Publikování ARC – odběr jednotného přihlašování SSO (SSO) s podporou jednotného přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Publikování ARC – jednotné přihlašování podporuje **aktualizace SP a IDP se** iniciovalo.
* Publikování oblouku – jednotné přihlašování podporuje **jenom čas** zřizování uživatelů


## <a name="adding-arc-publishing---sso-from-the-gallery"></a>Přidání publikování oblouku – jednotné přihlašování z Galerie

Pokud chcete konfigurovat integraci ARC pro publikování v rámci služby Azure AD, musíte do svého seznamu spravovaných aplikací pro SaaS přidat ARC pro publikování pomocí jednotného přihlašování z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **příkaz oblouk Publishing-SSO** .
1. Vyberte možnost **publikování ARC – jednotné přihlašování** z panelu výsledků a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-arc-publishing---sso"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro publikování ARC – jednotné přihlašování

Konfigurace a testování jednotného přihlašování služby Azure AD pomocí publikování ARC – jednotné přihlašování s použitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v části publikování v ARC.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí publikování ARC – jednotné přihlašování, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte publikování ARC – jednotné](#configure-arc-publishing---sso-sso)** přihlašování SSO – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvoření publikování ARC – uživatel s testováním jednotného přihlašování](#create-arc-publishing---sso-test-user)** – bude mít protějšek B. Simon v oblouku publikování – SSO, který je propojený s předplatným služby Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce pro integraci aplikace pro **publikování ARC** , najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://www.okta.com/saml2/service-provider/<Unique ID>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Kontaktujte [publikování ARC – tým podpory klienta jednotného přihlašování](mailto:inf@washpost.com) získá tyto hodnoty. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Publikování ARC – aplikace jednotného přihlašování očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/edit-attribute.png)

1. Kromě výše očekává aplikace oblouk Publishing-SSO několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy se také předem naplní, ale můžete je zkontrolovat podle vašich požadavků.


    | Name | Zdrojový atribut|
    | ---------------| --------------- |    
    | firstName | User. křestní jméno |
    | lastName | User. příjmení |
    | e-mail | uživatel. pošta |
    | skupiny | User. assignedroles |

    > [!NOTE]
    > Tady je atribut **Groups** namapován pomocí **User. assignedroles**. Jedná se o vlastní role vytvořené ve službě Azure AD, které mapují názvy skupin zpátky v aplikaci. Další pokyny najdete v [tématu](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) vytvoření vlastních rolí v Azure AD.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V oddílu **Nastavení publikování oblouku – jednotné přihlašování** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k publikování ARC – jednotné přihlašování.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **publikování oblouku – jednotné přihlašování**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud jste nastavili role, jak je vysvětleno výše, můžete je vybrat v rozevíracím seznamu **Vybrat roli** .
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-arc-publishing---sso-sso"></a>Konfigurace publikování ARC – jednotné přihlašování SSO

Chcete-li konfigurovat jednotné přihlašování při **publikování ARC** , je třeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory pro publikování pomocí jednotného přihlašování](mailto:inf@washpost.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-arc-publishing---sso-test-user"></a>Vytvoření publikování ARC – uživatel testu jednotného přihlašování

V této části se uživatel s názvem Britta Simon vytvoří v části publikování ARC – jednotné přihlašování. Publikování ARC – jednotné přihlašování podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel již v nástroji pro publikování ARC neexistuje, vytvoří se po ověření nový.

> [!Note]
> Pokud potřebujete ručně vytvořit uživatele, kontaktujte [tým podpory pro jednotné přihlašování (SSO](mailto:inf@washpost.com)).

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na publikování ARC – na adrese URL pro přihlášení k JEDNOTNÉmu přihlašování můžete zahájit tok přihlášení.  

* Přejít na adresu URL pro publikování ARC – přihlašování pomocí jednotného přihlašování přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k publikování ARC – SSO, pro které jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici pro publikování z publikačního rozhraní v části Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a v případě, že jste nakonfigurovali v režimu IDP, měli byste se automaticky přihlásit k publikačnímu účtu, pro který nastavíte jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po konfiguraci publikování oblouku – jednotné přihlašování můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
