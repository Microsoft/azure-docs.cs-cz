---
title: 'Kurz: Integrace jednotného přihlašování (SSO) služby Azure Active Directory s Jamf Pro | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Jamf Pro.
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
ms.openlocfilehash: 1d83dbe756e8e6acdb58861ac359801bc13a63c4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77373194"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-jamf-pro"></a>Kurz: Integrace služby Azure Active Directory SSO s Jamf Pro

V tomto kurzu se dozvíte, jak integrovat Jamf Pro s Azure Active Directory (Azure AD). Když integrujete Jamf Pro s Azure AD, můžete:

* Azure AD slouží k řízení, kdo má přístup k Jamf Pro.
* Automaticky se přihlaste k Jamf Pro pomocí svých účtů Azure AD.
* Spravujte své účty na jednom centrálním místě: na portálu Azure.

Další informace o integraci aplikací SaaS s Azure AD najdete v [tématu Jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte předplatné, můžete získat bezplatný [účet](https://azure.microsoft.com/free/).
* Předplatné Jamf Pro, které je jednotné přihlašování (SSO) povoleno.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete přisypná ní služby Azure AD v testovacím prostředí. 

* Jamf Pro podporuje **sp-inicioval** a **IdP-inicioval** sso.
* Jakmile nakonfigurujete Jamf Pro, můžete vynutit řízení relací, které chrání exfiltraci a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relací se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutit řízení relací pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="add-jamf-pro-from-the-gallery"></a>Přidat Jamf Pro z galerie

Pokud chcete nakonfigurovat integraci Jamf Pro do Azure AD, musíte Jamf Pro přidat z galerie do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo svého osobního účtu Microsoft.
1. V levém podokně vyberte službu **Azure Active Directory.**
1. Přejděte na **položku Podnikové aplikace**a vyberte **možnost Všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte **možnost Nová aplikace**.
1. V části **Přidat z galerie** zadejte do vyhledávacího pole *Jamf Pro.*
1. Z panelu výsledků vyberte **Jamf Pro** a přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-sso-in-azure-ad-for-jamf-pro"></a>Konfigurace a testování přihlašovaného zabezpečení ve službě Azure AD pro Jamf Pro

Konfigurace a testování služby Azure AD SSO s Jamf Pro pomocí testovacího uživatele s názvem B.Simon. Aby služby Přihlašovací služby fungovaly, je třeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Jamf Pro.

V této části nakonfigurujete a otestujete přihlašované službou Azure AD s Jamf Pro.

1. [Nakonfigurujte služby Přizpůsobé uživatelské ho správě ve službě Azure AD](#configure-sso-in-azure-ad) tak, aby vaši uživatelé mohli používat tuto funkci.
    1. [Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user) pro testování služby Přisuzované k obnovení služby Azure AD s účtem B.Simon.
    1. [Přiřaďte uživatele testu Azure AD](#assign-the-azure-ad-test-user) tak, aby B.Simon můžete použít přisuzování uživatelů ve službě Azure AD.
1. [Konfigurace sso v Jamf Pro](#configure-sso-in-jamf-pro) pro konfiguraci nastavení spřimit na straně aplikace.
    1. [Vytvořte testovacího uživatele Jamf Pro,](#create-a-jamf-pro-test-user) který bude mít v Jamf Pro protějšek B.Simona, který je propojený s reprezentací Azure AD uživatele.
1. [Otestujte konfiguraci s](#test-the-sso-configuration) přiděluje a ověřte, zda konfigurace funguje.

## <a name="configure-sso-in-azure-ad"></a>Konfigurace přizpůsobovaného při spoje ve službě Azure AD

V této části povolíte služby Přisuzuje azure ad na webu Azure Portal.

1. Na [webu Azure Portal](https://portal.azure.com/)najdete na stránce integrace aplikací **Jamf Pro** oddíl **Správa** a vyberte **jedno přihlášení**.
1. Na stránce **Select a Single Sign-On Method** vyberte možnost **SAML**.
1. Na stránce **Nastavit jednotné přihlašování pomocí saml** vyberte ikonu pera pro **základní konfiguraci SAML a** upravte nastavení.

   ![Upravte stránku Konfigurace základní saml.](common/edit-urls.png)

1. Pokud chcete aplikaci konfigurovat v režimu **iniciovaném protokolem IdP** v části **Základní konfigurace SAML,** zadejte hodnoty pro následující pole:

    a. Do textového pole **Identifikátor** zadejte adresu URL, která používá následující vzorec:`https://<subdomain>.jamfcloud.com/saml/metadata`

    b. Do textového pole **Adresa URL pro odpověď** zadejte adresu URL, která používá následující vzorec:`https://<subdomain>.jamfcloud.com/saml/SSO`

1. Vyberte **Nastavit další adresy URL**. Pokud chcete aplikaci nakonfigurovat v režimu **iniciovaném službou SP,** zadejte do textového pole **Přihlašovací adresa URL** adresu URL adresu URL, která používá následující vzorec:`https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Skutečnou hodnotu identifikátoru získáte z oddílu **Jednotné přihlášení na** portálu Jamf Pro, což je vysvětleno dále v kurzu. Skutečnou hodnotu subdomény můžete extrahovat z hodnoty identifikátoru a použít tyto informace o subdoméně jako přihlašovací adresu URL a adresu URL odpovědi. Můžete také odkazovat na vzorce uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

1. Na stránce **Nastavit jednotné přihlašování pomocí saml** přejděte do části **Podpisový certifikát SAML,** vyberte tlačítko **kopírovat,** chcete-li zkopírovat **adresu URL metadat federace aplikací**, a pak ji uložte do počítače.

    ![Odkaz ke stažení podpisového certifikátu SAML](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele na webu Azure s názvem B.Simon.

1. V levém podokně na webu Azure Portal vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel.**
1. Ve vlastnostech **Uživatele** postupujte takto:
   1. Do pole **Název** zadejte `B.Simon`.
   1. Do pole **Uživatelské jméno** zadejte [name]@[companydomain]. [rozšíření]. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli **Heslo.**
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části udělíte B.Simonovi přístup k Jamf Pro.

1. Na portálu Azure vyberte **Podnikové aplikace**a pak vyberte **Všechny aplikace**.
1. V seznamu aplikací vyberte **Jamf Pro**.
1. Na stránce s přehledem aplikace najděte část **Spravovat** a vyberte **Uživatelé a skupiny**.

   ![Vyberte Uživatelé a skupiny.](common/users-groups-blade.png)

1. V dialogovém okně **Přidat přiřazení** vyberte **Přidat uživatele**a pak vyberte **Uživatelé a skupiny.**

    ![Vyberte tlačítko Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu Uživatelé **B.Simon** a v dolní části obrazovky vyberte tlačítko **Vybrat.**
1. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele. Potom vyberte tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** vyberte tlačítko **Přiřadit.**

## <a name="configure-sso-in-jamf-pro"></a>Konfigurace automatického přihlašovat v Jamf Pro

1. Chcete-li automatizovat konfiguraci v rámci programu Jamf Pro, nainstalujte **rozšíření prohlížeče Bezpečné přihlášení aplikace** pomocí **možnosti Nainstalovat rozšíření**.

    ![Stránka rozšíření prohlížeče Zabezpečené aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče vyberte **Nastavit Jamf Pro**. Po otevření aplikace Jamf Pro zadejte přihlašovací údaje správce k přihlášení. Rozšíření prohlížeče automaticky nakonfiguruje aplikaci a automatizuje kroky 3 až 7.

    ![Instalační konfigurační stránka v Jamf Pro](common/setup-sso.png)

3. Chcete-li Jamf Pro nastavit ručně, otevřete nové okno webového prohlížeče a přihlaste se na firemní web Jamf Pro jako správce. Potom proveďte následující kroky.

4. V pravém horním rohu stránky vyberte **ikonu Nastavení.**

    ![Vyberte ikonu nastavení v Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Vyberte **možnost Jednotné přihlašování**.

    ![Vybrat jednotné přihlašování v Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Na stránce **jednotného přihlášení** postupujte takto.

    ![Stránka jednotného přihlášení v Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Vyberte **Upravit**.

    b. Zaškrtněte políčko **Povolit jednotné ověřování přihlášením.**

  c. V rozevírací nabídce **Zprostředkovatel identity** vyberte **Azure** jako možnost.

  d. Zkopírujte hodnotu **ID entity** a vložte ji do pole **Identifikátor (ID entity)** v části **Základní konfigurace SAML** na webu Azure Portal.

> [!NOTE]
> Pomocí hodnoty v `<SUBDOMAIN>` poli dokončete přihlašovací adresu URL a adresu URL odpovědi v části **Základní konfigurace SAML** na webu Azure Portal.

  e. V rozevírací nabídce Zdroj **metadat zprostředkovatele identity** vyberte **url metadat.** Do pole, které se zobrazí, vložte hodnotu **url metadat federace aplikace,** kterou jste zkopírovali z webu Azure Portal.

  f. (Nepovinné) Upravte hodnotu vypršení platnosti tokenu nebo vyberte "Zakázat vypršení platnosti tokenu SAML".

7. Na stejné stránce přejděte dolů do části **Mapování uživatelů.** Potom proveďte následující kroky.

    ![Oddíl Mapování uživatelů na stránce jednotného přihlášení v Jamf Pro.](./media/jamfprosamlconnector-tutorial/tutorial-jamfprosamlconnector-single.png)

    a. Vyberte možnost **NameID** pro **mapování uživatelů zprostředkovatele identity**. Ve výchozím nastavení je tato možnost nastavena na **NameID**, ale můžete definovat vlastní atribut.

    b. Vyberte **možnost E-mail** pro **mapování uživatelů Jamf Pro**. Jamf Pro mapuje atributy SAML odeslané idp nejprve uživateli a poté skupinami. Když se uživatel pokusí o přístup k Jamf Pro, Jamf Pro získá informace o uživateli od poskytovatele identity a porovná je se všemi uživatelskými účty Jamf Pro. Pokud příchozí uživatelský účet nebyl nalezen, jamf Pro se pokusí porovnat jej podle názvu skupiny.

    c. Vložte `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` hodnotu do pole **NÁZEV ATRIBUTU SKUPINY IDENTITY PROVIDER.**

    d. Na stejné stránce přejděte dolů do části **Zabezpečení** a vyberte **Povolit uživatelům obejít ověřování jednotného přihlašování**. V důsledku toho nebudou uživatelé přesměrováni na přihlašovací stránku zprostředkovatele identity pro ověření a místo toho se mohou přihlásit přímo k Jamf Pro. Když se uživatel pokusí o přístup k Jamf Pro prostřednictvím zprostředkovatele identity, dojde k ověřování a autorizaci iniciovaného zprostředkovatelem identity.

    e. Vyberte **Uložit**.

### <a name="create-a-jamf-pro-test-user"></a>Vytvoření testovacího uživatele Jamf Pro

Aby se uživatelé Azure AD mohli přihlásit k Jamf Pro, musí být zřídit Jamf Pro. Zřizování v Jamf Pro je ruční úloha.

Chcete-li zřídit uživatelský účet, postupujte takto:

1. Přihlaste se na firemní web Jamf Pro jako správce.

2. V pravém horním rohu stránky vyberte ikonu **Nastavení.**

    ![Ikona nastavení v Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Vyberte **možnost Uživatelské účty Jamf Pro & skupiny**.

    ![Ikona Uživatelských účtů Jamf Pro & skupiny v nastavení Jamf Pro](./media/jamfprosamlconnector-tutorial/user1.png)

4. Vyberte **nový**.

    ![Stránka Nastavení systému ProPro Uživatelské účty & Skupiny](./media/jamfprosamlconnector-tutorial/user2.png)

5. Vyberte **Vytvořit standardní účet**.

    ![Možnost Vytvořit standardní účet na stránce Uživatelské účty Jamf Pro & skupiny](./media/jamfprosamlconnector-tutorial/user3.png)

6. V dialogovém okně **Nový účet** proveďte následující kroky:

    ![Nové možnosti nastavení účtu v nastavení systému Jamf Pro](./media/jamfprosamlconnector-tutorial/user4.png)

    a. Do pole **USERNAME** `Britta Simon`zadejte celé jméno testovacího uživatele.

    b. Vyberte možnosti **pro ÚROVEŇ PŘÍSTUPU**, **SADU OPRÁVNĚNÍ**a **STATUS PŘÍSTUPU,** které jsou v souladu s vaší organizací.

    c. Do pole **CELÝ** NÁZEV `Britta Simon`zadejte .

    d. Do pole **E-MAILOVÁ ADRESA** zadejte e-mailovou adresu účtu Britty Simonové.

    e. Do pole **HESLO** zadejte heslo uživatele.

    f. Do pole **OVĚŘIT HESLO** zadejte heslo uživatele znovu.

    g. Vyberte **Uložit**.

## <a name="test-the-sso-configuration"></a>Testování konfigurace s připomene

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když na přístupovém panelu vyberete dlaždici Jamf Pro, měli byste být automaticky přihlášeni k účtu Jamf Pro, pro který jste nakonfigurovali automatické přihlašující. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Kurzy pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jednotné přihlašování k aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Vyzkoušejte Jamf Pro s Azure AD](https://aad.portal.azure.com/)