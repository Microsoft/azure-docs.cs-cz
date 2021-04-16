---
title: 'Kurz: Azure Active Directory integrace s Gigya | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Gigya.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: 6956b8ba2dcad0e67caac797a47e5308e649d042
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516034"
---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>Kurz: Azure Active Directory integrace s Gigya

V tomto kurzu se dozvíte, jak integrovat Gigya s Azure Active Directory (Azure AD). Když integrujete Gigya s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Gigya.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Gigya svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Gigya odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Gigya podporuje jednotné přihlašování iniciované v **SP** .

## <a name="add-gigya-from-the-gallery"></a>Přidání Gigya z Galerie

Pokud chcete nakonfigurovat integraci Gigya do služby Azure AD, musíte přidat Gigya z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Gigya** .
1. Na panelu výsledků vyberte **Gigya** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-gigya"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Gigya

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Gigya pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Gigya.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí Gigya postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte GIGYA SSO](#configure-gigya-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Gigya Test User](#create-gigya-test-user)** -to, abyste měli protějšek B. Simon v Gigya, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Gigya** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `http://<companyname>.gigya.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://fidm.gigya.com/saml/v2.0/<companyname>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta Gigya](https://developers.gigya.com/display/GD/Opening+A+Support+Incident) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení Gigya** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Vytvoření testovacího uživatele Azure AD 

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Gigya.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Gigya**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name=&quot;configure-gigya-sso&quot;></a>Konfigurace jednotného přihlašování Gigya

1. V jiném okně webového prohlížeče se přihlaste k webu Gigya společnosti jako správce.

2. Přejděte na **nastavení \> přihlášení SAML** a potom klikněte na tlačítko **Přidat** .
   
    ![Přihlášení SAML](./media/gigya-tutorial/login.png &quot;Přihlášení SAML")

3. V části **přihlášení SAML** proveďte následující kroky:
   
    ![Konfigurace SAML](./media/gigya-tutorial/configuration.png "Konfigurace SAML")
   
    a. Do textového pole **název** zadejte název konfigurace.
   
    b. Do textového pole **vystavitele** vložte hodnotu **identifikátoru služby Azure AD** , který jste zkopírovali z Azure Portal. 
   
    c. Do textového pole **Adresa URL služby Single Sign-On Service** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.
   
    d. Do textového pole **formát ID názvu** vložte hodnotu **formátu identifikátoru názvu** , který jste zkopírovali z Azure Portal.
   
    e. Otevřete v programu Poznámkový blok certifikát s kódováním Base-64 stažený z Azure Portal, zkopírujte jeho obsah do schránky a vložte ho do textového pole **certifikát X. 509** .
   
    f. Klikněte na **Uložit nastavení**.

## <a name="create-gigya-test-user"></a>Vytvořit testovacího uživatele Gigya

Aby se uživatelé Azure AD mohli přihlašovat k Gigya, musí se zřídit v Gigya. V případě Gigya je zřizování ručním úkolem.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Při zřizování uživatelských účtů proveďte následující kroky:

1. Přihlaste se k webu **Gigya** společnosti jako správce.

2. Přejděte na **správce \> Správa uživatelů** a pak klikněte na **pozvat uživatele**.
   
    ![Správa uživatelů](./media/gigya-tutorial/users.png "Správa uživatelů")

3. V dialogovém okně pozvat uživatele proveďte následující kroky:
   
    ![Pozvat uživatele](./media/gigya-tutorial/invite-user.png "Pozvat uživatele")
   
    a. Do textového pole **e-mail** zadejte e-mailový alias platného Azure Active Directory účtu, který chcete zřídit.
    
    b. Klikněte na **pozvat uživatele**.
      
    > [!NOTE]
    > Držitel účtu Azure Active Directory dostane e-mail s odkazem na potvrzení účtu před jeho aktivním použitím.
    > 

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k Gigya, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení k Gigya přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Gigya v části Moje aplikace, přesměruje se na přihlašovací adresu Gigya. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Gigya můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
