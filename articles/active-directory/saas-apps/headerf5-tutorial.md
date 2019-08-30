---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s F5 | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59a87abb-1ec1-4438-be07-5b115676115f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea4167f5a8f4e29641a999c72f57b368190a34e0
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70166090"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s F5

V tomto kurzu se naučíte integrovat F5 s Azure Active Directory (Azure AD). Při integraci F5 se službou Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k F5.
* Umožněte uživatelům, aby se automaticky přihlásili k F5 pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Jednotné přihlašování (SSO) F5 se dá nakonfigurovat třemi různými způsoby.

- [Konfigurace jednotného přihlašování F5 pro aplikaci založenou na hlavičkách](#configure-f5-single-sign-on-for-header-based-application)

- [Konfigurace jednotného přihlašování F5 pro aplikaci Kerberos](kerbf5-tutorial.md)

- [Konfigurace jednotného přihlašování F5 pro pokročilou aplikaci Kerberos](advance-kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>Přidávání F5 z Galerie

Pokud chcete nakonfigurovat integraci F5 na Azure AD, musíte přidat F5 z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole klávesu **F5** .
1. Vyberte **F5** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Konfigurace a testování jednotného přihlašování Azure AD pro F5

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí F5 s použitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v F5.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí F5, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte F5 SSO](#configure-f5-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořit testovacího uživatele F5](#create-f5-test-user)** , aby měl protějšek B. Simon v F5, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikací **F5** najděte část **Správa** a vyberte jednotné přihlašování.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://<YourCustomFQDN>.f5.com/`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:`https://<YourCustomFQDN>.f5.com/`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory F5 pro klienty](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

1. V části **nastavit na F5** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k F5.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **F5**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-f5-sso"></a>Konfigurace F5 SSO

- [Konfigurace jednotného přihlašování F5 pro aplikaci Kerberos](kerbf5-tutorial.md)

- [Konfigurace jednotného přihlašování F5 pro pokročilou aplikaci Kerberos](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>Konfigurace jednotného přihlašování F5 pro aplikaci založenou na hlavičkách

1. Otevřete nové okno webového prohlížeče a přihlaste se k webu společnosti F5 (na základě záhlaví) jako správce a proveďte následující kroky:

1. Certifikát metadat musíte importovat do F5 (na základě záhlaví), které se použije později v procesu instalace. Do **> seznamu certifikátů protokolu SSL vyberte System > Certificate management > Traffic Certificate management >** . Klikněte na **Import** pravého rohu.

    ![Konfigurace F5 (na základě hlaviček)](./media/headerf5-tutorial/configure12.png)
 
1. Navíc také potřebujete **certifikát SSL** pro název hostitele (`headerapp.superdemo.live`), v tomto příkladu jsme použili certifikát se zástupnými znaky.

    ![Konfigurace F5 (na základě hlaviček)](./media/headerf5-tutorial/configure13.png)

1. Přejít na – **F5 (na základě záhlaví) Big-IP klikněte na přístup > s asistencí konfigurace > federaci > poskytovateli služeb SAML**.

    ![Konfigurace F5 (na základě hlaviček)](./media/headerf5-tutorial/configure01.png)

    ![Konfigurace F5 (na základě hlaviček)](./media/headerf5-tutorial/configure02.png)
 
1. Zadejte **ID entity** (stejné jako to, co jste nakonfigurovali v konfiguraci aplikace Azure AD).

    ![Konfigurace F5 (na základě hlaviček)](./media/headerf5-tutorial/configure03.png) 

1. Vytvořit nový virtuální server, zadat **cílovou adresu**, **port přesměrování** je nepovinný. Vyberte **certifikát Nezástupných karet** (nebo **certifikát** , který jste nahráli pro aplikaci), který jsme nahráli dříve a **přidružený privátní klíč**.

    ![Konfigurace F5 (na základě hlaviček)](./media/headerf5-tutorial/configure04.png) 

1. Nahrajte konfigurační **metadata** a zadejte nový **název pro konektor SAML IDP** a budete taky muset zadat federační certifikát, který jste nahráli dříve.

    ![Konfigurace F5 (na základě hlaviček)](./media/headerf5-tutorial/configure05.png)
 
1. **Vytvořit nový** Fond aplikací back-end, zadejte **IP adresy** back-end aplikačních serverů.

    ![Konfigurace F5 (na základě hlaviček)](./media/headerf5-tutorial/configure06.png)

1. V části jednotné přihlašování vyberte **záhlaví HTTP**. Na základě vaší aplikace můžete přidat další záhlaví. Seznam proměnných relace SAML najdete v příloze.

    ![Konfigurace F5 (na základě hlaviček)](./media/headerf5-tutorial/configure07.png) 

1. Kontaktujte [F5 (na základě záhlaví) tým podpory](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) pro **kontrolu nastavení koncových bodů** v dokumentaci k podrobnostem.

    ![Konfigurace F5 (na základě hlaviček)](./media/headerf5-tutorial/configure08.png)

1. Kontaktujte [F5 (na základě záhlaví) tým technické podpory](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) pro **Vlastnosti správy relací** podrobnosti dokumentace.

    ![Konfigurace F5 (na základě hlaviček)](./media/headerf5-tutorial/configure09.png)

1. **Zkontrolujte souhrn** a klikněte na **nasadit**.

    ![Konfigurace F5 (na základě hlaviček)](./media/headerf5-tutorial/configure10.png)

    ![Konfigurace F5 (na základě hlaviček)](./media/headerf5-tutorial/configure11.png)

### <a name="create-f5-test-user"></a>Vytvořit testovacího uživatele F5

V této části vytvoříte na F5 uživatele s názvem B. Simon. Pokud chcete přidat uživatele na platformě F5, pracujte s nástrojem [F5 Client Support Team](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) . Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování. 

## <a name="test-sso"></a>Test SSO 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici F5 na přístupovém panelu byste měli být automaticky přihlášeni k F5, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Zkuste F5 pomocí Azure AD](https://aad.portal.azure.com/)

- [Konfigurace jednotného přihlašování F5 pro aplikaci Kerberos](kerbf5-tutorial.md)

- [Konfigurace jednotného přihlašování F5 pro pokročilou aplikaci Kerberos](advance-kerbf5-tutorial.md)

