---
title: 'Kurz: Azure Active Directory integrace s cloudem Atlassian | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Atlassian cloudem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/04/2020
ms.author: jeedes
ms.openlocfilehash: 8548e7449056ecb1f211d5dd026544bf7e1375cc
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708185"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Kurz: integrace Atlassian cloudu s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat Atlassian Cloud s Azure Active Directory (Azure AD). Když integrujete Cloud Atlassian s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup ke cloudu Atlassian.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Atlassian cloudu pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadované součásti

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat zdarma [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Předplatné Atlassian Cloud s povoleným jednotným přihlašováním (SSO).
* Pokud chcete povolit jednotné přihlašování Security Assertion Markup Language (SAML) pro cloudové produkty Atlassian, musíte nastavit přístup Atlassian. Přečtěte si další informace o [Atlassian přístupu]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. 

* Atlassian Cloud podporuje jednotné přihlašování (SSO) **a IDP** .
* Atlassian Cloud podporuje [Automatické zřizování a rušení zřizování uživatelů](atlassian-cloud-provisioning-tutorial.md)
* Po nakonfigurování Atlassian cloudu můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Přidání cloudu Atlassian z Galerie

Pokud chcete nakonfigurovat integraci Atlassian cloudu do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Atlassian Cloud z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Atlassian Cloud** .
1. Z panelu výsledků vyberte **Cloud Atlassian** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Atlassian cloudu pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Atlassian cloudu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí cloudu Atlassian, dokončete následující stavební bloky:

1. **[Nakonfigurujte Azure AD pomocí jednotného přihlašování Atlassian Cloud](#configure-azure-ad-sso)** – umožníte uživatelům používat jednotné přihlašování SAML založené na Azure AD s cloudem Atlassian.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Vytvořte uživatele cloudového testu Atlassian](#create-atlassian-cloud-test-user)** , abyste měli protějšek B. Simon v Atlassian cloudu, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. Pokud chcete automatizovat konfiguraci v rámci Atlassian cloudu, je potřeba nainstalovat **rozšíření prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

1. Po přidání rozšíření do prohlížeče klikněte na **nastavit Cloud Atlassian** , který vás přesměruje na cloudovou aplikaci Atlassian. Odtud zadejte přihlašovací údaje správce pro přihlášení ke cloudu Atlassian. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás.

    ![Konfigurace instalace](common/setup-sso.png)

1. Pokud chcete nastavit Cloud Atlassian ručně, přihlaste se k webu cloudové společnosti Atlassian jako správce a proveďte následující kroky.

1. Než začnete, přejdete do instance produktu Atlassian a zkopírujete/uložíte adresu URL instance.
   > [!NOTE]
   > Adresa URL by měla odpovídat `https://<instancename>.atlassian.net` vzoru

   ![image](./media/atlassian-cloud-tutorial/get-atlassian-instance-name.png)

1. Otevřete [portál pro správu Atlassian](https://admin.atlassian.com/) a klikněte na název vaší organizace.

   ![image](./media/atlassian-cloud-tutorial/click-on-organization-in-atlassian-access.png)

1. Než budete moct nakonfigurovat jednotné přihlašování, musíte ověřit svoji doménu. Další informace najdete v dokumentu [ověření domény Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) .
1. Na obrazovce portálu pro správu Atlassian vyberte **zabezpečení** z levého zásobníku.

   ![image](./media/atlassian-cloud-tutorial/click-on-security-in-atlassian-access.png)

1. Na obrazovce zabezpečení na portálu pro správu Atlassian vyberte v levém zásobníku **jednotné přihlašování SAML** .

   ![image](./media/atlassian-cloud-tutorial/click-on-saml-sso-in-atlassian-access-security.png)

1. Klikněte na **Přidat konfiguraci SAML** a nechte stránku otevřenou.

   ![image](./media/atlassian-cloud-tutorial/saml-configuration-in-atlassian-access-security-saml-sso.png)

   ![image](./media/atlassian-cloud-tutorial/add-saml-configuration.png)

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **cloudových aplikací Atlassian** najděte část **Správa** a vyberte **nastavit jednotné přihlašování**.

   ![image](./media/atlassian-cloud-tutorial/set-up-sso.png)

1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.

   ![image](./media/atlassian-cloud-tutorial/saml-in-azure.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** přejděte dolů k části **Nastavení cloudu Atlassian** .
   
   a. Kliknout na **konfigurační adresy URL**

   ![image](./media/atlassian-cloud-tutorial/configuration-urls.png)
   
   b. Zkopírujte hodnotu **identifikátoru Azure AD** z Azure Portal a vložte ji do TEXTOVÉHO pole **ID entity zprostředkovatele identity** v Atlassian.
   
   c. Zkopírujte hodnotu **URL pro přihlášení** z Azure Portal a vložte ji do TEXTOVÉHO pole **URL jednotného přihlašování zprostředkovatele identity** v Atlassian

   ![image](./media/atlassian-cloud-tutorial/configuration-urls-azure.png)

   ![image](./media/atlassian-cloud-tutorial/entity-id-and-ss.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

   ![image](./media/atlassian-cloud-tutorial/certificate.png)

   ![image](./media/atlassian-cloud-tutorial/certificate-1.png)

1. **Přidání/uložení** konfigurace SAML v Atlassian

1. Pokud chcete nakonfigurovat aplikaci v režimu **IDPed** , upravte **základní konfigurační oddíl SAML** na stránce **nastavit jednotné přihlašování s SAML** v Azure a otevřete **stránku jednotného přihlašování SAML** na portálu pro správu Atlassian.

   a. Zkopírujte hodnotu **ID entity SP** z Atlassian a vložte ji do pole **identifikátor (ID entity)** v Azure a nastavte ji jako výchozí.
   
   b. Zkopírujte hodnotu **adresy URL služby příjemce kontrolního výrazu SP** z Atlassian a vložte ji do pole **Adresa URL odpovědi (adresa URL služby potvrzení)** v Azure a nastavte ji jako výchozí.
   
   c. Zkopírujte hodnotu **adresy URL instance** , kterou jste zkopírovali v kroku 1, a vložte ji do pole **stav přenosu** v Azure.

   ![image](./media/atlassian-cloud-tutorial/copy-urls.png)

   ![image](./media/atlassian-cloud-tutorial/edit-button.png)

   ![image](./media/atlassian-cloud-tutorial/urls.png)
   
1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** , upravte **základní část konfigurace SAML** v tématu **nastavení jednotného přihlašování pomocí stránky SAML** v Azure. Zkopírujte **adresu URL instance** (z kroku 1) a vložte ji do pole **Adresa URL pro přihlášení** v Azure.

   ![image](./media/atlassian-cloud-tutorial/edit-button.png)

   ![image](./media/atlassian-cloud-tutorial/sign-on-URL.png)
   
1. Cloudová aplikace Atlassian očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Mapování atributů můžete upravit kliknutím na ikonu **Upravit** . 

   ![image](./media/atlassian-cloud-tutorial/default-attributes.png)
   
   1. Mapování atributu pro tenanta Azure AD s licencí Microsoft 365
      
      a. Klikněte na deklaraci identity **jedinečného identifikátoru uživatele (ID názvu)** .

      ![image](./media/atlassian-cloud-tutorial/user-attributes-and-claims.png)
      
      b. Atlassian Cloud očekává, že **NameIdentifier** (**jedinečný uživatelský identifikátor**) se namapuje na e-mail uživatele (**uživatel. e-mail**). Upravte **zdrojový atribut** a změňte jej na **User. mail**. Uložte změny v deklaraci identity.

      ![image](./media/atlassian-cloud-tutorial/unique-user-identifier.png)
      
      c. Konečné mapování atributů by mělo vypadat takto.

      ![image](common/default-attributes.png)
      
   1. Mapování atributu pro tenanta Azure AD bez licence na Microsoft 365 

      a. Klikněte na `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` deklaraci identity.

      ![image](./media/atlassian-cloud-tutorial/email-address.png)
         
      b. Zatímco Azure neplní atribut **User. mail** pro uživatele vytvořené v klientech Azure AD bez Microsoft 365 licencí a ukládá e-mail pro tyto uživatele v atributu **userPrincipalName** . Atlassian Cloud očekává, že **NameIdentifier** (**jedinečný uživatelský identifikátor**) se namapuje na e-mail uživatele (**User. userPrincipalName**).  Upravte **zdrojový atribut**  a změňte jej na **User. userPrincipalName**. Uložte změny v deklaraci identity.

      ![image](./media/atlassian-cloud-tutorial/set-email.png)
         
      c. Konečné mapování atributů by mělo vypadat takto.

      ![image](common/default-attributes.png)
     
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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup ke cloudu Atlassian.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Cloud Atlassian**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](./media/atlassian-cloud-tutorial/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-atlassian-cloud-test-user"></a>Vytvořit uživatele cloudového testu Atlassian

Pokud chcete uživatelům Azure AD povolit, aby se přihlásili ke cloudu Atlassian, zřiďte uživatelské účty ručně v cloudu Atlassian pomocí následujícího postupu:

1. V podokně **Správa** vyberte **Uživatelé**.

    ![Odkaz na uživatele cloudu Atlassian](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. Pokud chcete v cloudu Atlassian vytvořit uživatele, vyberte **pozvat uživatele**.

    ![Vytvoření cloudového uživatele v Atlassian](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. Do pole **e-mailová adresa** zadejte e-mailovou adresu uživatele a pak přiřaďte přístup k aplikaci.

    ![Vytvoření cloudového uživatele v Atlassian](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. Pokud chcete uživateli odeslat pozvánku e-mailem, vyberte **pozvat uživatele**. Uživateli se pošle e-mailová pozvánka a po přijetí pozvánky je uživatel v systému aktivní.

> [!NOTE]
> Můžete také hromadně vytvářet uživatele tak, že v části **Uživatelé** vyberete tlačítko **hromadně vytvořit** .

### <a name="test-sso"></a>Test SSO

Když na přístupovém panelu vyberete dlaždici Atlassian Cloud, měli byste se automaticky přihlásit ke cloudu Atlassian, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si Atlassian Cloud s Azure AD](https://aad.portal.azure.com/)

- [Jak chránit Atlassian Cloud s pokročilými viditelnostmi a ovládacími prvky](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)