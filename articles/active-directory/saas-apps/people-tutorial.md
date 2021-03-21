---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s lidmi | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a lidmi.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 43edf62a625d80de0a5de8e0924e771d68a595e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101647094"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-people"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s lidmi

V tomto kurzu se dozvíte, jak integrovat lidi s Azure Active Directory (Azure AD). Když integrujete lidi s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k lidem
* Umožněte uživatelům, aby se automaticky přihlásili k uživatelům pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Odběr povolený jednotného přihlašování (SSO) pro lidi

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Lidé podporují jednotné přihlašování, které iniciuje **SP**
* Mobilní aplikace pro lidi se teď dá nakonfigurovat se službou Azure AD pro povolení jednotného přihlašování. V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

>[!NOTE]
>Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="add-people-from-the-gallery"></a>Přidat lidi z Galerie

Pokud chcete nakonfigurovat integraci lidí do Azure AD, musíte přidat lidi z Galerie do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **osoby** .
1. Vyberte **lidé** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-people"></a>Konfigurace a testování jednotného přihlašování Azure AD pro lidi

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s lidmi pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v lidech.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s lidmi, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
2. **[Nakonfigurujte jednotné přihlašování pro lidi](#configure-people-sso)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
    1. **[Vytvořte uživatele s testovacím uživatelem](#create-people-test-user)** – abyste měli protějšek B. Simon v lidech, kteří jsou propojení s reprezentací uživatele v Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací pro **lidi** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<company name>.peoplehr.net`

    b. Do pole **identifikátor** zadejte adresu URL: `https://www.peoplehr.com`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<company name>.peoplehr.net/Pages/Saml/ConsumeAzureAD.aspx`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečnou adresou URL Sign-On a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, kontaktujte [tým podpory pro pracovníky](mailto:customerservices@peoplehr.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavit osoby** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup lidem.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **osoby**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-people-sso"></a>Konfigurace jednotného přihlašování uživatelů

1. Pokud chcete automatizovat konfiguraci v rámci lidí, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **nastavit osoby** , které vás přesměrují do aplikace lidé. Odtud zadejte přihlašovací údaje správce, které se budou přihlašovat lidem. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Konfigurace instalace](common/setup-sso.png)

3. Chcete-li nastavit osoby ručně, otevřete nové okno webového prohlížeče a přihlaste se k firemnímu webu osoby jako správce a proveďte následující kroky:
   
4. V nabídce na levé straně klikněte na **Nastavení**.

    ![Snímek obrazovky, který zobrazuje levou stranu nabídky s vybranou možnosti "nastavení".](./media/people-tutorial/settings.png)

5. Klikněte na **Společnost**.

    ![Snímek obrazovky, který zobrazuje "společnost" vybraný v nabídce nastavení.](./media/people-tutorial/company.png)

6. V **souboru METADAT SAML pro nahrání jednotného přihlašování** klikněte na **Procházet** a nahrajte stažený soubor metadat.

    ![Konfigurace jednoho Sign-On](./media/people-tutorial/xml.png)

### <a name="create-people-test-user"></a>Vytvořit lidi testovacího uživatele

V této části vytvoříte uživatele s názvem B. Simon v lidech. Pracujte s [týmem podpory klientů](mailto:customerservices@peoplehr.com) pro uživatele, kteří budou přidávat uživatele na platformě lidí. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlašování uživatelů, kde můžete spustit tok přihlášení. 

* Přejít přímo na adresu URL pro přihlašování uživatelů a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici lidé v nabídce Moje aplikace, přesměruje se na přihlašovací adresu URL pro lidi. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="test-sso-for-people-mobile"></a>Testování jednotného přihlašování pro lidi (mobilní zařízení)

1. Mobilní aplikace otevřené pro lidi Na přihlašovací stránce zadejte **ID e-mailu** a potom klikněte na **jednotné přihlašování**.

    ![Přihlášení](./media/people-tutorial/test01.png)

2. Zadejte **ID organizace** a klikněte na **Další**.

    ![E-mail](./media/people-tutorial/test02.png)

3. Nakonec po úspěšném přihlášení se zobrazí domovská stránka aplikace, která je uvedená níže:

    ![Jednou](./media/people-tutorial/test03.png)

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete lidi, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).