---
title: 'Kurz: Azure Active Directory integrace s Zoho | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zoho.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: c5778f39a5091753a1658ec121379a4ed29a7542
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648369"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Kurz: Azure Active Directory integrace s Zoho

V tomto kurzu se dozvíte, jak integrovat Zoho s Azure Active Directory (Azure AD). Když integrujete Zoho s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Zoho.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Zoho svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Pokud chcete nakonfigurovat integraci Azure AD s Zoho, budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Zoho odběr povoleného jednotného přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Zoho podporuje jednotné přihlašování iniciované v **SP**

## <a name="add-zoho-from-the-gallery"></a>Přidání Zoho z Galerie

Pokud chcete nakonfigurovat integraci Zoho do služby Azure AD, musíte přidat Zoho z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Zoho** .
1. Na panelu výsledků vyberte **Zoho** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-zoho"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Zoho

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Zoho pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Zoho.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí Zoho postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Zoho SSO](#configure-zoho-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Zoho Test User](#create-zoho-test-user)** -to, abyste měli protějšek B. Simon v Zoho, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Zoho** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<company name>.zohomail.com`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou Sign-On. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta Zoho](https://www.zoho.com/mail/contact.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení Zoho** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Zoho.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Zoho**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="configure-zoho-sso"></a>Konfigurace jednotného přihlašování Zoho

1. V jiném okně webového prohlížeče se přihlaste k webu společnosti Zoho mail jako správce.

2. Otevřete **Ovládací panely**.
   
    ![Ovládací panel](./media/zoho-mail-tutorial/control-panel.png "Ovládací panel")

3. Klikněte na kartu **ověřování SAML** .
   
    ![Ověřování SAML](./media/zoho-mail-tutorial/saml-authentication.png "Ověřování SAML")

4. V části **Podrobnosti ověřování SAML** proveďte následující kroky:
   
    ![Podrobnosti ověřování SAML](./media/zoho-mail-tutorial/details.png "Podrobnosti ověřování SAML")
   
    a. Do textového pole **Adresa URL pro přihlášení** vložte **přihlašovací adresu URL** , kterou jste zkopírovali z Azure Portal.
   
    b. V textovém poli **Adresa URL pro odhlášení** vložte **adresu URL** pro odhlášení, kterou jste zkopírovali z Azure Portal.
   
    c. V poli **změnit adresu URL hesla** vložte adresu **URL pro změnu hesla** , kterou jste zkopírovali z Azure Portal.
       
    d. Otevřete v programu Poznámkový blok certifikát s kódováním Base-64, který jste stáhli z Azure Portal, zkopírujte jeho obsah do schránky a vložte ho do textového pole **PublicKey** .
   
    e. Jako **algoritmus** vyberte **RSA**.
   
    f. Klikněte na **OK**.

### <a name="create-zoho-test-user"></a>Vytvořit testovacího uživatele Zoho

Aby se uživatelé Azure AD mohli přihlásit k e-mailu Zoho, musí se zřídit v e-mailu Zoho. V případě Zoho pošty je zřizování ručním úkolem.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů Zoho mail nebo rozhraní API, které poskytuje Zoho mail.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Chcete-li zřídit uživatelský účet, proveďte následující kroky:

1. Přihlaste se k webu společnosti **Zoho mail** jako správce.

1. Přejít na **ovládací panel \> Pošta & docs**.

1. Přejít na **Podrobnosti o uživateli \> Přidat uživatele**.
   
    ![Snímek obrazovky se zobrazí na webu Zoho mail s podrobnostmi o uživateli a vybraným uživatelem.](./media/zoho-mail-tutorial/add-user-1.png "Přidání uživatele")

1. V dialogovém okně **Přidat uživatele** proveďte následující kroky:
   
    ![Snímek obrazovky se zobrazí dialogové okno Přidat uživatele, kde můžete zadat hodnoty, které jsou popsány.](./media/zoho-mail-tutorial/add-user-2.png "Přidání uživatele")
   
    a. Do textového pole **jméno v prvním** poli zadejte jméno uživatele jako **Britta**.

    b. Do textového pole **příjmení** zadejte jméno uživatele jako **Simon**.

    c. Do textového pole **ID e-mailu** zadejte ID e-mailu uživatele, jako je **brittasimon \@ contoso.com**.

    d. Do textového pole **heslo** zadejte heslo uživatele.
   
    e. Klikněte na **OK**.  
      
    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mail s odkazem na potvrzení účtu předtím, než se aktivuje.

### <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k Zoho, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení k Zoho přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Zoho v okně moje aplikace, měli byste se automaticky přihlásit k Zoho, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Zoho můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).