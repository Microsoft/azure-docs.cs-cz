---
title: 'Kurz: Azure Active Directory integrace s Zendesk | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zendesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: jeedes
ms.openlocfilehash: 6059f0b3c102fed04c52b87a2c7c226d508184c1
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519040"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zendesk"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Zendesk

V tomto kurzu se dozvíte, jak integrovat Zendesk s Azure Active Directory (Azure AD). Když integrujete Zendesk s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Zendesk.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Zendesk svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Zendesk odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Zendesk podporuje jednotné přihlašování iniciované v **SP**
* Zendesk podporuje [ **automatizované** zřizování uživatelů](zendesk-provisioning-tutorial.md)
* Po nakonfigurování Zendesk můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-zendesk-from-the-gallery"></a>Přidání Zendesk z Galerie

Pokud chcete nakonfigurovat integraci Zendesk do služby Azure AD, musíte přidat Zendesk z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Zendesk** .
1. Na panelu výsledků vyberte **Zendesk** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-zendesk"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Zendesk

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Zendesk pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Zendesk.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Zendesk, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte ZENDESK SSO](#configure-zendesk-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Zendesk Test User](#create-zendesk-test-user)** -to, abyste měli protějšek B. Simon v Zendesk, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Zendesk** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.zendesk.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.zendesk.com`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.zendesk.com/access/saml`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí vlastního přihlašovací adresy URL, identifikátoru a adresy URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta Zendesk](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Zendesk aplikace očekává kontrolní výrazy SAML v určitém formátu. Neexistují žádné povinné atributy SAML, ale Volitelně můžete spravovat z oddílu **atributy uživatele** na stránce integrace aplikací. Na stránce **nastavit jeden Sign-On se** stránkou SAML kliknutím na tlačítko **Upravit** otevřete dialogové okno **atributy uživatele** .

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Pomocí atributů rozšíření ve výchozím nastavení přidáte atributy, které nejsou ve službě Azure AD. Klikněte na [atributy uživatele, které se dají nastavit v SAML](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) , aby se získal úplný seznam atributů SAML, které **Zendesk** akceptuje.

1. V části **podpisový certifikát SAML** kliknutím na tlačítko **Upravit** otevřete dialogové okno **podpisový certifikát SAML** .

    ![Upravit podpisový certifikát SAML](common/edit-certificate.png)

1. V části **podpisový certifikát SAML** zkopírujte **hodnotu kryptografického otisku** a uložte ji do svého počítače.

    ![Kopírovat hodnotu kryptografického otisku](common/copy-thumbprint.png)

1. V části **Nastavení Zendesk** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Zendesk.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Zendesk**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-zendesk-sso"></a>Konfigurace jednotného přihlašování Zendesk

1. Pokud chcete automatizovat konfiguraci v rámci **Zendesk**, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![image](./media/target-process-tutorial/install_extension.png)

1. Po přidání rozšíření do prohlížeče klikněte na **Zendesk nastavení** a nasměrujte vás na aplikaci Zendesk. Odtud zadejte přihlašovací údaje správce, které se přihlásí k Zendesk. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Konfigurace instalace](common/setup-sso.png)

1. Pokud chcete nastavit Zendesk ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu Zendesk společnosti jako správce a proveďte následující kroky:

1. V **centru pro správu Zendesk**klikněte na **nastavení zabezpečení** na kartě **zabezpečení** .

    ![Zabezpečení](./media/zendesk-tutorial/settings.png "Zabezpečení")

1. Přejděte na stránku **jednotného přihlašování** a klikněte na **Upravit** v **SAML**.

    ![Zabezpečení](./media/zendesk-tutorial/saml-sso.png "Zabezpečení")

1. Na stránce **jednotného přihlašování** proveďte následující kroky.

    ![Jednotné přihlašování](./media/zendesk-tutorial/saml-configuration.png "Jednotné přihlašování")

    a. Do textového pole **URL jednotného přihlašování SAML** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    b. Do textového pole **otisku certifikátu** vložte hodnotu **kryptografického otisku** certifikátu, který jste zkopírovali z Azure Portal.

    c. Do textového pole **Adresa URL pro vzdálené odhlášení** vložte hodnotu **URL** pro odhlášení, kterou jste zkopírovali z Azure Portal.

    d. Klikněte na **Uložit**.

### <a name="create-zendesk-test-user"></a>Vytvořit testovacího uživatele Zendesk

Cílem této části je vytvořit uživatele s názvem Britta Simon v Zendesk. Zendesk podporuje automatické zřizování uživatelů, které je ve výchozím nastavení povolené. Další podrobnosti najdete v [tématu](Zendesk-provisioning-tutorial.md) konfigurace automatického zřizování uživatelů.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Zendesk, měli byste se automaticky přihlásit k Zendesk, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si Zendesk s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chránit Zendesk pomocí pokročilých viditelností a ovládacích prvků](/cloud-app-security/proxy-intro-aad)

- [Konfigurace zřizování uživatelů](zendesk-provisioning-tutorial.md)