---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) pomocí řídicího panelu Meraki | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a řídicím panelem Meraki.
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
ms.openlocfilehash: 2bedcaa414328f9d876b212ff931acfb193f51ca
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517119"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-meraki-dashboard"></a>Kurz: Azure Active Directory integraci jednotného přihlašování pomocí řídicího panelu Meraki

V tomto kurzu se naučíte integrovat řídicí panel Meraki s Azure Active Directory (Azure AD). Když integrujete řídicí panel Meraki s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k řídicímu panelu Meraki
* Umožněte uživatelům, aby se automaticky přihlásili k řídicímu panelu Meraki pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Odběr povoleného jednotného přihlašování (SSO) řídicího panelu Meraki

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Řídicí panel Meraki podporuje **IDP** iniciované jednotné přihlašování.
* Po nakonfigurování řídicího panelu Meraki můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-meraki-dashboard-from-the-gallery"></a>Přidání řídicího panelu Meraki z Galerie

Ke konfiguraci integrace řídicího panelu Meraki do služby Azure AD je nutné přidat řídicí panel Meraki z Galerie do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Meraki Dashboard** .
1. Vyberte **řídicí panel Meraki** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-meraki-dashboard"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro řídicí panel Meraki

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí řídicího panelu Meraki pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v řídicím panelu Meraki.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí řídicího panelu Meraki, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurovat jednotné přihlašování řídicího panelu Meraki](#configure-meraki-dashboard-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte testovacího uživatele řídicího panelu Meraki](#create-meraki-dashboard-test-user)** , abyste měli protějšek B. Simon v řídicím panelu Meraki, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **řídicího panelu Meraki** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** proveďte následující kroky:
     
    Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:  `https://n27.meraki.com/saml/login/m9ZEgb/< UNIQUE ID >`

    > [!NOTE]
    > Hodnota adresy URL odpovědi není reálné číslo. Aktualizujte tuto hodnotu skutečnou hodnotou adresy URL odpovědi, která je vysvětlena dále v tomto kurzu.

1. Klikněte na tlačítko **Uložit**.

1. Aplikace řídicího panelu Meraki očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě výše očekává aplikace řídicího panelu Meraki několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.
    
    | Name | Zdrojový atribut|
    | ---------------| --------- |
    | `https://dashboard.meraki.com/saml/attributes/username` | User. userPrincipalName |
    | `https://dashboard.meraki.com/saml/attributes/role` | User. assignedroles |

    > [!NOTE]
    > Informace o tom, jak nakonfigurovat role v Azure AD, najdete [tady](../develop/active-directory-enterprise-app-role-management.md).

1. V části **podpisový certifikát SAML** kliknutím na tlačítko **Upravit** otevřete dialogové okno **podpisový certifikát SAML** .

    ![Upravit podpisový certifikát SAML](common/edit-certificate.png)

1. V části **podpisový certifikát SAML** zkopírujte **hodnotu kryptografického otisku** a uložte ji do svého počítače.

    ![Kopírovat hodnotu kryptografického otisku](common/copy-thumbprint.png)

1. V části **Nastavení řídicího panelu Meraki** ZKOPÍRUJTE hodnotu URL pro odhlášení a uložte ji do svého počítače.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k řídicímu panelu Meraki.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **řídicí panel Meraki**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-meraki-dashboard-sso"></a>Konfigurace jednotného přihlašování k řídicímu panelu Meraki

1. V jiném okně webového prohlížeče se přihlaste k řídicímu panelu Meraki jako správce.

1. Přejděte do **Organization**  ->  **Nastavení**organizace.

    ![Karta Nastavení řídicího panelu Meraki](./media/meraki-dashboard-tutorial/configure1.png)

1. V části ověřování změňte **jednotné přihlašování SAML** na **SSO povoleno jednotného přihlašování SAML**.

    ![Ověřování řídicího panelu Meraki](./media/meraki-dashboard-tutorial/configure2.png)

1. Klikněte na **Přidat IDP SAML**.

    ![Řídicí panel Meraki přidat IdP SAML](./media/meraki-dashboard-tutorial/configure3.png)

1. Vložte hodnotu **kryptografického otisku** , kterou jste zkopírovali z Azure Portal do textového pole **otisku certifikátu SHA1 pro certifikát X. 590** . Potom klikněte na **Uložit**. Po uložení se adresa URL příjemce zobrazí. Zkopírujte hodnotu adresy URL příjemce a vložte ji do textového pole **Adresa URL odpovědi** v **části základní konfigurace SAML** v Azure Portal.

    ![Konfigurace řídicího panelu Meraki](./media/meraki-dashboard-tutorial/configure4.png)

### <a name="create-meraki-dashboard-test-user"></a>Vytvořit testovacího uživatele řídicího panelu Meraki

1. V jiném okně webového prohlížeče se přihlaste k řídicímu panelu Meraki jako správce.

1. Přejděte ke **Organization**  ->  **Správci**organizace.

    ![Správci řídicích panelů Meraki](./media/meraki-dashboard-tutorial/user1.png)

1. V části role správce SAML klikněte na tlačítko **Přidat roli SAML** .

    ![Tlačítko pro přidání role SAML v řídicím panelu Meraki](./media/meraki-dashboard-tutorial/user2.png)

1. Zadejte **Meraki_full_admin**rolí, označte **přístup organizace** jako **úplný** a klikněte na **vytvořit roli**. Opakujte tento postup pro **meraki_readonly_admin**, tentokrát označí **přístup organizace** jako pole jen **pro čtení** .
 
    ![Řídicí panel meraki vytvořit uživatele](./media/meraki-dashboard-tutorial/user3.png)

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici řídicího panelu Meraki, měli byste být automaticky přihlášeni k řídicímu panelu Meraki, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si řídicí panel Meraki s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)