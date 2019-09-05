---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s Jamf pro | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Jamf pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24f0d067351ab2e24b103624c8534ac8d6f2e757
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305451"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jamf-pro"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s Jamf pro

V tomto kurzu se dozvíte, jak integrovat Jamf pro s Azure Active Directory (Azure AD). Když integruje Jamf pro s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Jamf pro.
* Umožněte uživatelům, aby se přihlásili k Jamf pro s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Jamf pro jednotné přihlašování (SSO) s povoleným jednotným přihlašováním.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Jamf pro podporuje jednotné přihlašování (SSO) pro **SP a IDP** .

## <a name="adding-jamf-pro-from-the-gallery"></a>Přidání Jamf pro z Galerie

Pokud chcete nakonfigurovat integraci Jamf pro do služby Azure AD, musíte přidat Jamf pro z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Jamf pro** .
1. Z panelu výsledků vyberte **Jamf pro** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-jamf-pro"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Jamf pro

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Jamf pro pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Jamf pro.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Jamf pro, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Jamf pro SSO](#configure-jamf-pro-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte testovacího uživatele Jamf pro](#create-jamf-pro-test-user)** , abyste měli protějšek B. Simon v Jamf pro, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Jamf pro** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.jamfcloud.com/saml/metadata`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.jamfcloud.com/saml/SSO`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Zobrazí se skutečná hodnota identifikátoru z oddílu **jednotného přihlašování** na portálu Jamf pro, který je vysvětlen dále v tomto kurzu. Z hodnoty identifikátoru můžete extrahovat skutečnou hodnotu **subdomény** a použít ji v přihlašovací adrese URL a adrese URL odpovědi. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Jamf pro.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **Jamf pro**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-jamf-pro-sso"></a>Konfigurace jednotného přihlašování Jamf pro

1. Pokud chcete automatizovat konfiguraci v rámci služby Jamf pro, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **Nastavení Jamf pro** a přesměruje vás na aplikaci Jamf pro. Odtud zadejte přihlašovací údaje správce, které se přihlásí k Jamf pro. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-7.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit Jamf pro ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu Jamf pro jako správce a proveďte následující kroky:

4. Klikněte na **ikonu nastavení** v pravém horním rohu stránky.

    ![Konfigurace Jamf pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Klikněte na **jednotné přihlašování**.

    ![Konfigurace Jamf pro](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Na stránce **jednotného přihlašování** proveďte následující kroky:

    ![Konfigurace Jamf pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Zaškrtněte **možnost povolit ověřování jednotného přihlašování**.

    b. V rozevíracím seznamu **zprostředkovatel identity** vyberte možnost **jiný** .

    c. Do textového pole **druhý poskytovatel** zadejte **Azure AD**.

    d. Zkopírujte hodnotu **ID entity** a vložte ji do textového pole **identifikátor (ID entity)** v **základní části Konfigurace SAML** na Azure Portal.

    > [!NOTE]
    > V rámci této části je potřeba tuto hodnotu použít k dokončení přihlašovací adresy URL a adresy URL odpovědi v části **základní konfigurace SAML** na Azure Portal. `<SUBDOMAIN>`

    e. Jako možnost z rozevíracího seznamu **zdroj METADAT poskytovatele identity** vyberte **URL metadat** a v následujícím textovém poli vložte hodnotu **URL federačních metadat aplikace** , kterou jste zkopírovali z Azure Portal.

7. Přejděte na stejnou stránku v části mapování na **uživatele** a proveďte následující kroky: 

    ![Jamf pro Single](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Vyberte možnost **NameId** pro **mapování uživatelů zprostředkovatele identity**. Ve výchozím nastavení je toto nastavení nastaveno na **NameId** , ale je možné definovat vlastní atribut.

    b. Vyberte **e-mail** pro **mapování uživatelů JAMF pro**. Atributy SAML pro mapy Jamf pro jsou odesílány IdP následujícími způsoby: podle uživatelů a skupin. Když se uživatel pokusí získat přístup k Jamf pro, ve výchozím nastavení Jamf pro získá informace o uživateli od poskytovatele identity a porovná ho s uživatelskými účty Jamf pro. Pokud příchozí uživatelský účet v Jamf pro neexistuje, pak dojde k shodě názvu skupiny.

    c. Vložte hodnotu `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` do textového pole **název atributu skupiny zprostředkovatel identity** .

    d. Výběrem možnosti **dovolit uživatelům obejít ověřování pomocí jednotného přihlašování** nebude Přesměrováno na přihlašovací stránku zprostředkovatele identity pro ověřování, ale místo toho se může přihlásit k Jamf pro. Když se uživatel pokusí k Jamf pro získat přístup prostřednictvím poskytovatele identity, dojde k ověření a autorizaci IdPho jednotného přihlašování.

    e. Klikněte na **Uložit**.

### <a name="create-jamf-pro-test-user"></a>Vytvořit testovacího uživatele Jamf pro

Aby se uživatelé Azure AD mohli přihlašovat k Jamf pro, musí se zřídit v Jamf pro. V případě Jamf pro je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu společnosti Jamf pro jako správce.

2. Klikněte na **ikonu nastavení** v pravém horním rohu stránky.

    ![Přidat zaměstnance](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Klikněte na **uživatelské účty Jamf pro & skupiny**.

    ![Přidat zaměstnance](./media/jamfprosamlconnector-tutorial/user1.png)

4. Klikněte na možnost **Nové**.

    ![Přidat zaměstnance](./media/jamfprosamlconnector-tutorial/user2.png)

5. Vyberte **vytvořit standardní účet**.

    ![Přidat zaměstnance](./media/jamfprosamlconnector-tutorial/user3.png)

6. Na **novém DAILOG účtu** proveďte následující kroky:

    ![Přidat zaměstnance](./media/jamfprosamlconnector-tutorial/user4.png)

    a. Do textového pole **uživatelské jméno** zadejte celé jméno BrittaSimon.

    b. Vyberte odpovídající možnosti podle vaší organizace pro **úroveň přístupu**, **sadu oprávnění**a **stav přístupu**.

    c. Do textového pole **celé jméno** zadejte úplný název Britta Simon.

    d. Do textového pole **e-mailová adresa** zadejte e-mailovou adresu účtu Britta Simon.

    e. Do textového pole **heslo** zadejte heslo uživatele.

    f. Do textového pole **ověřit heslo** zadejte heslo uživatele.

    g. Klikněte na **Uložit**.

## <a name="test-sso"></a>Test SSO 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Jamf pro, měli byste se automaticky přihlásit k Jamf pro, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si Jamf pro s Azure AD](https://aad.portal.azure.com/)

