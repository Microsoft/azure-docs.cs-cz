---
title: 'Kurz: Azure Active Directory integrace s TalentLMS | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TalentLMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 1b65191e97402f2fb352c56664dad085c448242c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650617"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Kurz: Azure Active Directory integrace s TalentLMS

V tomto kurzu se dozvíte, jak integrovat TalentLMS s Azure Active Directory (Azure AD). Když integrujete TalentLMS s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k TalentLMS.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k TalentLMS svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s TalentLMS potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* TalentLMS odběr povoleného jednotného přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* TalentLMS podporuje jednotné přihlašování iniciované v **SP**

## <a name="add-talentlms-from-the-gallery"></a>Přidání TalentLMS z Galerie

Pokud chcete nakonfigurovat integraci TalentLMS do služby Azure AD, musíte přidat TalentLMS z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **TalentLMS** .
1. Na panelu výsledků vyberte **TalentLMS** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-talentlms"></a>Konfigurace a testování jednotného přihlašování Azure AD pro TalentLMS

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí TalentLMS pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v TalentLMS.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí TalentLMS postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte TALENTLMS SSO](#configure-talentlms-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte TalentLMS Test User](#create-talentlms-test-user)** -to, abyste měli protějšek B. Simon v TalentLMS, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **TalentLMS** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně TalentLMS a adresách URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<tenant-name>.TalentLMSapp.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta TalentLMS](https://www.talentlms.com/contact) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. V části **podpisový certifikát SAML** kliknutím na tlačítko **Upravit** otevřete dialogové okno **podpisový certifikát SAML** .

    ![Upravit podpisový certifikát SAML](common/edit-certificate.png)

6. V části **podpisový certifikát SAML** zkopírujte **kryptografický otisk** a uložte ho do svého počítače.

    ![Kopírovat hodnotu kryptografického otisku](common/copy-thumbprint.png)

7. V části **Nastavení TalentLMS** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k TalentLMS.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **TalentLMS**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="configure-talentlms-sso"></a>Konfigurace jednotného přihlašování TalentLMS

1. V jiném okně webového prohlížeče se přihlaste k webu TalentLMS společnosti jako správce.

1. V části **nastavení & účtu** klikněte na kartu **Uživatelé** .

    ![Nastavení & účtu](./media/talentlms-tutorial/IC777296.png "Nastavení & účtu")

1. Klikněte na **jeden Sign-On (SSO)**,

1. V části s jedním Sign-On proveďte následující kroky:

    ![Jednotné přihlašování](./media/talentlms-tutorial/saml.png "Jednotné přihlašování")

    a. V seznamu **typ integrace jednotného přihlašování** vyberte **SAML 2,0**.

    b. Do textového pole **poskytovatel identity (IDP)** vložte hodnotu **identifikátoru Azure AD**, kterou jste zkopírovali z Azure Portal.

    c. Vložte hodnotu **kryptografického otisku** z Azure Portal do textového pole **otisku certifikátu** .

    d.  Do textového pole **Adresa URL vzdáleného přihlášení** vložte hodnotu **přihlašovací adresa URL**, kterou jste zkopírovali z Azure Portal.

    e. Do textového pole **Adresa URL vzdáleného přihlášení** vložte hodnotu **URL pro odhlášení**, kterou jste zkopírovali z Azure Portal.

    f. Zadejte tyto informace:

    * Do textového pole **TargetedID** zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    * Do textového pole **název** zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    * Do textového pole **příjmení** zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    * Do textového pole **e-mail** zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

1. Klikněte na **Uložit**.

### <a name="create-talentlms-test-user"></a>Vytvořit testovacího uživatele TalentLMS

Aby se uživatelé Azure AD mohli přihlašovat k TalentLMS, musí se zřídit v TalentLMS. V případě TalentLMS je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se ke svému tenantovi **TalentLMS** .

1. Klikněte na **Uživatelé** a potom klikněte na **Přidat uživatele**.

1. Na stránce **Přidat uživatelský** dialog proveďte následující kroky:

    ![Přidat uživatele](./media/talentlms-tutorial/IC777299.png "Přidání uživatele")  

    a. Do textového pole **jméno a příjmení** zadejte jméno uživatele jako například **Britta**.

    b. Do textového pole **příjmení** zadejte jméno uživatele jako **Simon**.
 
    c. Do textového pole **e-mailová adresa** zadejte e-maily uživatele, jako je `brittasimon\@contoso.com` .

    d. Klikněte na **Add User** (Přidat uživatele).

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jiné nástroje pro vytváření uživatelských účtů TalentLMS nebo rozhraní API poskytovaná TalentLMS.

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k TalentLMS, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení k TalentLMS přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici TalentLMS v části Moje aplikace, přesměruje se na přihlašovací adresu TalentLMS. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování TalentLMS můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).