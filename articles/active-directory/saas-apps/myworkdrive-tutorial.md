---
title: 'Kurz: Azure Active Directory integrace s MyWorkDrive | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a MyWorkDrive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/27/2019
ms.author: jeedes
ms.openlocfilehash: aa0fe40ec28aea7f82ee0e635b3c42140a472816
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88552437"
---
# <a name="tutorial-integrate-myworkdrive-with-azure-active-directory"></a>Kurz: integrace MyWorkDrive s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat MyWorkDrive s Azure Active Directory (Azure AD). Když integrujete MyWorkDrive s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k MyWorkDrive.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k MyWorkDrive svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat zdarma [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* MyWorkDrive odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. MyWorkDrive podporuje **jednotné** přihlašování (SSO) a **IDP** .

## <a name="adding-myworkdrive-from-the-gallery"></a>Přidání MyWorkDrive z Galerie

Pokud chcete nakonfigurovat integraci MyWorkDrive do služby Azure AD, musíte přidat MyWorkDrive z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **MyWorkDrive** .
1. Na panelu výsledků vyberte **MyWorkDrive** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí MyWorkDrive pomocí testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v MyWorkDrive.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí MyWorkDrive, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
2. **[Nakonfigurujte MYWORKDRIVE SSO](#configure-myworkdrive-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření MyWorkDrive Test User](#create-myworkdrive-test-user)** – pro Britta Simon v MyWorkDrive, který je propojený s reprezentací uživatele Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **MyWorkDrive** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte na stránce **základní konfigurace SAML** hodnoty pro následující pole:

    Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:  `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<SERVER.DOMAIN.COM>/Account/Login-saml`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné adresy URL odpovědi a adresy URL Sign-On. Zadejte název hostitele serveru MyWorkDrive vaší společnosti: např.
    > 
    > Adresa URL odpovědi: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > Přihlašovací adresa URL:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Pokud si nejste jistí, jak nastavit vlastní název hostitele a certifikát TLS/SSL pro tyto hodnoty, kontaktujte [tým podpory MyWorkDrive](mailto:support@myworkdrive.com) .

1. Na stránce **nastavit jednu Sign-On s SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** do schránky.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-myworkdrive-sso"></a>Konfigurace jednotného přihlašování MyWorkDrive

1. Pokud chcete automatizovat konfiguraci v rámci MyWorkDrive, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

1. Po přidání rozšíření do prohlížeče klikněte na **MyWorkDrive nastavení** a nasměrujte vás na aplikaci MyWorkDrive. Odtud zadejte přihlašovací údaje správce, které se přihlásí k MyWorkDrive. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-4.

    ![Konfigurace instalace](common/setup-sso.png)

1. Pokud chcete nastavit MyWorkDrive ručně, v jiném okně webového prohlížeče se přihlaste k MyWorkDrive jako správce zabezpečení.

1. Na serveru MyWorkDrive na panelu pro správu klikněte na **Enterprise** a proveďte následující kroky:

    ![Správce](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. Povolte **jednotné přihlašování SAML/AD FS**.

    b. Vybrat **SAML – Azure AD**

    c. V poli **Adresa URL federačních metadat aplikace Azure** vložte hodnotu **adresy URL federačních metadat aplikace** , kterou jste zkopírovali z Azure Portal.

    d. Klikněte na **Uložit**.

    > [!NOTE]
    > Další informace najdete v [článku o podpoře MyWorkDrive Azure AD](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/).

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k MyWorkDrive.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **MyWorkDrive**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-myworkdrive-test-user"></a>Vytvořit testovacího uživatele MyWorkDrive

V této části vytvoříte uživatele s názvem Britta Simon v MyWorkDrive. Pokud chcete přidat uživatele na platformě MyWorkDrive, pracujte s [týmem podpory MyWorkDrive](mailto:support@myworkdrive.com) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-sso"></a>Test SSO

Když na přístupovém panelu vyberete dlaždici MyWorkDrive, měli byste se automaticky přihlásit k MyWorkDrive, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)