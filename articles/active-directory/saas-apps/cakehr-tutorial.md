---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s CakeHR | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a CakeHR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.openlocfilehash: 8464d8918ccb6351de06dcba9a355e93c2cd97cd
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91740339"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cakehr"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s CakeHR

V tomto kurzu se dozvíte, jak integrovat CakeHR s Azure Active Directory (Azure AD). Když integrujete CakeHR s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k CakeHR.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k CakeHR svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* CakeHR odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* CakeHR podporuje jednotné přihlašování iniciované v **SP**

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-cakehr-from-the-gallery"></a>Přidání CakeHR z Galerie

Pokud chcete nakonfigurovat integraci CakeHR do služby Azure AD, musíte přidat CakeHR z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **CakeHR** .
1. Na panelu výsledků vyberte **CakeHR** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cakehr"></a>Konfigurace a testování jednotného přihlašování Azure AD pro CakeHR

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí CakeHR pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v CakeHR.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí CakeHR, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte CAKEHR SSO](#configure-cakehr-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte CakeHR Test User](#create-cakehr-test-user)** -to, abyste měli protějšek B. Simon v CakeHR, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **CakeHR** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<yourcakedomain>.cake.hr/`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<yourcakedomain>.cake.hr/services/saml/consume`
    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL a adresy URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta CakeHR](mailto:info@cake.hr) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. V části **podpisový certifikát SAML** kliknutím na tlačítko **Upravit** otevřete dialogové okno **podpisový certifikát SAML** .

    ![Upravit podpisový certifikát SAML](common/edit-certificate.png)

1. V části **podpisový certifikát SAML** Zkopírujte hodnotu **kryptografického otisku** a uložte ji do poznámkového bloku.

    ![Kopírovat hodnotu kryptografického otisku](common/copy-thumbprint.png)

1. V části **Nastavení CakeHR** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Create** (Vytvořit).

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k CakeHR.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **CakeHR**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-cakehr-sso"></a>Konfigurace jednotného přihlašování CakeHR

1. Pokud chcete automatizovat konfiguraci v rámci CakeHR, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

1. Po přidání rozšíření do prohlížeče klikněte na **nastavit CakeHR** , které vás přesměruje do aplikace CakeHR. Odtud zadejte přihlašovací údaje správce, které se přihlásí k CakeHR. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-5.

    ![Konfigurace instalace](common/setup-sso.png)

1. Pokud chcete nastavit CakeHR ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu CakeHR společnosti jako správce a proveďte následující kroky:

1. V pravém horním rohu stránky klikněte na **profil** a potom přejděte na **Nastavení**.

    ![Snímek obrazovky zobrazuje profil s vybraným nastavením.](./media/cakehr-tutorial/config01.png)

1. Na levé straně řádku nabídek klikněte na integrace s protokolem **INTEGRATIONS**  >  **SAML SSO** a proveďte následující kroky:

    ![Snímek obrazovky se zobrazí v podokně nastavení, ve kterém provedete tyto kroky.](./media/cakehr-tutorial/config02.png)

    a. Do textového pole **ID entity** zadejte `cake.hr` .

    b. Do textového pole **Adresa URL pro ověřování** vložte hodnotu **přihlašovací adresa URL**, kterou jste zkopírovali z Azure Portal.

    c. V textovém poli **otisk klíče (formát SHA1)** vložte hodnotu **kryptografického otisku** , kterou jste zkopírovali z Azure Portal.

    d. Zaškrtněte políčko **Povolit jednotné přihlašování** .

    e. Klikněte na **Uložit**.

### <a name="create-cakehr-test-user"></a>Vytvořit testovacího uživatele CakeHR

Aby se uživatelé Azure AD mohli přihlašovat k CakeHR, musí se zřídit v CakeHR. V CakeHR je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k CakeHR jako správce zabezpečení.

2. Na levé straně řádku nabídek klikněte na Přidat do **společnosti**  >  **ADD**.

    ![Snímek obrazovky ukazuje CakeHR s FIRMou a vybranou možnost Přidat.](./media/cakehr-tutorial/config03.png)

3. V automaticky otevíraném okně **Přidat nový zaměstnanec** proveďte následující kroky:

     ![Snímek obrazovky ukazuje přidat nového zaměstnance, na kterém provedete tyto kroky.](./media/cakehr-tutorial/config04.png)

    a. Do textového pole jméno a **příjmení** zadejte jméno uživatele jako B. Simon.

    b. Do textového pole **pracovní e-mail** zadejte e-mail uživatele, jako je například `B.Simon@contoso.com` .

    c. Klikněte na **vytvořit účet**.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici CakeHR, měli byste se automaticky přihlásit k CakeHR, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si CakeHR s Azure AD](https://aad.portal.azure.com/)
