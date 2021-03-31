---
title: 'Kurz: Azure Active Directory integrace s autopracovním pracovištěm | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a úlohou automatického pracovního pracoviště.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: 1a2602ee3b8fad6e87a778ab966f7cb1f8aad8c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649934"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Kurz: Integrace Azure Active Directory s autopracovním pracovištěm

V tomto kurzu se dozvíte, jak integrovat pracovní plochu AutoTask pomocí Azure Active Directory (Azure AD). Když integrujete pracovní plochu AutoTask v Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k pracovním prostorům pro autoúlohu.
* Umožněte uživatelům, aby se automaticky přihlásili k automatickému úkolu na pracovišti s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Odběr automatického přihlašování na pracovišti pro jednotné přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Pracovní prostor autopracovní pracoviště podporuje **aktualizace SP a IDP, které** bylo iniciováno.

## <a name="add-autotask-workplace-from-the-gallery"></a>Přidání úlohy autopracovní plocha z Galerie

Pokud chcete nakonfigurovat integraci autopracovní pracoviště do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat pracovní plochu pro vzdálenou úlohu z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole text **AutoTask pracoviště** .
1. Na panelu výsledků vyberte možnost **pracovní plocha AutoTask** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-autotask-workplace"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro pracovní plochu AutoTask

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí úlohy autopracovní plocha pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci úlohy autopracovní plocha.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí úlohy autopracovní plocha, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte jednotné přihlašování k pracovní ploše](#configure-autotask-workplace-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte si uživatele s Autopracovní pracovištěm pro testování](#create-autotask-workplace-test-user)** , abyste měli protějšek B. Simon ve službě automatického úkolu, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce pro integraci aplikací automatického **pracovního pracoviště** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Snímek obrazovky ukazuje základní konfiguraci SAML, kde můžete zadat identifikátor, odpovědět U R L a vybrat Uložit.](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Snímek obrazovky s nastavením další U R ls, kde můžete zadat přihlášení U R L.](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<subdomain>.awp.autotask.net/loginsso`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory pro pracovní plochu AutoTask](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

7. V části **nastavit pracovní plochu Autopracovní pracoviště** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k pracovní ploše automatického úkolu.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **pracovní plocha pro autoúlohu**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-autotask-workplace-sso"></a>Konfigurace jednotného přihlašování k pracovní ploše pro úlohy

1. V jiném okně webového prohlížeče se přihlaste k pracovní ploše online pomocí přihlašovacích údajů správce.

    > [!Note]
    > Při konfiguraci IdP bude nutné zadat subdoménu. Pokud chcete potvrdit správnou subdoménu, přihlaste se k pracovní ploše online. Po přihlášení si poznamenejte poddoménu v adrese URL. Subdoména je součástí mezi "https://" a ". awp.autotask.net/" a měla by být US, EU, CA nebo au.

2. Přejít na **Konfigurace**  >  **jednotného přihlašování** a proveďte následující kroky:

    ![Konfigurace automatického přihlašování k jednotnému úkolu](./media/autotaskworkplace-tutorial/configuration-1.png)

    a. Vyberte možnost **soubor metadat XML** a poté nahrajte stažené **federační Metadata XML** z Azure Portal.

    b. Klikněte na **Povolit jednotné přihlašování**.

    ![Konfigurace automatického přihlašování k jednotnému úkolu pro schválení](./media/autotaskworkplace-tutorial/configuration-2.png)

    c. Zaškrtněte políčko **potvrzuji, že tyto informace jsou správné a toto zaškrtávací políčko je důvěryhodné pro IDP** .

    d. Klikněte na tlačítko **schválit**.

> [!Note]
> Pokud potřebujete pomoc s konfigurací pracovního prostředí pro vzdálenou úlohu, přečtěte si prosím na [této stránce](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) , kde získáte pomoc s účtem na pracovišti.

### <a name="create-autotask-workplace-test-user"></a>Vytvořit úlohu pro test uživatele autopracovní plocha

V této části vytvoříte uživatele s názvem Britta Simon na pracovišti pro autoúlohu. Pokud chcete přidat uživatele na platformě autopracovní pracoviště autopracovní plocha, obraťte se na [tým podpory](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) autopracovní pracoviště.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro autoúlohu na přihlášení na pracovišti, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL automatického úkolu pro přihlášení k pracovišti přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste být automaticky přihlášení k pracovní ploše automatického úkolu, pro kterou jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici automaticky prohledat pracovní plochu v části Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k pracovní ploše automatického úkolu, pro kterou jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování pracovní plochy pro autoúlohu můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).