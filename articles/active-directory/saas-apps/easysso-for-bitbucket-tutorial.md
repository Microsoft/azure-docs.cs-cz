---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s EasySSO pro BitBucket | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a EasySSO pro BitBucket.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3ed96e97-b590-4dca-8d00-36288444c641
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5c89693c49e7a5f24308d82c7277ca0bc3a7ae2
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/26/2020
ms.locfileid: "83854382"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bitbucket"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s EasySSO pro BitBucket

V tomto kurzu se dozvíte, jak integrovat EasySSO pro BitBucket s Azure Active Directory (Azure AD). Při integraci EasySSO pro BitBucket se službou Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k EasySSO pro BitBucket.
* Umožněte uživatelům, aby se automaticky přihlásili k EasySSO pro BitBucket s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* EasySSO pro odběr BitBucket jednotného přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* EasySSO for BitBucket podporuje **SP a IDP** inicioval jednotné přihlašování.
* EasySSO for BitBucket podporuje zřizování uživatelů **jenom v čase**
* Po nakonfigurování EasySSO pro BitBucket můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-easysso-for-bitbucket-from-the-gallery"></a>Přidání EasySSO pro BitBucket z Galerie

Pokud chcete nakonfigurovat integraci EasySSO pro BitBucket do služby Azure AD, musíte přidat EasySSO pro BitBucket z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **EasySSO for Bitbucket** .
1. Z panelu výsledků vyberte **EasySSO for Bitbucket** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-bitbucket"></a>Konfigurace a testování jednotného přihlašování Azure AD pro EasySSO pro BitBucket

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí EasySSO pro BitBucket pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v EasySSO pro BitBucket.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí EasySSO pro BitBucket, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte EasySSO pro jednotné](#configure-easysso-for-bitbucket-sso)** přihlašování Bitbucket – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte EasySSO pro Bitbucket testovacího uživatele](#create-easysso-for-bitbucket-test-user)** – Pokud chcete mít protějšek B. Simon v EasySSO pro BitBucket, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce Application Integration **EasySSO for Bitbucket** vyhledejte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/easysso/saml`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/easysso/saml`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, kontaktujte [EasySSO pro tým podpory klientů Bitbucket](mailto:support@techtime.co.nz) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aplikace EasySSO for BitBucket očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace EasySSO for BitBucket u odpovědi SAML méně dalších atributů, které jsou uvedené dál. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Name |  Zdrojový atribut|
    | --------------- | --------- |
    | urn: OID: 2.16.840.1.113730.3.1.241 | User. DisplayName |
    | urn: OID: 2.5.4.42 | User. křestní jméno |
    | urn: OID: 2.5.4.4 | User. příjmení |
    | urn: OID: 0.9.2342.19200300.100.1.1 | User. userPrincipalName|
    | urn: OID: 0.9.2342.19200300.100.1.3 | User. userPrincipalName |
    | | |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k EasySSO pro BitBucket.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **EasySSO pro Bitbucket**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-easysso-for-bitbucket-sso"></a>Konfigurace EasySSO pro jednotné přihlašování BitBucket

1. Přihlaste se k EasySSO pro instanci BitBucket s oprávněními správce a přejděte do části **Správa aplikací** .

    ![Správa aplikací](./media/easysso-for-bitbucket-tutorial/jira-admin-1.png)

1. Klikněte na **EasySSO**.

    ![Snadné jednotné přihlašování](./media/easysso-for-bitbucket-tutorial/jira-admin-2.png)

1. Vyberte možnost **SAML** . Tím přejdete do konfiguračního oddílu SAML.

    ![SAML](./media/easysso-for-bitbucket-tutorial/jira-admin-3.png)

1. V horní části karty vybrat **certifikáty** a zobrazí se vám následující obrazovka, kde najdete **certifikát (Base64)** nebo **soubor metadat** , který jste uložili v předchozích krocích konfigurace **jednotného přihlašování služby Azure AD** . Máte následující možnosti, jak pokračovat:

    ![Adresa URL metadat](./media/easysso-for-bitbucket-tutorial/jira-admin-4.png)

    a. Použijte soubor federačních **metadat** aplikace, který jste stáhli do místního souboru v počítači. Vyberte možnost **nahrát** přepínač a postupujte podle dialogového okna pro nahrání souboru, které je specifické pro váš operační systém.

    **ANI**

    b. Otevřete soubor federačních **metadat** aplikace a zobrazte obsah (v libovolném textovém editoru) souboru a zkopírujte ho do schránky. Vyberte možnost **vstup** a vložte obsah schránky do textového pole.

    **ANI**

    c. Plně ruční konfigurace. Otevřete certifikát federační aplikace **(Base64)** , abyste zobrazili obsah (v libovolném textovém editoru) souboru a zkopírovali ho do schránky. Vložte ho do textového pole **IDP tokeny podepisování certifikátů** . Pak přejděte na kartu **Obecné** a vyplňte **pole Adresa URL příspěvku** a **ID entity** příslušnými hodnotami pro **přihlašovací adresu URL** a **identifikátor služby Azure AD** , který jste předtím uložili.

1. V dolní části stránky klikněte na tlačítko **Uložit** . Zobrazí se obsah metadat nebo souborů certifikátů, které se analyzují do polí konfigurace. Konfigurace EasySSO pro BitBucket se dokončila.

1. Pro účely nejlepšího testování přejděte na kartu **vzhled &** a zkontrolujte možnost tlačítko pro **přihlášení SAML** na. Tím se povolí samostatné tlačítko na obrazovce pro přihlášení k JIRA, které vám konkrétně umožní otestovat Azure AD SAML Integration end na konec. Toto tlačítko můžete ponechat zapnuté a nakonfigurovat jeho umístění, barvu a překlad pro režim výroby.

    ![Vzhled &](./media/easysso-for-bitbucket-tutorial/jira-admin-5.png)

    > [!NOTE]
    > Pokud máte nějaké problémy, obraťte se prosím na [tým podpory EasySSO](mailto:support@techtime.co.nz).

### <a name="create-easysso-for-bitbucket-test-user"></a>Vytvořit EasySSO pro testovacího uživatele BitBucket

V této části se v EasySSO pro BitBucket vytvoří uživatel s názvem B. Simon. EasySSO for BitBucket podporuje zřizování uživatelů za běhu, které je ve výchozím nastavení **zakázáno** . Pokud chcete povolit zřizování uživatelů, musíte explicitně zaškrtnout možnost **vytvořit uživatele při úspěšném přihlášení** v části Obecné v konfiguraci modulu plug-in EasySSO. Pokud uživatel ještě v EasySSO pro BitBucket neexistuje, vytvoří se po ověření nový.

Pokud však nechcete povolit Automatické zřizování uživatelů při prvním přihlášení uživatele, musí existovat uživatelé v adresářích uživatelů back-endu, který používá instance EasySSO for BitBucket, jako je například LDAP nebo Atlassian.

![Zřizování uživatelů](./media/easysso-for-bitbucket-tutorial/jira-admin-6.png)

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici EasySSO for BitBucket na přístupovém panelu, měli byste se automaticky přihlásit k EasySSO pro BitBucket, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si EasySSO pro BitBucket s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chránit EasySSO pro BitBucket s pokročilými viditelnostmi a ovládacími prvky](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
