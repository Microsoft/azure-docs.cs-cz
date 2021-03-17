---
title: 'Kurz: Azure Active Directory integrace se softwarem pro správu osobních údajů v OneTrust | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a OneTrust software pro správu ochrany osobních údajů.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: f0a145b0eb9dd9dbed0927ce825a21d8f47c48ab
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648420"
---
# <a name="tutorial-azure-active-directory-integration-with-onetrust-privacy-management-software"></a>Kurz: Azure Active Directory integrace se softwarem pro správu ochrany osobních údajů v OneTrust

V tomto kurzu se dozvíte, jak integrovat software pro správu ochrany osobních údajů OneTrust pomocí služby Azure Active Directory (Azure AD). Když integrujete software pro správu osobních údajů v OneTrust s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k OneTrust softwaru pro správu ochrany osobních údajů.
* Umožněte uživatelům, aby se automaticky přihlásili k OneTrust softwaru pro správu ochrany osobních údajů pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD se softwarem OneTrust Privacy Management potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* OneTrust předplatné s povoleným jednotným přihlašováním softwaru pro správu osobních údajů.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Software pro správu ochrany osobních údajů OneTrust podporuje **aktualizace SP** a **IDP** , iniciované SSO

* Software pro správu ochrany osobních údajů OneTrust podporuje zřizování uživatelů **jenom v čase**

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="add-onetrust-privacy-management-software-from-the-gallery"></a>Přidání softwaru pro správu ochrany osobních údajů OneTrust z Galerie

Pokud chcete nakonfigurovat integraci softwaru pro správu ochrany osobních údajů OneTrust do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat OneTrust software pro správu ochrany osobních údajů z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **OneTrust software pro správu ochrany osobních údajů** .
1. Na panelu výsledků vyberte **OneTrust software pro správu ochrany osobních údajů** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-onetrust-privacy-management-software"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro software pro správu osobních údajů v OneTrust

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí OneTrust softwaru pro správu ochrany osobních údajů pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v OneTrust softwaru pro správu osobních údajů.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí softwaru OneTrust Privacy Management, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace jednotného přihlašování k softwaru OneTrust ochrany osobních údajů](#configure-onetrust-privacy-management-software-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte uživatele testovacího softwaru pro správu osobních údajů v OneTrust](#create-onetrust-privacy-management-software-test-user)** , abyste měli protějšek B. Simon inOneTrust software pro správu ochrany osobních údajů, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.
 
1. V Azure Portal na stránce integrace **OneTrustch softwarových aplikací pro správu soukromí** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **vybrat jednu Sign-On metodu** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    a. Do textového pole **identifikátor** zadejte adresu URL: `https://www.onetrust.com/saml2`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.onetrust.com/auth/consumerservice`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

     Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<subdomain>.onetrust.com/auth/login`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné adresy URL odpovědi a přihlašovací adresy URL. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory OneTrust ochrany osobních údajů pro softwarového klienta](mailto:support@onetrust.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

7. V části **nastavit software pro správu ochrany osobních údajů OneTrust** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k softwaru pro správu ochrany osobních údajů OneTrust.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu aplikace vyberte **OneTrust software pro správu osobních údajů**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte možnost **Přidat uživatele**. Pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **B. Simon** . Pak zvolte **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="configure-onetrust-privacy-management-software-sso"></a>Konfigurace jednotného přihlašování OneTrust pro správu osobních údajů softwaru

Chcete-li nakonfigurovat jednotné přihlašování na straně **softwaru OneTrust Privacy Management** , je třeba odeslat stažený **soubor XML federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory OneTrust pro správu ochrany osobních údajů](mailto:support@onetrust.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-onetrust-privacy-management-software-test-user"></a>Vytvořit uživatele zkušebního softwaru pro správu osobních údajů OneTrust

V této části se ve OneTrust softwaru pro správu ochrany osobních údajů vytvoří uživatel s názvem Britta Simon. Software pro správu osobních údajů OneTrust podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě neexistuje v softwaru OneTrust Privacy Management, vytvoří se po ověření nový.

>[!Note]
>Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory OneTrust Privacy Management](mailto:support@onetrust.com).

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL softwaru OneTrust Privacy Management, kde můžete spustit tok přihlášení.  
 
* Přejít na adresu URL pro přihlašování k softwaru OneTrust Privacy Management přímo a zahajte tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k softwaru pro správu ochrany osobních údajů OneTrust, pro který jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici software pro správu osobních údajů OneTrust v části Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k softwaru OneTrust Privacy Management, pro který jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování OneTrust softwaru pro správu ochrany osobních údajů můžete vynutili řízení relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).