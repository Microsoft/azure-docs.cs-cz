---
title: 'Kurz: Azure Active Directory integrace s Zohoem 1 | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zoho jednu.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: 12ac4d9fbf30873f0392a6d767d7568129bad112
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650644"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Kurz: Azure Active Directory integrace s Zoho 1

V tomto kurzu se dozvíte, jak integrovat Zoho s Azure Active Directory (Azure AD). Když integrujete Zoho s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Zoho.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Zoho účtu s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Pokud chcete nakonfigurovat integraci Azure AD s Zoho, budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Zoho jedno předplatné s povoleným přihlašováním.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Zoho One podporuje **aktualizace SP** a **IDP** , které iniciovaly jednotné přihlašování.

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="add-zoho-one-from-the-gallery"></a>Přidání Zoho z Galerie

Pokud chcete nakonfigurovat integraci Zoho do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Zoho jednu z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Zoho** .
1. Vyberte **Zoho** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-zoho-one"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Zoho One

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Zoho s jedním pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Zoho.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s Zoho jedním, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Zoho jednotné přihlašování](#configure-zoho-one-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Zoho jednoho testovacího uživatele](#create-zoho-one-test-user)** – abyste měli protějšek B. Simon v Zoho, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce věnované integraci aplikace **Zoho** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    a. Do textového pole **identifikátor** zadejte adresu URL: `one.zoho.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > Hodnota **adresy URL předchozí odpovědi** není reálné číslo. `<saml-identifier>`Hodnota z #step4 **nakonfigurovat jednu část jednotného přihlašování Zoho** , která se vysvětluje později v tomto kurzu.

    c. Klikněte na **nastavit další adresy URL**.

    d. Do textového pole **stav přenosu** zadejte adresu URL: `https://one.zoho.com`

5. Pokud chcete nakonfigurovat aplikaci v režimu **SP** iniciované, proveďte následující krok:

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > Hodnota předchozí **přihlašovací adresy URL** není skutečná. Tuto hodnotu aktualizujete skutečnou adresou URL Sign-On v části **Konfigurace jednotného přihlašování Zoho** , která je vysvětlena dále v tomto kurzu. 

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavení Zoho 1** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Zoho.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Zoho jednu**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="configure-zoho-one-sso"></a>Konfigurace Zoho jednoho jednotného přihlašování

1. V jiném okně webového prohlížeče se přihlaste k Zoho jedné firemní síti jako správce.

2. Na kartě **organizace** klikněte v části **ověřování SAML** na **Nastavení** .

    ![Zoho jednu organizační organizaci](./media/zoho-one-tutorial/set-up.png)

3. Na místní stránce proveďte následující kroky:

    ![Zoho jeden podpis](./media/zoho-one-tutorial/save.png)

    a. Do textového pole **Adresa URL pro přihlášení** vložte hodnotu **přihlašovací adresa URL**, kterou jste zkopírovali z Azure Portal.

    b. Do textového pole **Adresa URL** pro odhlášení vložte hodnotu URL pro **odhlášení**, kterou jste zkopírovali z Azure Portal.

    c. Klikněte na tlačítko **Procházet** a nahrajte **certifikát (Base64)** , který jste stáhli z Azure Portal.

    d. Klikněte na **Uložit**.

4. Po uložení nastavení ověřování SAML Zkopírujte hodnotu **identifikátoru SAML** a místo toho ji přidejte s **adresou URL odpovědi** `<saml-identifier>` , třeba `https://accounts.zoho.com/samlresponse/one.zoho.com` a vložte vygenerovanou hodnotu do pole **Adresa URL odpovědi** v části **základní konfigurační oddíl SAML** .

    ![Zoho jednu SAML](./media/zoho-one-tutorial/saml-identifier.png)

5. Přejděte na kartu **domény** a potom klikněte na **Přidat doménu**.

    ![Zoho jednu doménu](./media/zoho-one-tutorial/add-domain.png)

6. Na stránce **Přidat doménu** proveďte následující kroky:

    ![Zoho jednu doménu přidat](./media/zoho-one-tutorial/add-domain-name.png)

    a. Do textového pole **název domény** zadejte Domain, jako je contoso.com.

    b. Klikněte na **Přidat**.

    >[!Note]
    >Po přidání domény postupujte podle [těchto](https://www.zoho.com/one/help/admin-guide/domain-verification.html) kroků a ověřte svoji doménu. Po ověření domény použijte název domény v **přihlašovací adrese URL** v **základní části konfigurace SAML** v Azure Portal.

### <a name="create-zoho-one-test-user"></a>Vytvořit Zoho jednoho testovacího uživatele

Pokud chcete uživatelům Azure AD povolit, aby se přihlásili k Zohou, musí se zřídit do Zoho One. V jednom Zoho je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k Zoho jako správce zabezpečení.

2. Na kartě **Uživatelé** klikněte na **logo uživatele**.

    ![Zoho jednoho uživatele](./media/zoho-one-tutorial/user.png)

3. Na stránce **Přidat uživatele** proveďte následující kroky:

    ![Zoho jednoho přidat uživatele](./media/zoho-one-tutorial/add-user.png)
    
    a. Do textového pole **název** zadejte jméno uživatele jako **Britta Simon**.
    
    b. Do textového pole **e-mailová adresa** zadejte e-maily uživatele jako brittasimon@contoso.com .

    >[!Note]
    >Vyberte ověřenou doménu ze seznamu domén.

    c. Klikněte na **Přidat**.

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na Zoho adresu URL pro jedno přihlášení, kde můžete spustit tok přihlášení.  

* Přejít na Zoho adresu URL pro jedno přihlášení přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k Zoho, pro který jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na Zoho jednu dlaždici v části Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k Zoho, pro kterou jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Zoho můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).