---
title: 'Kurz: Azure Active Directory integrací s tabulí – informace – Shibboleth | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a tabulí – Shibboleth.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: b1b3f265d0e1fcad2953292c5227c2630c6df229
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101649886"
---
# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn---shibboleth"></a>Kurz: Azure Active Directory integrace s tabulí – informace – Shibboleth

V tomto kurzu se dozvíte, jak integrovat příkaz tabule Shibboleth s Azure Active Directory (Azure AD). Když integrujete příkaz tabule Shibboleth s Azure AD, můžete postupovat takto:

* Řízení ve službě Azure AD, která má přístup k ovládacímu prvku tabule učení – Shibboleth.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k programu Tabule informující-Shibboleth s jejich účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:
 
* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Tabule učení – Shibboleth odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Další informace o tabuli – Shibboleth podporuje jednotné přihlašování na základě **SP**

## <a name="add-blackboard-learn---shibboleth-from-the-gallery"></a>Přidání informací o tabuli – Shibboleth z Galerie

Pokud chcete nakonfigurovat integraci tabule Shibboleth do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat tabuli učení-Shibboleth z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **příkaz tabule reučení-Shibboleth** .
1. Z panelu výsledků vyberte **tabule Shibboleth** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-blackboard-learn---shibboleth"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro tabuli – informace – Shibboleth

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí programu Tabule Shibboleth pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v části tabule s učením-Shibboleth.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí programu Tabule Shibboleth, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace tabule učení – SHIBBOLETH SSO](#configure-blackboard-learn---shibboleth-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvoření tabule učení – Shibboleth testovací uživatel](#create-blackboard-learn---shibboleth-test-user)** – Pokud chcete mít protějšek B. Simon v tabuli Shibboleth, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí programu Tabule Shibboleth, proveďte následující kroky:

1. V Azure Portal na stránce **tabule – informace –** integrace aplikace Shibboleth vyberte **jednotné přihlašování**.

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

3. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu tužky a otevřete dialogové okno **základní konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/Login`

    b. Do pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<yourblackoardlearnserver>.blackboardlearn.com/shibboleth-sp`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/SAML2/POST`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným Sign-On URL, identifikátor a adresu URL odpovědi. Pokud chcete získat tyto hodnoty, kontaktujte [tabuli s informací – Shibboleth tým podpory klientů](https://www.blackboard.com/forms/contact-us_form.aspx) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení tabule informování – Shibboleth** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k tabuli učení-Shibboleth.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **tabule – informace – Shibboleth**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="configure-blackboard-learn---shibboleth-sso"></a>Konfigurace učení tabule – Shibboleth SSO

Pokud chcete nakonfigurovat jednotné přihlašování na **tabuli Shibboleth** , je potřeba odeslat stažený **soubor XML federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [tabule Shibboleth support týmu](https://www.blackboard.com/forms/contact-us_form.aspx). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-blackboard-learn---shibboleth-test-user"></a>Vytvoření tabule učení – Shibboleth Test User

V této části vytvoříte uživatele s názvem Britta Simon v tabuli Shibboleth. Práce s nástrojem [tabule učení – Shibboleth Support Team](https://www.blackboard.com/forms/contact-us_form.aspx) umožňuje přidat uživatele na platformě tabule učení-Shibboleth. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tato akce se přesměruje na tabuli Shibboleth přihlašovací adresu URL, kde můžete spustit tok přihlášení. 

* Přejít na tabuli Shibboleth adresa URL pro přihlášení přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Shibboleth tabule v okně moje aplikace, měli byste se automaticky přihlásit k Shibboleth tabule, pro kterou jste si nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Shibboleth tabule můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).