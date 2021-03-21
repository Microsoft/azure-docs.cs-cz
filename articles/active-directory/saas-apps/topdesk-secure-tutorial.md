---
title: 'Kurz: Azure Active Directory integrace s TOPdesk – Secure | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TOPdesk-Secure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 5ed23889d8648c65ea0887d2f0f3406b50291f12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101654289"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Kurz: Azure Active Directory integrace s TOPdesk – Secure

V tomto kurzu se dozvíte, jak integrovat TOPdesk-Secure s Azure Active Directory (Azure AD). Když integrujete TOPdesk-Secure s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k TOPdesk-Secure.
* Umožněte uživatelům, aby se automaticky přihlásili k TOPdesk (jednotné přihlašování) pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:
 
 * Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* TOPdesk – odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* TOPdesk – zabezpečená podpora pro jednotné přihlašování s podporou **SP**

## <a name="add-topdesk---secure-from-the-gallery"></a>Přidání TOPdesk – zabezpečení z Galerie

Pokud chcete nakonfigurovat integraci TOPdesk-Secure do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat TOPdesk – zabezpečený z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **TopDesk-Secure** .
1. Na panelu výsledků vyberte **TopDesk-Secure** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-topdesk---secure"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro TOPdesk – Secure

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí TOPdesk – Secure na základě testovacího uživatele s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v TOPdesk.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí TOPdesk-Secure, musíte provést následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
    2. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
2. **[Nakonfigurujte TopDesk – Secure SSO](#configure-topdesk---secure-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
    1. **[Vytvořte TopDesk – zabezpečený testový uživatel](#create-topdesk---secure-test-user)** – pro Britta Simon v TopDesk-Secure, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí TOPdesk – Secure, proveďte následující kroky:

1. V Azure Portal na stránce integrace aplikace **TopDesk – zabezpečení** vyberte **jednotné přihlašování**.

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

3. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu tužky a otevřete dialogové okno **základní konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.topdesk.net`

    b. V poli **Adresa URL identifikátoru** zadejte adresu URL metadat TopDesk, kterou můžete načíst z konfigurace TopDesk. Měl by používat následující vzor: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným Sign-On URL, identifikátor a adresu URL odpovědi. Pokud chcete získat tyto hodnoty, kontaktujte [TopDesk – tým podpory pro zabezpečené klienty](https://www.topdesk.com/us/support/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení TopDesk-Secure** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k TOPdesk-Secure.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **TopDesk-Secure**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="configure-topdesk---secure-sso"></a>Konfigurace TOPdesk – zabezpečené jednotné přihlašování

1. Přihlaste se k vašemu **TopDesk** firemnímu webu jako správce.

2. V nabídce **TopDesk** klikněte na **Nastavení**.

    ![Nastavení](./media/topdesk-secure-tutorial/ic790598.png "Nastavení")

3. Klikněte na tlačítko **Nastavení přihlášení**.

    ![Nastavení přihlášení](./media/topdesk-secure-tutorial/ic790599.png "Nastavení přihlášení")

4. Rozbalte nabídku **Nastavení přihlášení** a pak klikněte na **Obecné**.

    ![Obecné](./media/topdesk-secure-tutorial/ic790600.png "Obecné")

5. V části **zabezpečení** v části konfigurace **přihlášení SAML** proveďte následující kroky:

    ![Technické nastavení](./media/topdesk-secure-tutorial/ic790855.png "Technické nastavení")

    a. Klikněte na tlačítko **Stáhnout** a Stáhněte si soubor veřejné metadat a uložte ho místně na svém počítači.

    b. Otevřete soubor metadat a vyhledejte uzel **AssertionConsumerService** .

    ![Služba příjemce kontrolního výrazu](./media/topdesk-secure-tutorial/ic790856.png "Služba příjemce kontrolního výrazu")

    c. Zkopírujte hodnotu **AssertionConsumerService** a vložte tuto hodnotu do textového pole Adresa URL odpovědi v části **TopDesk – zabezpečení domény a adresy URL** .

6. Chcete-li vytvořit soubor certifikátu, proveďte následující kroky:

    ![Certifikát](./media/topdesk-secure-tutorial/ic790606.png "Certifikát")

    a. Otevřete stažený soubor metadat z Azure Portal.

    b. Rozbalte uzel **RoleDescriptor** , který má **atribut xsi: Type** dodaný **: ApplicationServiceType**.

    c. Zkopírujte hodnotu uzlu **certifikátu x509** .

    d. Uložte zkopírovanou hodnotu **certifikátu x509** lokálně na svém počítači do souboru.

7. V části **Public** klikněte na **Přidat**.

    ![Přidat](./media/topdesk-secure-tutorial/ic790607.png "Přidání")

8. Na stránce **Průvodce konfigurací SAML** proveďte následující kroky:

    ![Pomocník pro konfiguraci SAML](./media/topdesk-secure-tutorial/ic790608.png "Pomocník pro konfiguraci SAML")

    a. Pokud chcete nahrát stažený soubor metadat z Azure Portal, klikněte v části **federační metadata** na **Procházet**.

    b. Pokud chcete nahrát soubor certifikátu, klikněte v části **certifikát (RSA)** na **Procházet**.

    c. U **privátního klíče (RSA, PKCS8, der)** můžete nahrát svůj vlastní privátní klíč, nebo můžete kontaktovat [tým podpory TopDesk – zabezpečeného klienta](https://www.topdesk.com/us/support) , abyste získali privátní klíč.

    d. Pokud chcete nahrát soubor loga, který jste dostali z týmu podpory TOPdesk, klikněte v části **ikona loga** na **Procházet**.

    e. Do textového pole **atributu uživatelského jména** zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    f. Do textového pole **Zobrazovaný název** zadejte název konfigurace.

    například Klikněte na **Uložit**.

### <a name="create-topdesk---secure-test-user"></a>Vytvořit TOPdesk – zabezpečený testovací uživatel

Aby se uživatelé Azure AD mohli přihlašovat k TOPdesk-Secure, musí se zřídit v TOPdesk-Secure.  
V případě TOPdesk-Secure je zřizování ručním úkolem.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Při konfiguraci zřizování uživatelů proveďte následující kroky:

1. Přihlaste se k webu společnosti **TopDesk** jako správce.

2. V nabídce v horní části klikněte na **TopDesk \> New \> support Files \> Operator**.

    ![Operátor](./media/topdesk-secure-tutorial/ic790610.png "Operátor")

3. V dialogovém okně **Nový operátor** proveďte následující kroky:

    ![New – operátor](./media/topdesk-secure-tutorial/ic790611.png "Operátor new")

    a. Klikněte na kartu **Obecné**.

    b. Do textového pole **příjmení** zadejte příjmení uživatele, jako je **Simon**.

    c. V části **umístění** vyberte **lokalitu** pro účet.

    d. Do textového pole **přihlašovací jméno** v části **přihlášení TopDesk** zadejte přihlašovací jméno uživatele.

    e. Klikněte na **Uložit**.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jakýkoli jiný TOPdesk nástrojů pro vytváření uživatelských účtů nebo rozhraní API, které poskytuje TOPdesk – Secure.

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlašování TOPdesk, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro zabezpečené přihlašování přímo z TOPdesk a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Po kliknutí na dlaždici TOPdesk-Secure v okně moje aplikace byste měli být automaticky přihlášeni k TOPdesk – zabezpečení, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování TOPdesk můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).