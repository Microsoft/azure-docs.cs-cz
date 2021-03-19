---
title: 'Kurz: Azure Active Directory integrace s Wdesk | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Wdesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: jeedes
ms.openlocfilehash: d85d7ef37536b54ecfc1b65d19eafd1d499ca050
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104603230"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Wdesk

V tomto kurzu se dozvíte, jak integrovat Wdesk s Azure Active Directory (Azure AD). Když integrujete Wdesk s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Wdesk.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Wdesk svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Wdesk odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Wdesk podporuje **aktualizace SP** a **IDP** , které iniciovaly jednotné přihlašování.

## <a name="add-wdesk-from-the-gallery"></a>Přidání Wdesk z Galerie

Pokud chcete nakonfigurovat integraci Wdesk do služby Azure AD, musíte přidat Wdesk z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Wdesk** .
1. Na panelu výsledků vyberte **Wdesk** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-wdesk"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Wdesk

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Wdesk na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Wdesk.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí Wdesk postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte WDESK SSO](#configure-wdesk-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Wdesk Test User](#create-wdesk-test-user)** -to, abyste měli protějšek B. Simon v Wdesk, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Wdesk** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a adresou URL Sign-On. Tyto hodnoty získáte z portálu WDesk při konfiguraci jednotného přihlašování.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení Wdesk** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Wdesk.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Wdesk**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-wdesk-sso"></a>Konfigurace jednotného přihlašování Wdesk

1. V jiném okně webového prohlížeče se přihlaste k Wdesk jako správce zabezpečení.

1. V levém dolním rohu klikněte na **správce** a vyberte **správce účtu**:
 
    ![Snímek obrazovky se zobrazí správce účtu vybraný v nabídce správce.](./media/wdesk-tutorial/account.png)

1. V Wdesk správce přejděte na **zabezpečení** **a pak na**  >  **Nastavení SAML SAML**:

    ![Snímek obrazovky zobrazuje nastavení SAML vybraná na kartě SAML.](./media/wdesk-tutorial/settings.png)

1. V části **nastavení ID uživatele SAML** ověřte, že **ID uživatele SAML je Wdesk uživatelské jméno**.

    ![Snímek obrazovky ukazuje nastavení uživatele SAML I D, kde můžete vybrat uživatele SAML I D.](./media/wdesk-tutorial/wdesk-username.png)

4. V části **Obecné nastavení** zaškrtněte **možnost Povolit jednotné přihlašování SAML**:

    ![Snímek obrazovky ukazuje upravit nastavení SAML, kde můžete vybrat Povolit jednotné přihlašování SAML.](./media/wdesk-tutorial/user-settings.png)

5. V části **Podrobnosti o poskytovateli služeb** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí podrobnosti o poskytovateli služeb, kde můžete zadat hodnoty, které jsou popsány.](./media/wdesk-tutorial/service-provider.png)

    1. Zkopírujte **adresu URL pro přihlášení** a vložte ji do textového pole **Adresa URL pro přihlášení** na Azure Portal.

    1. Zkopírujte **adresu URL metadat** a vložte ji do textového pole **identifikátoru** v Azure Portal.

    1. Zkopírujte **adresu URL příjemce** a vložte ji do textového pole **adresa url odpovědi** na Azure Portal.

    1. Uložte změny kliknutím na **Uložit** na Azure Portal.      

1. Kliknutím na **Konfigurovat nastavení IDP** otevřete dialogové okno **Upravit nastavení IDP** . Kliknutím na **zvolit soubor** vyhledejte **Metadata.xml** soubor, který jste uložili z Azure Portal a pak ho nahrajte.
    
    ![Snímek obrazovky s nastavením upravit I d P, kde můžete nahrávat metadata.](./media/wdesk-tutorial/metadata.png)
  
1. Klikněte na **Uložit změny**.

    ![Snímek obrazovky se zobrazí tlačítko Uložit změny.](./media/wdesk-tutorial/save.png)

### <a name="create-wdesk-test-user"></a>Vytvořit testovacího uživatele Wdesk

Aby se uživatelé Azure AD mohli přihlašovat k Wdesk, musí se zřídit v Wdesk. V Wdesk je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k Wdesk jako správce zabezpečení.

2. Přejděte na   >  **správce účtu** správce.

     ![Snímek obrazovky se zobrazí správce účtu vybraný v nabídce správce.](./media/wdesk-tutorial/account.png)

3. Klikněte na **Členové** v části **lidé**.

4. Nyní klikněte na tlačítko **Přidat člena** a otevřete dialogové okno **Přidat člena** . 
   
    ![Snímek obrazovky se zobrazí na kartě členové, kde můžete vybrat přidat člena.](./media/wdesk-tutorial/create-user-1.png)  

5. Do textového pole **uživatel** zadejte uživatelské jméno, jako třeba b.simon@contoso.com , a klikněte na tlačítko **pokračovat** .

    ![Snímek obrazovky se zobrazí dialogové okno Přidat člena, kde můžete zadat uživatele.](./media/wdesk-tutorial/create-user-3.png)

6.  Zadejte podrobnosti, jak je znázorněno níže:
  
    ![Snímek obrazovky se zobrazí dialogové okno Přidat člena, kde můžete přidat základní informace pro uživatele.](./media/wdesk-tutorial/create-user-4.png)
 
    a. Do textového pole **e-mail** zadejte e-maily uživatele, jako je například b.simon@contoso.com .

    b. Do textového pole **jméno a příjmení** zadejte jméno uživatele jako **B**.

    c. Do textového pole **příjmení** zadejte příjmení uživatele, jako je **Simon**.

7. Klikněte na tlačítko **Uložit člen** .  

    ![Snímek obrazovky s tlačítkem pro uložení členů zobrazí úvodní e-mail s informacemi o odeslání.](./media/wdesk-tutorial/create-user-5.png)

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k Wdesk, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlášení k Wdesk přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k Wdesk, pro které jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Wdesk v nabídce Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k Wdesk, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Wdesk můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
