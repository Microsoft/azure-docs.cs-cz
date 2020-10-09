---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s EasySSO pro Bamboo | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a EasySSO pro Bamboo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/28/2020
ms.author: jeedes
ms.openlocfilehash: 651f2b39de457a0d93fd39f841540d50273da3af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88555580"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bamboo"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s EasySSO pro Bamboo

V tomto kurzu se dozvíte, jak integrovat EasySSO pro Bamboo s Azure Active Directory (Azure AD). Při integraci EasySSO pro Bamboo se službou Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k Bamboo.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Bamboo svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* EasySSO pro odběr Bamboo jednotného přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* EasySSO for Bamboo podporuje **SP a IDP** inicioval jednotné přihlašování.
* EasySSO for Bamboo podporuje zřizování uživatelů **jenom v čase**
* Po nakonfigurování EasySSO pro Bamboo můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-easysso-for-bamboo-from-the-gallery"></a>Přidání EasySSO pro Bamboo z Galerie

Pokud chcete nakonfigurovat integraci EasySSO pro Bamboo do služby Azure AD, musíte přidat EasySSO pro Bamboo z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **EasySSO for Bamboo** .
1. Z panelu výsledků vyberte **EasySSO for Bamboo** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-bamboo"></a>Konfigurace a testování jednotného přihlašování Azure AD pro EasySSO pro Bamboo

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí EasySSO pro Bamboo pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v EasySSO pro Bamboo.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí EasySSO pro Bamboo, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte EasySSO pro jednotné](#configure-easysso-for-bamboo-sso)** přihlašování Bamboo – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte EasySSO pro Bamboo testovacího uživatele](#create-easysso-for-bamboo-test-user)** – Pokud chcete mít protějšek B. Simon v EasySSO pro Bamboo, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce Application Integration **EasySSO for Bamboo** vyhledejte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Obraťte se na [tým podpory EasySSO](mailto:support@techtime.co.nz) a získejte tyto hodnoty, pokud jsou nejisté. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aplikace EasySSO for Bamboo očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace EasySSO for Bamboo u odpovědi SAML méně dalších atributů, které jsou uvedené dál. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.
    
    | Název | Zdrojový atribut |
    | ---------------| --------- |
    | urn: OID: 0.9.2342.19200300.100.1.1 | User. userPrincipalName |
    | urn: OID: 0.9.2342.19200300.100.1.3 | uživatel. pošta |
    | urn: OID: 2.16.840.1.113730.3.1.241 | User. DisplayName |
    | urn: OID: 2.5.4.4 | User. příjmení |
    | urn: OID: 2.5.4.42 | User. křestní jméno |
    
    V případě, že mají uživatelé Azure AD nakonfigurovaný **sAMAccountName** , budete muset mapovat **název urn: OID: 0.9.2342.19200300.100.1.1** na atribut **sAMAccountName** .
    
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na možnost **Stáhnout** odkazy na **certifikát (Base64)** nebo **metadata federačních metadat** a v počítači uložte buď nebo vše. Později budete potřebovat nakonfigurovat Bamboo EasySSO.

    ![Odkaz na stažení certifikátu](./media/easysso-for-bamboo-tutorial/certificate.png)
    
    Pokud máte v úmyslu provést EasySSO pro konfiguraci Bamboo ručně s certifikátem, musíte také zkopírovat **adresu URL pro přihlášení** a **identifikátor Azure AD** z níže uvedené části a uložit je do počítače.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k EasySSO pro Bamboo.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **EasySSO pro Bamboo**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-easysso-for-bamboo-sso"></a>Konfigurace EasySSO pro jednotné přihlašování Bamboo

1. Přihlaste se k instanci Atlassian Bamboo s oprávněními správce a přejděte do části **Správa aplikací** . 

    ![Správa aplikací](./media/easysso-for-bamboo-tutorial/bamboo-admin-1.png)

2. Na levé straně vyhledejte **EasySSO** a klikněte na něj.

    ![Snadné jednotné přihlašování](./media/easysso-for-bamboo-tutorial/bamboo-admin-2.png)

3. Vyberte možnost **SAML** . Tím přejdete do konfiguračního oddílu SAML.

    ![SAML](./media/easysso-for-bamboo-tutorial/bamboo-admin-3.png)

4. V horní části karty vybrat **certifikáty** a zobrazí se tato obrazovka:

    ![Adresa URL metadat](./media/easysso-for-bamboo-tutorial/bamboo-admin-4.png)

5. Teď Najděte **certifikát (Base64)** nebo **soubor metadat** , který jste uložili v předchozích krocích konfigurace **jednotného přihlašování služby Azure AD** . Máte následující možnosti, jak pokračovat:

    a. Použijte soubor federačních **metadat** aplikace, který jste stáhli do místního souboru v počítači. Vyberte možnost **nahrát** přepínač a postupujte podle dialogového okna pro nahrání souboru, které je specifické pro váš operační systém.

    **OR**

    b. Otevřete soubor federačních **metadat** aplikace a zobrazte obsah (v libovolném textovém editoru) souboru a zkopírujte ho do schránky. Vyberte možnost **vstup** a vložte obsah schránky do textového pole.
 
    **OR**

    c.  Plně ruční konfigurace. Otevřete certifikát federační aplikace **(Base64)** , abyste zobrazili obsah (v libovolném textovém editoru) souboru a zkopírovali ho do schránky. Vložte ho do textového pole **IDP tokeny podepisování certifikátů** . Pak přejděte na kartu **Obecné** a vyplňte **pole Adresa URL příspěvku** a **ID entity** příslušnými hodnotami pro **přihlašovací adresu URL** a **identifikátor služby Azure AD** , který jste předtím uložili.
 
6. V dolní části stránky klikněte na tlačítko **Uložit** . Zobrazí se obsah metadat nebo souborů certifikátů, které se analyzují do polí konfigurace. Konfigurace EasySSO pro Bamboo se dokončila.

7. Pro účely nejlepšího testování přejděte na kartu **vzhled &** a zkontrolujte možnost tlačítko pro **přihlášení SAML** na. Tím se povolí samostatné tlačítko na obrazovce pro přihlášení k Bamboo, které vám konkrétně umožní otestovat Azure AD SAML Integration end na konec. Toto tlačítko můžete nechat zapnuté a nakonfigurovat jeho umístění, barvu a překlad pro režim výroby.

    ![Vzhled &](./media/easysso-for-bamboo-tutorial/bamboo-admin-5.png)

    > [!NOTE]
    > Pokud máte nějaké problémy, obraťte se prosím na [tým podpory EasySSO](mailto:support@techtime.co.nz).

### <a name="create-easysso-for-bamboo-test-user"></a>Vytvořit EasySSO pro testovacího uživatele Bamboo

V této části se v Bamboo vytvoří uživatel s názvem Britta Simon. EasySSO for Bamboo podporuje zřizování uživatelů za běhu, které je ve výchozím nastavení **zakázáno** . Pokud chcete povolit zřizování uživatelů, musíte explicitně zaškrtnout možnost **vytvořit uživatele při úspěšném přihlášení** v části Obecné v konfiguraci modulu plug-in EasySSO. Pokud uživatel ještě v Bamboo neexistuje, vytvoří se po ověření nový.

Pokud však nechcete povolit Automatické zřizování uživatelů při prvním přihlášení uživatele, musí existovat uživatelé v adresářích uživatelů back-end, které instance Bamboo využívají, jako je například LDAP nebo Atlassian.

![Zřizování uživatelů](./media/easysso-for-bamboo-tutorial/bamboo-admin-6.png)

## <a name="test-sso"></a>Test SSO 

### <a name="idp-initiated-workflow"></a>Pracovní postup iniciované IdP

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici EasySSO for Bamboo na přístupovém panelu, měli byste se automaticky přihlásit k instanci Bamboo, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

### <a name="sp-initiated-workflow"></a>Pracovní postup iniciované v SP

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí Bamboo tlačítka pro **přihlášení SAML** .

![Uživatelské přihlášení SAML](./media/easysso-for-bamboo-tutorial/bamboo-admin-7.png)

V tomto scénáři se předpokládá, že jste povolili **tlačítko pro přihlášení SAML** na kartě **Hledat & chování** na stránce konfigurace EasySSO pro Bamboo (viz výše). Otevřete přihlašovací adresu URL Bamboo v režimu prohlížeče anonymním, abyste se vyhnuli jakémukoli rušení s vašimi stávajícími relacemi. Klikněte na tlačítko pro **přihlášení SAML** a budete přesměrováni na tok ověřování uživatelů Azure AD. Po úspěšném dokončení budete přesměrováni zpátky do své Bamboo instance jako ověřený uživatel prostřednictvím SAML.

Po přesměrování zpátky z Azure AD se může zobrazit následující obrazovka.

![Obrazovka chyby EasySSO](./media/easysso-for-bamboo-tutorial/bamboo-admin-8.png)

V takovém případě musíte podle [pokynů na této stránce]( https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS) získat přístup k souboru **Atlassian-Bamboo. log** . Podrobnosti o chybě budou k dispozici na základě ID odkazu, které najdete na chybové stránce EasySSO.

Pokud máte problémy s vyčtením zpráv protokolu, obraťte se prosím na [tým podpory EasySSO](mailto:support@techtime.co.nz).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si EasySSO pro Bamboo s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chránit EasySSO pro Bamboo s pokročilými viditelnostmi a ovládacími prvky](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)