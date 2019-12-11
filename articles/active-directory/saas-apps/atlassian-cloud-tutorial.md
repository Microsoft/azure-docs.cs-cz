---
title: 'Kurz: Azure Active Directory integrace s cloudem Atlassian | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Atlassian cloudem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: celested
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3a54b096019e9e38bc800ae313016a430062dab
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964306"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Kurz: integrace Atlassian cloudu s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat Atlassian Cloud s Azure Active Directory (Azure AD). Když integrujete Cloud Atlassian s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup ke cloudu Atlassian.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Atlassian cloudu pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat zdarma [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Předplatné Atlassian Cloud s povoleným jednotným přihlašováním (SSO).
* Pokud chcete povolit jednotné přihlašování Security Assertion Markup Language (SAML) pro cloudové produkty Atlassian, musíte nastavit přístup Atlassian. Přečtěte si další informace o [Atlassian přístupu]( https://www.atlassian.com/enterprise/cloud/identity-manager).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. 

* Atlassian Cloud podporuje jednotné přihlašování (SSO) **a IDP** .
* Atlassian Cloud podporuje [Automatické zřizování a rušení zřizování uživatelů](atlassian-cloud-provisioning-tutorial.md)

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Přidání cloudu Atlassian z Galerie

Pokud chcete nakonfigurovat integraci Atlassian cloudu do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Atlassian Cloud z galerie.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Atlassian Cloud** .
1. Z panelu výsledků vyberte **Cloud Atlassian** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Atlassian cloudu pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Atlassian cloudu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí cloudu Atlassian, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování Atlassian do cloudu](#configure-atlassian-cloud-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte uživatele cloudového testu Atlassian](#create-atlassian-cloud-test-user)** , abyste měli protějšek B. Simon v Atlassian cloudu, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **cloudových aplikací Atlassian** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://auth.atlassian.com/saml/<unique ID>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. Klikněte na **nastavit další adresy URL**.

    d. Do textového pole **stav přenosu** zadejte adresu URL pomocí následujícího vzoru: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Předchozí hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Tyto reálné hodnoty se zobrazí na obrazovce **Konfigurace SAML Atlassian cloudu** , která je vysvětlena dále v kurzu **Konfigurace Atlassian cloudu s jednotným přihlašováním** .

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Hodnota adresy URL pro přihlášení není v reálném čase. Vložte hodnotu z instance, kterou používáte k přihlášení na portál pro správu cloudu Atlassian.

    ![Konfigurace jednotného přihlašování](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-10.png)

1. Cloudová aplikace Atlassian očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, kde **NameIdentifier** je mapován pomocí **User. userPrincipalName**. Cloudová aplikace Atlassian očekává, že **NameIdentifier** se namapují pomocí **User. mail**, takže musíte upravit mapování atributů kliknutím na ikonu **Upravit** a změnit mapování atributů.

    ![image](common/edit-attribute.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení cloudu Atlassian** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup ke cloudu Atlassian.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Cloud Atlassian**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-atlassian-cloud-sso"></a>Konfigurace jednotného přihlašování Atlassian Cloud

1. Pokud chcete automatizovat konfiguraci v rámci Atlassian cloudu, je potřeba nainstalovat **rozšíření prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

1. Po přidání rozšíření do prohlížeče klikněte na **nastavit Cloud Atlassian** , který vás přesměruje na cloudovou aplikaci Atlassian. Odtud zadejte přihlašovací údaje správce pro přihlášení ke cloudu Atlassian. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-7.

    ![Konfigurace instalace](common/setup-sso.png)

1. Pokud chcete nastavit Cloud Atlassian ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu Atlassian cloudové společnosti jako správce a proveďte následující kroky:

1. Než budete moct nakonfigurovat jednotné přihlašování, musíte ověřit svoji doménu. Další informace najdete v dokumentu [ověření domény Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) .

1. V levém podokně vyberte **zabezpečení** > **jednotné přihlašování SAML**. Pokud jste to ještě neudělali, přihlaste se k odběru Atlassian identity Manageru.

    ![Konfigurace jednotného přihlašování](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-11.png)

1. V okně **Přidat konfiguraci SAML** udělejte toto:

    ![Konfigurace jednotného přihlašování](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-12.png)

    a. Do pole **ID entity zprostředkovatele identity** vložte **identifikátor Azure AD** , který jste zkopírovali z Azure Portal.

    b. V poli **Adresa URL jednotného přihlašování zprostředkovatele identity** vložte **přihlašovací adresu URL** , kterou jste zkopírovali z Azure Portal.

    c. Otevřete stažený certifikát z Azure Portal v souboru. txt, zkopírujte hodnotu (bez *počátečního certifikátu* a řádků *koncového certifikátu* ) a pak ho vložte do pole **veřejný certifikát x509** .

    d. Klikněte na **Uložit konfiguraci**.

1. Pokud chcete mít jistotu, že jste nastavili správné adresy URL, aktualizujte nastavení Azure AD pomocí následujícího postupu:

    ![Konfigurace jednotného přihlašování](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-13.png)

    a. V okně SAML zkopírujte **ID identity SP** a pak v Azure Portal v části **Konfigurace SAML**v Atlassian Cloud Basic ji vložte do pole **identifikátor** .

    b. V okně SAML zkopírujte **adresu URL služby příjemce kontrolního výrazu SP** a potom v Azure Portal v části **Konfigurace SAML**v Atlassian Cloud Basic ji vložte do pole **Adresa URL odpovědi** . Přihlašovací adresa URL je klientský URL vašeho cloudu Atlassian.

    > [!NOTE]
    > Pokud jste stávající zákazník, po aktualizaci hodnot ID adresy URL služby pro identifikaci **identity SP** a **SP kontrolního výrazu SP** v Azure Portal vyberte **Ano, aktualizovat konfiguraci**. Pokud jste nový zákazník, můžete tento krok přeskočit.

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

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si Atlassian Cloud s Azure AD](https://aad.portal.azure.com/)
