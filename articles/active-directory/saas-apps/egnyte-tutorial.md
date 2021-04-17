---
title: 'Kurz: Azure Active Directory integrace s Egnyte | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Egnyte.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: jeedes
ms.openlocfilehash: 2662b686102a1a4f6aa6db0f7a4052de329def60
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519797"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Egnyte

V tomto kurzu se dozvíte, jak integrovat Egnyte s Azure Active Directory (Azure AD). Když integrujete Egnyte s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Egnyte.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Egnyte svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Egnyte odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Egnyte podporuje jednotné přihlašování iniciované v **SP** .

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="add-egnyte-from-the-gallery"></a>Přidání Egnyte z Galerie

Pokud chcete nakonfigurovat integraci Egnyte do služby Azure AD, musíte přidat Egnyte z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Egnyte** .
1. Na panelu výsledků vyberte **Egnyte** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-egnyte"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Egnyte

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Form.com pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Form.com.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí Form.com postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte EGNYTE SSO](#configure-egnyte-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Egnyte Test User](#create-egnyte-test-user)** -to, abyste měli protějšek B. Simon v Egnyte, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Egnyte** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.egnyte.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.egnyte.com/samlconsumer/AzureAD`
    
    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte hodnotu skutečnou adresou URL Sign-On a adresou URL odpovědi. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta Egnyte](https://www.egnyte.com/corp/contact_egnyte.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

5. V části **Nastavení Egnyte** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Egnyte.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Egnyte**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name=&quot;configure-egnyte-sso&quot;></a>Konfigurace jednotného přihlašování Egnyte

1. V jiném okně webového prohlížeče se přihlaste k webu Egnyte společnosti jako správce.

2. Klikněte na **Nastavení**.
   
    ![Nastavení 1](./media/egnyte-tutorial/settings-tab.png &quot;Nastavení")

3. V nabídce klikněte na **Nastavení**.

    ![Nabídka 1](./media/egnyte-tutorial/menu-tab.png "Nabídka")

4. Klikněte na kartu **Konfigurace** a pak klikněte na **zabezpečení**.

    ![Zabezpečení](./media/egnyte-tutorial/configuration.png "Zabezpečení")

5. V části **ověřování jedním Sign-On** proveďte následující kroky:

    ![Ověřování jednotného přihlašování](./media/egnyte-tutorial/authentication.png "Ověřování jednotného přihlašování")   
    
    a. Jako **ověřování pomocí jednotného přihlašování** vyberte **SAML 2,0**.
   
    b. Jako **zprostředkovatel identity** vyberte **AzureAD**.
   
    c. Vložte **přihlašovací adresu URL** zkopírovanou z Azure Portal do textového pole **Adresa URL pro přihlášení zprostředkovatele identity** .
   
    d. Do textového pole **ID entity zprostředkovatele identity** vložte **identifikátor Azure AD** , který jste zkopírovali z Azure Portal.
      
    e. Otevřete v programu Poznámkový blok certifikát s kódováním Base-64 stažený z Azure Portal, zkopírujte jeho obsah do schránky a vložte ho do textového pole **certifikát poskytovatele identity** .
   
    f. Jako **výchozí mapování uživatelů** vyberte **e-mailová adresa**.
   
    například Jako **hodnotu vystavitele v doméně použijte** možnost **zakázáno**.
   
    h. Klikněte na **Uložit**.

### <a name="create-egnyte-test-user"></a>Vytvořit testovacího uživatele Egnyte

Aby se uživatelé Azure AD mohli přihlašovat k Egnyte, musí se zřídit v Egnyte. V případě Egnyte je zřizování ručním úkolem.

**Při zřizování uživatelských účtů proveďte následující kroky:**

1. Přihlaste se k webu **Egnyte** společnosti jako správce.

2. Přejít na **nastavení \> Uživatelé & skupiny**.

3. Klikněte na tlačítko **Přidat nového uživatele** a potom vyberte typ uživatele, který chcete přidat.
   
    ![Uživatelé](./media/egnyte-tutorial/add-user.png "Uživatelé")

4. V části **Nová skupina Power** Users proveďte následující kroky:
    
    ![Nový standardní uživatel](./media/egnyte-tutorial/new-user.png "Nový standardní uživatel")   

    a. Do textového pole **e-mail** zadejte e-maily uživatele, jako je **Brittasimon \@ contoso.com**.

    b. Do textového pole **uživatelské jméno** zadejte uživatelské jméno uživatele, jako je **Brittasimon**.

    c. Jako **typ ověřování** vyberte **jednotné přihlašování** .
   
    d. Klikněte na **Uložit**.
    
    >[!NOTE]
    >Držitel účtu Azure Active Directory dostane e-mail s oznámením.
    >

>[!NOTE]
>K zřizování uživatelských účtů Azure AD můžete použít jiné nástroje pro vytváření uživatelských účtů Egnyte nebo rozhraní API poskytovaná Egnyte.
>

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k Egnyte, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení k Egnyte přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Egnyte v části Moje aplikace, přesměruje se na přihlašovací adresu Egnyte. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Egnyte můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
