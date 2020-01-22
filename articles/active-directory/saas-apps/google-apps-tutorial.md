---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s G Suite | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8a1736092578634680da5d56b5ec02f70cdde38
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289845"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-g-suite"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s G Suite

V tomto kurzu se dozvíte, jak integrovat G Suite s Azure Active Directory (Azure AD). Když integrujete G Suite s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k G Suite.
* Umožněte uživatelům, aby se automaticky přihlásili k G Suite pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

- Předplatné služby Azure AD.
- Předplatné G Suite s podporou jednotného přihlašování (SSO).
- Předplatné Google Apps nebo předplatné Google Cloud Platform.

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí. Tento dokument byl vytvořen pomocí jednotného přihlašování uživatele. Pokud stále používáte starou, bude instalace vypadat jinak. Nové prostředí můžete povolit v nastavení jednotného přihlašování aplikace G-Suite. Přejděte na **Azure AD, podnikové aplikace**, vyberte **G Suite**, vyberte **jednotné přihlašování** a potom klikněte na **vyzkoušet nové prostředí**.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

1. **Otázka: Tato integrace podporuje Google Cloud Platform integraci jednotného přihlašování se službou Azure AD?**

    Odpověď: Ano. Aplikace Google Cloud Platform a Google sdílí stejnou platformu ověřování. Proto je třeba provést integraci GCP, abyste mohli nakonfigurovat jednotné přihlašování s aplikacemi Google.

2. **Otázka: jsou Chromebooks a jiná zařízení Chrome kompatibilní s jednotným přihlašováním Azure AD?**
  
    Odpověď: Ano, uživatelé se mohou přihlašovat ke svým zařízením Chromebook pomocí svých přihlašovacích údajů Azure AD. Informace o tom, proč se uživatelům může zobrazit výzva k zadání přihlašovacích údajů dvakrát, najdete v tomto [článku podpory G Suite](https://support.google.com/chrome/a/answer/6060880) .

3. **Otázka: Pokud povolíte jednotné přihlašování, budou uživatelé moci používat svoje přihlašovací údaje Azure AD k tomu, aby se mohli přihlásit k libovolnému produktu Google, například Google Classroom, GMail, disk Google, YouTube atd.?**

    Odpověď: Ano, v závislosti na [tom, kterou sadu G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) jste zvolili pro povolení nebo zakázání pro vaši organizaci.

4. **Otázka: můžu povolit jednotné přihlašování jenom pro podmnožinu svých uživatelů G Suite?**

    Odpověď: Ne, zapnutí jednotného přihlašování okamžitě vyžaduje, aby všichni uživatelé sady G Suite ověřili své přihlašovací údaje služby Azure AD. Vzhledem k tomu, že G Suite nepodporuje více zprostředkovatelů identity, může být poskytovatel identity pro prostředí G Suite buď Azure AD, nebo Google, ale ne současně současně.

5. **Otázka: Pokud je uživatel přihlášený prostřednictvím Windows, automaticky se ověří v G Suite, aniž by se zobrazila výzva k zadání hesla?**

    Odpověď: Existují dvě možnosti pro povolení tohoto scénáře. Nejdřív se uživatelé mohli do zařízení s Windows 10 přihlásit pomocí [Azure Active Directory JOIN](../device-management-introduction.md). Další možností je, že se uživatelé můžou přihlašovat do zařízení s Windows, která jsou připojená k doméně, do místní služby Active Directory, u které se povolilo jednotné přihlašování ke službě Azure AD prostřednictvím nasazení [Active Directory Federation Services (AD FS) (AD FS)](../hybrid/plan-connect-user-signin.md) . Obě možnosti vyžadují, abyste provedli kroky v následujícím kurzu a povolili jste jednotné přihlašování mezi Azure AD a G Suite.

6. **Otázka: co mám dělat, když se zobrazí chybová zpráva "Neplatný e-mail"?**

    Odpověď: pro tuto instalaci je vyžadován atribut e-mail, aby se uživatelé mohli přihlásit. Tento atribut nelze nastavit ručně.

    Atribut e-mail je automaticky vyplněný pro každého uživatele s platnou licencí Exchange. Pokud uživatel není povolený e-mailem, zobrazí se tato chyba, protože aplikace potřebuje získat přístup k tomuto atributu.

    Můžete přejít na portal.office.com s účtem správce, potom kliknout na centrum pro správu, fakturace, předplatná, vybrat předplatné Office 365 a potom kliknout na přiřadit k uživatelům, vybrat uživatele, u kterých chcete ověřit předplatné, a v pravém podokně kliknout na Upravte licence.

    Po přiřazení licence O365 může trvat několik minut, než se použije. Potom bude automaticky vyplněn atribut User. mail, který by měl vyřešit problém.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* G Suite podporuje jednotné přihlašování (SSO) iniciované **SP**

* G Suite podporuje [ **automatizované** zřizování uživatelů.](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)
* Jakmile nakonfigurujete G Suite, můžete vyhovět ovládacím prvkům relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Ovládací prvky relace přesahují podmíněný přístup. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-g-suite-from-the-gallery"></a>Přidání G Suite z Galerie

Pokud chcete nakonfigurovat integraci G Suite do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat G Suite z galerie.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **G Suite** .
1. Vyberte **G Suite** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-g-suite"></a>Konfigurace a testování jednotného přihlašování Azure AD pro G Suite

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí G Suite pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte v G Suite vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí G Suite, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování G Suite](#configure-g-suite-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte testovacího uživatele G Suite](#create-g-suite-test-user)** , abyste měli protějšek B. Simon v g Suite, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace s aplikacemi **G Suite** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurační oddíl SAML** , pokud chcete nakonfigurovat pro **Gmail** , proveďte následující kroky:

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru:

    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

1. Pokud chcete pro **Google Cloud Platform** nakonfigurovat **základní konfigurační oddíl SAML** , proveďte následující kroky:

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |
    
    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL a identifikátoru. G Suite při konfiguraci jednotného přihlašování neposkytuje hodnotu ID nebo identifikátoru entity, takže když zrušíte kontrolu pro možnost **vystavitele specifické pro doménu** , hodnota identifikátoru se `google.com`. Pokud zaškrtnete možnost **vystavitele specifické pro doménu** , bude `google.com/a/<yourdomainname.com>`. Pokud chcete zaškrtnout/zrušit kontrolu pro **vystavitele specifické pro doménu** , musíte přejít do oddílu **Konfigurace G Suite jednotného přihlašování** , které se vysvětluje později v tomto kurzu. Další informace získáte od [týmu podpory pro klienty pro sadu G Suite](https://www.google.com/contact/).

1. Vaše aplikace G Suite očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Příklad ukazuje následující snímek obrazovky. Výchozí hodnotou **jedinečného identifikátoru uživatele** je **User. userPrincipalName,** ale G Suite očekává, že tato hodnota bude namapována pomocí e-mailové adresy uživatele. Pro tuto funkci můžete použít atribut **User. mail** ze seznamu nebo použít odpovídající hodnotu atributu na základě konfigurace vaší organizace.

    ![image](common/default-attributes.png)


1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení G Suite** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to G Suite.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. In the applications list, select **G Suite**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-g-suite-sso"></a>Configure G Suite SSO

1. Open a new tab in your browser, and sign into the [G Suite Admin Console](https://admin.google.com/) using your administrator account.

2. Klikněte na tlačítko **zabezpečení**. If you don't see the link, it may be hidden under the **More Controls** menu at the bottom of the screen.

    ![Klikněte na Zabezpečení.][10]

3. On the **Security** page, click **Set up single sign-on (SSO).**

    ![Click SSO.][11]

4. Perform the following configuration changes:

    ![Configure SSO][12]

    a. Select **Setup SSO with third-party identity provider**.

    b. In the **Sign-in page URL** field in G Suite, paste the value of **Login URL** which you have copied from Azure portal.

    c. In the **Sign-out page URL** field in G Suite, paste the value of **Logout URL** which you have copied from Azure portal.

    d. In the **Change password URL** field in G Suite, paste the value of **Change password URL** which you have copied from Azure portal.

    e. In G Suite, for the **Verification certificate**, upload the certificate that you have downloaded from Azure portal.

    f. Check/Uncheck the **Use a domain specific issuer** option as per the note mentioned in the above **Basic SAML Configuration** section in the Azure AD.

    g. Klikněte na tlačítko **uložit změny**.

### <a name="create-g-suite-test-user"></a>Create G Suite test user

The objective of this section is to [create a user in G Suite](https://support.google.com/a/answer/33310?hl=en) called B.Simon. After the user has manually been created in G Suite, the user will now be able to sign in using their Office 365 login credentials.

G Suite also supports automatic user provisioning. To configure automatic user provisioning, you must first [configure G Suite for automatic user provisioning](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial).

> [!NOTE]
> Make sure that your user already exists in G Suite if provisioning in Azure AD has not been turned on before testing Single Sign-on.

> [!NOTE]
> If you need to create a user manually, contact the [Google support team](https://www.google.com/contact/).

## <a name="test-sso"></a>Test SSO 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

When you click the G Suite tile in the Access Panel, you should be automatically signed in to the G Suite for which you set up SSO. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Configure User Provisioning](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)
- [Try G Suite with Azure AD](https://aad.portal.azure.com/)
- [What is session control in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/protect-gsuite)

- [How to protect G Suite with advanced visibility and controls](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png
