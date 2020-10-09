---
title: 'Kurz: Azure Active Directory integrace s Soloinsight-CloudGate jednotné přihlašování | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Soloinsight-CloudGate jednotné přihlašování.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: jeedes
ms.openlocfilehash: 5ac0f0777ea341036950550e19c5d8e7fb71a91f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88545059"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Kurz: integrace Soloinsight-CloudGate jednotného přihlašování s Azure Active Directory

V tomto kurzu se naučíte, jak integrovat Soloinsight-CloudGate SSO pomocí Azure Active Directory (Azure AD). Když integrujete Soloinsight-CloudGate SSO s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Soloinsight-CloudGate jednotné přihlašování.
* Umožněte uživatelům, aby se automaticky přihlásili k Soloinsight-CloudGate jednotné přihlašování s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat zdarma [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Soloinsight-CloudGate odběr povoleného jednotného přihlašování (SSO) jednotného přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. Soloinsight-CloudGate SSO podporuje jednotné přihlašování (SSO) iniciované **SP** .

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Přidání Soloinsight-CloudGateho jednotného přihlašování z Galerie

Pokud chcete nakonfigurovat integraci Soloinsight-CloudGateho jednotného přihlašování do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Soloinsight-CloudGate SSO z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **SOLOINSIGHT-CloudGate SSO** .
1. Vyberte **Soloinsight-CLOUDGATE SSO** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Soloinsight-CloudGate jednotného přihlašování s použitím testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Soloinsight-CloudGate SSO.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Soloinsight-CloudGate jednotného přihlašování, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
2. **[Nakonfigurujte Soloinsight-CloudGate jednotné](#configure-soloinsight-cloudgate-sso)** přihlašování pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** , aby mohl Britta Simon používat jednotné přihlašování Azure AD.
5. **[Vytvoření Soloinsight-CloudGateho testovacího uživatele jednotného přihlašování](#create-soloinsight-cloudgate-sso-test-user)** , aby měl protějšek Britta Simon v rámci Soloinsight-CloudGate jednotného přihlašování, které je propojené s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **SOLOINSIGHT-CloudGate SSO** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    1. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.sigateway.com/login`

    1. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí vlastního přihlašovacího URL a identifikátoru, který je vysvětlen dále v části **konfigurace Soloinsight-CloudGate jednotného přihlašování** v rámci tohoto kurzu.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte si certifikát a uložte ho do svého počítače.

   ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavit Soloinsight-CloudGate SSO** zkopírujte příslušné adresy URL na základě vašeho požadavku.

   ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>Konfigurace Soloinsight-CloudGate jednotného přihlašování

1. Pokud chcete automatizovat konfiguraci v rámci Soloinsight-CloudGate jednotného přihlašování, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **nastavení Soloinsight-CloudGate jednotné PŘIhlašování** vás přesměruje na Soloinsight-CloudGate aplikace jednotného přihlašování. Odtud zadejte přihlašovací údaje správce pro přihlášení Soloinsight-CloudGate jednotné přihlašování. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-8.

    ![Konfigurace instalace](common/setup-sso.png)

3. Chcete-li nastavit Soloinsight-CloudGate jednotné přihlašování ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu služby Soloinsight-CloudGate SSO pomocí jednotného přihlašování jako správce a proveďte následující kroky:

4. Chcete-li získat hodnoty, které mají být vloženy do Azure Portal při konfiguraci základní SAML, přihlaste se k webovému portálu CloudGate pomocí svých přihlašovacích údajů a potom přejděte k nastavení jednotného přihlašování, které najdete v následující cestě **>správě>nastavení systému>obecné**.

    ![Nastavení jednotného přihlašování CloudGate](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

5. **Adresa URL příjemce SAML**

    * Zkopírujte odkazy dostupné proti **adresám URL příjemce SAML** a **adres URL pro přesměrování** a vložte je do konfiguračního oddílu Azure Portal **Basic SAML** pro **identifikátor (ID entity)** a pole **adresy URL odpovědi** .

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

6. **Podpisový certifikát SAML**

    * Přejděte do zdroje souboru certifikátu (Base64), který jste stáhli ze seznamu Azure Portal podpisových certifikátů SAML, a klikněte na něj pravým tlačítkem. V seznamu vyberte možnost **upravit pomocí poznámkového bloku** . 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Zkopírujte obsah v souboru poznámkového bloku (Base64) Notepad + +.

        ![Kopie certifikátu](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Vložte obsah do pole **certifikát** nastavení jednotného přihlašování webového portálu CloudGate a klikněte na tlačítko Uložit.

        ![Portál certifikátů](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **Výchozí skupina**

    * V rozevíracím seznamu **výchozí skupiny** na webovém portálu CloudGate vyberte možnost **správce firmy** .

        ![Výchozí skupina](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **Identifikátor AD a přihlašovací adresa URL**

    * Zkopírovaná **Adresa URL pro přihlášení** z Azure Portal **nastavit Soloinsight-CloudGate konfigurace jednotného** přihlašování se musí zadat v části nastavení jednotného přihlašování webového portálu CloudGate.

    * Vložte odkaz **Adresa URL pro přihlášení** z Azure Portal do pole **přihlašovací adresa URL** webového portálu CloudGate.

    * Vložte odkaz na **identifikátor Azure AD** z Azure Portal do pole **identifikátor služby AD** webového portálu CloudGate.

        ![Přihlášení k AD](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `Britta Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `BrittaSimon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Soloinsight-CloudGate jednotné přihlašování.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Soloinsight-CLOUDGATE SSO**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Vytvořit Soloinsight-CloudGate zkušebního uživatele jednotného přihlašování

Chcete-li vytvořit testovacího uživatele, vyberte možnost **zaměstnanci** z hlavní nabídky webového portálu CloudGate a vyplňte formulář přidat nového zaměstnance. Úroveň autority, která se má přiřadit k testovacímu uživateli, **je po** vyplňování všech požadovaných polí kliknutí na **vytvořit** .

![Test zaměstnance](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-sso"></a>Test SSO

Když vyberete dlaždici Soloinsight-CloudGate SSO na přístupovém panelu, měli byste se automaticky přihlásit k Soloinsight-CloudGate SSO, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)