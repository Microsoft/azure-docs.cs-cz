---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování se sadou nitro produktivity | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a nitro produktivní sadou.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 3280c97e735e68aa36f018a8de59964ade9567b7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181896"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nitro-productivity-suite"></a>Kurz: Azure Active Directory integraci jednotného přihlašování se sadou nitro produktivity

V tomto kurzu se dozvíte, jak integrovat nitro produktivní sadu s Azure Active Directory (Azure AD). Když integrujete sadu nitro produktivity s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k sadě nitro produktivity.
* Umožněte uživatelům, aby se automaticky přihlásili k nitro produktivní sadě pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Na začátek budete potřebovat:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Nitro produktivní Suite [Enterprise](https://www.gonitro.com/pricing).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* NITRO produktivní sada podporuje **aktualizace SP** a **IDP** , které iniciovaly jednotné přihlašování.
* Sada nitro produktivity podporuje **jenom při** zřizování uživatelů.

## <a name="add-nitro-productivity-suite-from-the-gallery"></a>Přidání sady produktivity nitro z Galerie

Pokud chcete nakonfigurovat integraci sady nitro produktivity do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat nitro sadu produktivity z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém podokně vyberte **Azure Active Directory**.
1. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **nitro produktivní sada** .
1. Z výsledků vyberte **nitro produktivní sada** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-nitro-productivity-suite"></a>Konfigurace a testování jednotného přihlašování Azure AD pro sadu nitro produktivitu

Nakonfigurujte a otestujte jednotné přihlašování Azure AD se sadou nitro produktivity pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojený vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v nitro produktivní sadě.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí sady nitro produktivity, dokončete následující stavební bloky:

1. [NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso) , aby vaši uživatelé mohli používat tuto funkci.

    a. [Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user) pro testování jednotného přihlašování Azure AD pomocí B. Simon.
    
    b. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, [přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user) .
    
2. [Vytvořte testovacího uživatele nitro produktivní sady](#create-a-nitro-productivity-suite-test-user) , abyste měli protějšek B. Simon v sadě nitro produktivity, která je propojená s reprezentací uživatele v Azure AD.
1. [Otestujte jednotné přihlašování](#test-sso) a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **nitro produktivní sada** najděte část **Správa** . Vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. V části **podpisový certifikát SAML** Najděte **certifikát (Base64)**. Vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Snímek obrazovky s oddílem podpisového certifikátu SAML se zvýrazněným odkazem pro stažení](common/certificatebase64.png)
    
1. V části **nastavení sady nitro produktivity** vyberte ikonu Kopírovat vedle **přihlašovací adresy URL**.
    
    ![Snímek obrazovky s nastavením pro sadu nitro produktivity se zvýrazněnými adresami URL a ikonami kopírování](common/copy-configuration-urls.png)
    
1. V [portálu pro správu nitro](https://admin.gonitro.com/)na stránce **Nastavení organizace** vyhledejte část **jednotného přihlašování** . Vyberte **nastavit jednotné přihlašování SAML**.

    a. Vložte **přihlašovací adresu URL** z předchozího kroku do pole **Adresa URL pro přihlášení** .
    
    b. Nahrajte **certifikát (Base64)** z předchozího kroku v poli **podpisový certifikát x509** .
    
    c. Vyberte **Odeslat**.
    
    d. Vyberte **Povolit jednotné přihlašování**.


1. Vraťte se na [Azure Portal](https://portal.azure.com/). Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Snímek obrazovky nastavení jednoho Sign-On se stránkou SAML se zvýrazněnou ikonou tužky](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v části **základní konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zkopírujte a vložte pole **ID entity SAML** z [portálu pro správu nitro](https://admin.gonitro.com/). Měl by mít následující vzor: `urn:auth0:gonitro-prod:<ENVIRONMENT>`

    b. Do textového pole **Adresa URL odpovědi** zkopírujte a vložte pole **Adresa URL služby ACS** z portálu pro [správu nitro](https://admin.gonitro.com/). Měl by mít následující vzor: `https://gonitro-prod.eu.auth0.com/login/callback?connection=<ENVIRONMENT>`

1. Vyberte **nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://sso.gonitro.com/login`

1. Vyberte **Uložit**.

1. Aplikace nitro produktivní Suite očekává, že kontrolní výrazy SAML mají být v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![Snímek obrazovky s výchozími atributy](common/default-attributes.png)

1. Kromě předchozích atributů očekává aplikace nitro produktivní sada několik dalších atributů, které se vrátí zpět v odpovědi SAML. Tyto atributy jsou předem vyplněné, ale můžete je zkontrolovat podle svých požadavků.
    
    | Name  |  Zdrojový atribut|
    | ---------------| --------------- |
    | employeeNumber |  User. objectID |


### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a potom heslo zapište.
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k sadě nitro produktivity.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu aplikace vyberte **nitro produktivní sada**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte možnost **Přidat uživatele**. Pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **B. Simon** . Pak zvolte **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-nitro-productivity-suite-test-user"></a>Vytvoření testovacího uživatele nitro produktivní sady

NITRO produktivní sada podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. Neexistují žádné další akce, které byste mohli provést. Pokud uživatel ještě v sadě nitro produktivity neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

1. Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL nitro sady pro zvýšení produktivity, kde můžete spustit tok přihlášení.  

2. Přejít na adresu URL pro přihlašování k nitro produktivní sadě přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k sadě produktivity nitro, pro kterou jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít panel Microsoft Access. Když kliknete na dlaždici nitro produktivní sada na přístupovém panelu, pokud se nakonfiguruje v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k sadě nitro produktivity, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete sadu produktivity nitro, můžete vymáhat ovládací prvky relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Ovládací prvky relace se rozšíří z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).