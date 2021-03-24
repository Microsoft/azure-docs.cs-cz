---
title: 'Kurz: Azure Active Directory Integration s oddělením služeb SolarWinds (dříve Samanage) | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a oddělením služeb SolarWinds (dříve Samanage).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 6dcd5612bd2c5957ae0a397c3463dbb42445a754
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "104956039"
---
# <a name="tutorial-azure-active-directory-integration-with-solarwinds-service-desk-previously-samanage"></a>Kurz: Azure Active Directory integrace se službou SolarWinds Service Desk (dříve Samanage)

V tomto kurzu se dozvíte, jak integrovat SolarWinds s Azure Active Directory (Azure AD). Když integrujete SolarWinds s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k SolarWinds.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k SolarWinds svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* SolarWinds odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SolarWinds podporuje jednotné přihlašování iniciované v **SP** .

## <a name="add-solarwinds-from-the-gallery"></a>Přidání SolarWinds z Galerie

Pokud chcete nakonfigurovat integraci SolarWinds do služby Azure AD, musíte přidat SolarWinds z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **SolarWinds** .
1. Na panelu výsledků vyberte **SolarWinds** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-solarwinds"></a>Konfigurace a testování jednotného přihlašování Azure AD pro SolarWinds

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí SolarWinds pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v SolarWinds.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí SolarWinds postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte SOLARWINDS SSO](#configure-solarwinds-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte SolarWinds Test User](#create-solarwinds-test-user)** -to, abyste měli protějšek B. Simon v SolarWinds, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **SolarWinds** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL a identifikátoru, které jsou vysvětleny dále v tomto kurzu. Další podrobnosti získáte od [týmu podpory Samanage klientů](https://www.samanage.com/support). Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení SolarWinds** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k SolarWinds.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **SolarWinds**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

<a name="configure-solarwinds-single-sign-on"></a>

## <a name="configure-solarwinds-sso"></a>Konfigurace jednotného přihlašování SolarWinds

1. V jiném okně webového prohlížeče se přihlaste k webu SolarWinds společnosti jako správce.

2. Klikněte na **řídicí panel** a v levém navigačním podokně vyberte **Nastavení** .
   
    ![Řídicí panel](./media/samanage-tutorial/tutorial-samanage-1.png "Řídicí panel")

3. Klikněte na **jednotné přihlašování**.
   
    ![Jednotné přihlašování](./media/samanage-tutorial/tutorial-samanage-2.png "Jednotné přihlašování")

4. Přejděte k části **přihlášení pomocí SAML** , proveďte následující kroky:
   
    ![Přihlášení pomocí SAML](./media/samanage-tutorial/tutorial-samanage-3.png "Přihlášení pomocí SAML")
 
    a. Klikněte na **Povolit jednu Sign-On s SAML**.  
 
    b. Do textového pole **Adresa URL poskytovatele identity** vložte hodnotu **identifikátoru služby Azure AD** , který jste zkopírovali z Azure Portal.    
 
    c. Potvrďte **přihlašovací adresu URL** , která se shoduje s **adresou URL** **základního konfiguračního oddílu SAML** v Azure Portal.
 
    d. Do textového pole **Adresa URL pro odhlášení** zadejte hodnotu **adresy URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.
 
    e. Do textového pole **vystavitele SAML** zadejte identifikátor URI ID aplikace nastavený ve Vašem zprostředkovateli identity.
 
    f. Otevřete v programu Poznámkový blok certifikát s kódováním Base-64, který jste stáhli z Azure Portal, zkopírujte jeho obsah do schránky a vložte ho do pole **Vložit poskytovatele identity x. 509 pod** textovým polem.
 
    například **Pokud v SolarWinds neexistují**, klikněte na vytvořit uživatele.
 
    h. Klikněte na **Aktualizovat**.

### <a name="create-solarwinds-test-user"></a>Vytvořit testovacího uživatele SolarWinds

Aby se uživatelé Azure AD mohli přihlásit k SolarWinds, musí se zřídit v SolarWinds.  
V případě SolarWinds je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu SolarWinds společnosti jako správce.

2. Klikněte na **řídicí panel** a vyberte **Nastavení** v levém navigačním panelu.
   
    ![Nastavení](./media/samanage-tutorial/tutorial-samanage-1.png "Nastavení")

3. Klikněte na kartu **Uživatelé** .
   
    ![Uživatelé](./media/samanage-tutorial/tutorial-samanage-6.png "Uživatelé")

4. Klikněte na **Nový uživatel**.
   
    ![Nový uživatel](./media/samanage-tutorial/tutorial-samanage-7.png "Nový uživatel")

5. Zadejte **jméno** a **e-mailovou adresu** účtu Azure Active Directory, který chcete zřídit, a klikněte na **vytvořit uživatele**.
   
    ![Vytvoření uživatele](./media/samanage-tutorial/tutorial-samanage-8.png "Vytvořit uživatele")
   
   >[!NOTE]
   >Držitel účtu Azure Active Directory obdrží e-mail a provede odkaz pro potvrzení, že účet ještě nebude aktivní. K zřizování Azure Active Directorych uživatelských účtů můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů SolarWinds nebo rozhraní API poskytovaná SolarWinds.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k SolarWinds, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení k SolarWinds přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici SolarWinds v části Moje aplikace, přesměruje se na přihlašovací adresu SolarWinds. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování SolarWinds můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).