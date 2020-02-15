---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Jamf pro | Microsoft Docs'
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
ms.date: 02/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65b343c79c60bc74f1a140411ff1a4a27119d225
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251681"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-jamf-pro"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s Jamf pro

V tomto kurzu se dozvíte, jak integrovat Jamf pro s Azure Active Directory (Azure AD). Když integruje Jamf pro s Azure AD, můžete:

* Pomocí Azure AD můžete řídit, kdo má přístup k Jamf pro.
* Automaticky se přihlaste uživatele k Jamf pro pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Jamf pro s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. Jamf pro podporuje jednotné přihlašování iniciované v **SP** a **IDP** .

## <a name="add-jamf-pro-from-the-gallery"></a>Přidání Jamf pro z Galerie

Pokud chcete nakonfigurovat integraci Jamf pro do služby Azure AD, musíte přidat Jamf pro z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo svého osobního účet Microsoft.
1. V levém podokně vyberte službu **Azure Active Directory** .
1. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte *Jamf pro* .
1. Z panelu výsledků vyberte **Jamf pro** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-sso-in-azure-ad-for-jamf-pro"></a>Konfigurace a testování jednotného přihlašování v Azure AD pro Jamf pro

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Jamf pro pomocí testovacího uživatele s názvem B. Simon. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Jamf pro.

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Jamf pro.

1. [NAKONFIGURUJTE jednotné přihlašování ve službě Azure AD](#configure-sso-in-azure-ad) tak, aby uživatelé mohli tuto funkci používat.
    1. [Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user) pro otestování jednotného přihlašování Azure AD pomocí účtu B. Simon.
    1. [Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user) , aby B. Simon mohl používat jednotné přihlašování ve službě Azure AD.
1. [NAKONFIGURUJTE jednotné přihlašování v Jamf pro](#configure-sso-in-jamf-pro) pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. [Vytvořte testovacího uživatele Jamf](#create-a-jamf-pro-test-user) pro, který bude mít protějšek B. Simon v Jamf pro, který je propojený s Předprezentací Azure AD.
1. [Otestujte konfiguraci jednotného přihlašování](#test-the-sso-configuration) a ověřte, jestli konfigurace funguje.

## <a name="configure-sso-in-azure-ad"></a>Konfigurace jednotného přihlašování v Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Jamf pro** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyberte ikonu pera pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravte základní konfigurační stránku SAML.](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu **iniciované IDP** , zadejte v části **základní konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL, která používá následující vzorec: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL, která používá následující vzorec: `https://<subdomain>.jamfcloud.com/saml/SSO`

1. Vyberte **nastavit další adresy URL**. Chcete-li nakonfigurovat aplikaci v režimu **iniciované SP** , zadejte do textového pole **Adresa URL pro přihlášení** adresu URL, která používá následující vzorec: `https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Aktuální hodnotu identifikátoru získáte z oddílu **jednotného přihlašování** na portálu Jamf pro, který je vysvětlen dále v tomto kurzu. Z hodnoty identifikátoru můžete extrahovat skutečnou hodnotu subdomény a použít ji jako přihlašovací adresu URL a adresu URL odpovědi. Můžete se také podívat na vzorce zobrazené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** otevřete část **podpisový certifikát SAML** , vyberte tlačítko **Kopírovat** a zkopírujte **adresu URL federačních metadat aplikace**a uložte ji do svého počítače.

    ![Odkaz na stažení podpisového certifikátu SAML](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.
   1. Do pole **uživatelské jméno** zadejte [name] @ [doménaspolečnosti]. [rozšíření]. například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Vyberte **Create** (Vytvořit).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části udělíte B. Simon přístup k Jamf pro.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **Jamf pro**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Vybrat uživatele a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Vyberte tlačítko Přidat uživatele.](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak vyberte tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte příslušnou roli pro uživatele. Pak vyberte tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** vyberte tlačítko **přiřadit** .

## <a name="configure-sso-in-jamf-pro"></a>Konfigurace jednotného přihlašování v Jamf pro

1. Pokud chcete automatizovat konfiguraci v rámci Jamf pro, nainstalujte rozšíření **prohlížeče pro zabezpečené přihlašování k aplikacím** výběrem možnosti **nainstalovat rozšíření**.

    ![Stránka pro rozšíření přihlašovacího prohlížeče zabezpečeného přihlášení k aplikacím](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče vyberte **nastavit Jamf pro**. Po otevření aplikace Jamf pro zadejte přihlašovací údaje správce pro přihlášení. Rozšíření prohlížeče automaticky provede konfiguraci aplikace a automatizuje kroky 3 až 7.

    ![Stránka Konfigurace nastavení v Jamf pro](common/setup-sso.png)

3. Pokud chcete nastavit Jamf pro ručně, otevřete nové okno webového prohlížeče a přihlaste se ke svému webu Jamf pro jako správce. Pak proveďte následující kroky.

4. Vyberte **ikonu nastavení** v pravém horním rohu stránky.

    ![Výběr ikony nastavení v Jamf pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Vyberte **jednotné přihlašování**.

    ![Vyberte jednotné přihlašování v Jamf pro.](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Na stránce **jednotného přihlašování** proveďte následující kroky.

    ![Stránka jednotného přihlašování v Jamf pro](./media/jamfprosamlconnector-tutorial/configure3.png)

  a. Vyberte **Upravit**.

  b. Zaškrtněte políčko **Povolit ověřování pomocí jednotného přihlašování** .

  c. V rozevírací nabídce **zprostředkovatele identity** vyberte **Azure** jako možnost.

  d. Zkopírujte hodnotu **ID entity** a vložte ji do pole **identifikátor (ID entity)** v části **základní konfigurace SAML** v Azure Portal.

> [!NOTE]
> Pomocí hodnoty v poli `<SUBDOMAIN>` dokončete přihlašovací adresu URL a adresu URL odpovědi v části **základní konfigurace SAML** v Azure Portal.

  e. Z rozevírací nabídky **zdroje metadat zprostředkovatele identity** vyberte **metadata URL** . Do zobrazeného pole vložte hodnotu **adresy URL federačních metadat aplikace** , kterou jste zkopírovali z Azure Portal.

  f. Volitelné Upravte hodnotu vypršení platnosti tokenu nebo vyberte Zakázat vypršení platnosti tokenu SAML.

7. Na stejné stránce se posuňte dolů k oddílu **mapování uživatelů** . Pak proveďte následující kroky.

    ![Oddíl mapování uživatelů na stránce jednotného přihlašování v Jamf pro.](./media/jamfprosamlconnector-tutorial/tutorial-jamfprosamlconnector-single.png)

    a. Vyberte možnost **NameId** pro **mapování uživatelů zprostředkovatele identity**. Ve výchozím nastavení je tato možnost nastavená na **NameId**, ale můžete definovat vlastní atribut.

    b. Vyberte **e-mail** pro **mapování uživatelů Jamf pro**. Mapy Jamf pro mapují atributy SAML, které jsou nejprve IdP uživateli a pak podle skupin. Když se uživatel pokusí získat přístup k Jamf pro, Jamf pro získá informace o uživateli od poskytovatele identity a porovná ho se všemi uživatelskými účty Jamf pro. Pokud se nenalezne příchozí uživatelský účet, Jamf pro se pokusí ho porovnat s názvem skupiny.

    c. `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` hodnoty vložte do pole **název atributu skupiny zprostředkovatel identity** .

    d. Na stejné stránce přejděte dolů k části **zabezpečení** a vyberte možnost **dovolit uživatelům obejít ověřování pomocí jednotného přihlašování**. Výsledkem je, že uživatelé nebudou přesměrováni na přihlašovací stránku zprostředkovatele identity pro ověřování a místo toho se můžou přihlašovat k Jamf pro. Když se uživatel pokusí k Jamf pro získat přístup prostřednictvím poskytovatele identity, dojde k ověření a autorizaci IdPho jednotného přihlašování.

    e. Vyberte **Save** (Uložit).

### <a name="create-a-jamf-pro-test-user"></a>Vytvoření testovacího uživatele Jamf pro

Aby se uživatelé Azure AD přihlásili k Jamf pro, musí se zřídit v Jamf pro. Zřizování v Jamf pro je ruční úloha.

Chcete-li zřídit uživatelský účet, proveďte následující kroky:

1. Přihlaste se k webu společnosti Jamf pro jako správce.

2. V pravém horním rohu stránky vyberte ikonu **Nastavení** .

    ![Ikona nastavení v Jamf pro](./media/jamfprosamlconnector-tutorial/configure1.png)

3. **Pro & skupiny vyberte uživatelské účty pro Jamf pro**.

    ![Ikona skupin & uživatelských účtů pro Jamf pro v nastavení Jamf pro](./media/jamfprosamlconnector-tutorial/user1.png)

4. Vyberte **Nový**.

    ![Uživatelské účty Jamf pro – stránka nastavení systému skupin &](./media/jamfprosamlconnector-tutorial/user2.png)

5. Vyberte **vytvořit standardní účet**.

    ![Možnost vytvořit standardní účet na stránce & skupiny uživatelských účtů Jamf pro](./media/jamfprosamlconnector-tutorial/user3.png)

6. V dialogovém okně **nový účet** proveďte následující kroky.

    ![Možnosti nastavení nového účtu v nastavení systému Jamf pro](./media/jamfprosamlconnector-tutorial/user4.png)

    a. Do pole **uživatelské jméno** zadejte `Britta Simon`, celé jméno testovacího uživatele.

    b. Vyberte možnosti pro **úroveň přístupu**, **sadu oprávnění**a **stav přístupu** , které jsou v souladu s vaší organizací.

    c. Do pole **celé jméno** zadejte `Britta Simon`.

    d. Do pole **e-mailová adresa** zadejte e-mailovou adresu účtu Britta Simon.

    e. Do pole **heslo** zadejte heslo uživatele.

    f. Do pole **ověřit heslo** zadejte heslo uživatele znovu.

    g. Vyberte **Save** (Uložit).

## <a name="test-the-sso-configuration"></a>Test konfigurace jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu vyberete dlaždici Jamf pro, měli byste se automaticky přihlásit k účtu Jamf pro, pro který jste nakonfigurovali jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Kurzy pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si Jamf pro s Azure AD](https://aad.portal.azure.com/)
