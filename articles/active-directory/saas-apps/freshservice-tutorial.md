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
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: 8767444e7ea839b1bb89cbb52833283fa01f7b5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90055550"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Freshservice

V tomto kurzu se dozvíte, jak integrovat Freshservice s Azure Active Directory (Azure AD). Když integrujete Freshservice s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Freshservice.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Freshservice svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Freshservice odběr s povoleným jednotným přihlašováním (SSO).

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Freshservice podporuje jednotné přihlašování iniciované v **SP**
* Po nakonfigurování Freshservice můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-freshservice-from-the-gallery"></a>Přidání Freshservice z Galerie

Pokud chcete nakonfigurovat integraci Freshservice do služby Azure AD, musíte přidat Freshservice z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
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

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Freshservice** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<company-name>.freshservice.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<company-name>.freshservice.com`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<company-name>.freshservice.com/login/saml`
    
    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí vlastního přihlašovací adresy URL, identifikátoru a adresy URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta Freshservice](https://support.freshservice.com/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte si certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Freshservice** na **Azure Portal**zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Freshservice.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Freshservice**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-freshservice-sso"></a>Konfigurace jednotného přihlašování Freshservice

1. Pokud chcete automatizovat konfiguraci v rámci Freshservice, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

1. Po přidání rozšíření do prohlížeče klikněte na **nastavit Freshservice** , které vás přesměruje do aplikace Freshservice. Odtud zadejte přihlašovací údaje správce, které se přihlásí k Freshservice. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Konfigurace instalace](common/setup-sso.png)

1. Pokud chcete nastavit Freshservice ručně, přihlaste se k webu Freshservice společnosti jako správce.

1. V nabídce na levé straně klikněte na **správce** a v **obecných nastaveních**vyberte **zabezpečení helpdesku** .

    ![Správce](./media/freshservice-tutorial/configure-1.png "Správce")

1. V části **zabezpečení**klikněte na **přejít na Freshworks 360 Security**.

    ![Zabezpečení](./media/freshservice-tutorial/configure-2.png "Zabezpečení")

1. V části **zabezpečení** proveďte následující kroky:

    ![Jednotné přihlašování](./media/freshservice-tutorial/configure-3.png "Jednotné přihlašování")
  
    a. V případě **jednotného přihlašování**vyberte **zapnuto**.

    b. V **metodě přihlášení**vyberte **jednotné přihlašování SAML**.

    c. Do **ID entity poskytnuté** textovým polem IDP vložte hodnotu **ID entity** , kterou jste zkopírovali z Azure Portal.

    d. V textovém poli **URL jednotného přihlašování SAML** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    e. V **možnostech podepisování**vyberte v rozevíracím seznamu **pouze podepsané kontrolní výrazy** .

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

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Freshservice, měli byste se automaticky přihlásit k Freshservice, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si Freshservice s Azure AD](https://aad.portal.azure.com/)