---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Freshservice | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Freshservice.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: fb8b12cdb8fd9ed37ac4086d213183e800d6febc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651743"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Freshservice

V tomto kurzu se dozvíte, jak integrovat Freshservice s Azure Active Directory (Azure AD). Když integrujete Freshservice s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Freshservice.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Freshservice svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Freshservice odběr s povoleným jednotným přihlašováním (SSO).

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Freshservice podporuje jednotné přihlašování iniciované v **SP**

## <a name="add-freshservice-from-the-gallery"></a>Přidání Freshservice z Galerie

Pokud chcete nakonfigurovat integraci Freshservice do služby Azure AD, musíte přidat Freshservice z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Freshservice** .
1. Na panelu výsledků vyberte **Freshservice** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-freshservice"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Freshservice

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Freshservice pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Freshservice.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Freshservice, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte FRESHSERVICE SSO](#configure-freshservice-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Freshservice Test User](#create-freshservice-test-user)** -to, abyste měli protějšek B. Simon v Freshservice, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Freshservice** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<company-name>.freshservice.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<company-name>.freshservice.com`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<company-name>.freshservice.com/login/saml`
    
    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí vlastního přihlašovací adresy URL, identifikátoru a adresy URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta Freshservice](https://support.freshservice.com/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte si certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Freshservice** na **Azure Portal** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Freshservice.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Freshservice**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-freshservice-sso"></a>Konfigurace jednotného přihlašování Freshservice

1. Pokud chcete automatizovat konfiguraci v rámci Freshservice, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

1. Po přidání rozšíření do prohlížeče klikněte na **nastavit Freshservice** , které vás přesměruje do aplikace Freshservice. Odtud zadejte přihlašovací údaje správce, které se přihlásí k Freshservice. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Konfigurace instalace](common/setup-sso.png)

1. Pokud chcete nastavit Freshservice ručně, přihlaste se k webu Freshservice společnosti jako správce.

1. V nabídce na levé straně klikněte na **správce** a v **obecných nastaveních** vyberte **zabezpečení helpdesku** .

    ![Správce](./media/freshservice-tutorial/configure-1.png "Správce")

1. V části **zabezpečení** klikněte na **přejít na Freshservice 360 Security**.

    ![Zabezpečení](./media/freshservice-tutorial/configure-2.png "Zabezpečení")

1. V části **zabezpečení** proveďte následující kroky:

    ![Jednotné přihlašování](./media/freshservice-tutorial/configure-3.png "Jednotné přihlašování")
  
    a. V případě **jednotného přihlašování** vyberte **zapnuto**.

    b. V **metodě přihlášení** vyberte **jednotné přihlašování SAML**.

    c. Do **ID entity poskytnuté** textovým polem IDP vložte hodnotu **ID entity** , kterou jste zkopírovali z Azure Portal.

    d. V textovém poli **URL jednotného přihlašování SAML** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    e. V **možnostech podepisování** vyberte v rozevíracím seznamu **pouze podepsané kontrolní výrazy** .

    f. Do textového pole **Adresa URL pro odhlášení** vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    například Do textového pole **certifikát zabezpečení** vložte hodnotu **certifikátu (Base64)** , kterou jste získali dříve.
  
    h. Klikněte na **Uložit**.


## <a name="create-freshservice-test-user"></a>Vytvořit testovacího uživatele Freshservice

Aby se uživatelé Azure AD mohli přihlašovat k FreshService, musí se zřídit v FreshService. V případě FreshService je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu **FreshService** společnosti jako správce.

2. V nabídce na levé straně klikněte na **správce**.

3. V části **Správa uživatelů** klikněte na možnost **žadatelé**.

    ![Žadatelů o deklaraci](./media/freshservice-tutorial/create-user-1.png "Žadatelů o deklaraci")

4. Klikněte na **Nový žadatel**.

    ![Noví žadatelé](./media/freshservice-tutorial/create-user-2.png "Noví žadatelé")

5. V části **Nový žadatel** zadejte požadovaná pole a klikněte na **Uložit**.
    ![Nový žadatel](./media/freshservice-tutorial/create-user-3.png "Nový žadatel")  

    > [!NOTE]
    > Držitel účtu Azure Active Directory získá e-mail, včetně odkazu na potvrzení účtu, než bude aktivní.
    >  

    > [!NOTE]
    > K zřizování uživatelských účtů Azure AD můžete použít jiné nástroje pro vytváření uživatelských účtů FreshService nebo rozhraní API poskytovaná FreshService.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k Freshservice, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení k Freshservice přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Freshservice v okně moje aplikace, měli byste se automaticky přihlásit k Freshservice, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

 Po nakonfigurování Freshservice můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).