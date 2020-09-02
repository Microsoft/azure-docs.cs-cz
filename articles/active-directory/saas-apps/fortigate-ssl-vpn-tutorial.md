---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s protokolem SSL VPN v FortiGate | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a FortiGate SSL VPN.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecb53d661b1171f9c1b18d37d0bb35952645ba7e
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299673"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s protokolem SSL VPN FortiGate

V tomto kurzu se dozvíte, jak integrovat FortiGate SSL VPN s Azure Active Directory (Azure AD). Když integrujete FortiGate SSL VPN s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k FortiGate SSL VPN.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k FortiGate SSL VPN pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* FortiGate SSL VPN s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* FortiGate SSL VPN podporuje jednotné přihlašování (SSO) iniciované **SP**
* Po nakonfigurování FortiGate SSL VPN můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-fortigate-ssl-vpn-from-the-gallery"></a>Přidání FortiGate SSL VPN z Galerie

Pokud chcete nakonfigurovat integraci FortiGate SSL VPN do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat FortiGate SSL VPN z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Fortigate SSL VPN** .
1. Z panelu výsledků vyberte **FORTIGATE SSL VPN** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro FortiGate SSL VPN

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s FortiGate SSL VPN pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v FortiGate SSL VPN.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s FortiGate SSL VPN, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte FORTIGATE SSL VPN SSO](#configure-fortigate-ssl-vpn-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **Vytvořte testovacího uživatele FORTIGATE SSL VPN** – Pokud chcete mít protějšek B. Simon v FORTIGATE SSL VPN, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-single-sign-on)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **VPN Fortigate SSL** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<FQDN>/remote/login`

    b. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<FQDN>/remote/saml/metadata`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://><FQDN/remote/saml/login`

    d. Do textového pole **odhlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<FQDN>/remote/saml/logout`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí vlastního přihlašovací adresy URL, identifikátoru, adresy URL odpovědi a adresy URL pro odhlášení. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory FORTIGATE SSL VPN Client](mailto:tac_amer@fortinet.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aplikace FortiGate SSL VPN očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace FortiGate SSL VPN v odpovědi SAML několik atributů, které jsou uvedené dál. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.
    
    | Název |  Zdrojový atribut|
    | ------------ | --------- |
    | username | User. userPrincipalName |
    | group | User. Groups |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **set FORTIGATE SSL VPN** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k FortiGate SSL VPN.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **FORTIGATE SSL VPN**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-a-security-group-for-the-test-user"></a>Vytvořit skupinu zabezpečení pro testovacího uživatele

V této části vytvoříte skupinu zabezpečení v Azure Active Directory pro testovacího uživatele. Tuto skupinu zabezpečení bude používat FortiGate k udělení přístupu k síti uživatele prostřednictvím sítě VPN.

1. V levém podokně Azure Portal vyberte **Azure Active Directory**a pak vyberte **skupiny**.
1. V horní části obrazovky vyberte **Nová skupina** .
1. V okně vlastnosti **nové skupiny** postupujte takto:
   1. V poli **typ skupiny** vyberte **zabezpečení**.
   1. Do pole **Název** zadejte `FortiGateAccess`.
   1. Do pole **Popis skupiny** zadejte `Group for granting FortiGate VPN access` .
   1. Pro **role Azure AD se dají přiřadit nastavení skupiny (Preview)** a vybrat **ne**.
   1. V poli **typ členství** vyberte **přiřazeno**.
   1. V části **Členové**vyberte **žádné vybrané členy**.
   1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
   1. Vyberte **Create** (Vytvořit).
1. Až se vrátíte do okna **skupiny** v Azure Active Directory, vyhledejte skupinu **přístupu Fortigate** a poznamenejte si **ID objektu** pro pozdější použití.

## <a name="configure-fortigate-ssl-vpn-sso"></a>Konfigurace FortiGate SSL VPN SSO

### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>Nahrajte na zařízení FortiGate certifikát SAML base64.

Po dokončení konfigurace SAML aplikace FortiGate ve vašem tenantovi jste stáhli certifikát SAML kódovaný v kódování Base64. Musí se nahrát do zařízení FortiGate:

1. Přihlaste se na portál pro správu zařízení FortiGate.
1. V nabídce na levé straně klikněte na **systém**.
1. V části **systém**klikněte na **certifikáty**.
1. Klikněte na **importovat**  ->  **vzdálený certifikát**.
1. Přejděte na certifikát stažený z nasazení aplikace FortiGate v tenantovi Azure, vyberte ho a klikněte na **OK** .

Po nahrání certifikátu si poznamenejte jeho název pod **System**  >  **Certificates**  >  **vzdáleným certifikátem**systémové certifikáty. Ve výchozím nastavení bude pojmenován REMOTE_Cert_**n** , kde **N** je celočíselná hodnota.

### <a name="perform-fortigate-command-line-configuration"></a>Provést konfiguraci příkazového řádku FortiGate

Následující kroky vyžadují, aby se nakonfigurovala adresa URL pro odhlášení Azure. Tato adresa URL obsahuje otazník (?). K úspěšnému odeslání tohoto znaku se vyžadují speciální kroky. Tyto kroky nelze provést z konzoly CLI FortiGate. Místo toho vytvořte relaci SSH k FortiGate applicance pomocí nástroje, jako je například výstup. Pokud je vaše zařízení FortiGate virtuálním počítačem Azure, můžete provést následující kroky z konzoly sériového portu virtuálních počítačů Azure.

K provedení těchto kroků budete potřebovat hodnoty zaznamenané dříve:

- ID entity
- Adresa URL odpovědi
- Odhlašovací adresa URL
- Přihlašovací adresa URL Azure
- Identifikátor Azure AD
- Adresa URL pro odhlášení Azure
- Base64 – název certifikátu SAML (REMOTE_Cert_N)

1. Vytvořte relaci SSH k FortiGate applicance a přihlaste se pomocí účtu správce FortiGate.
1. Proveďte následující příkazy:

   ```console
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert <Base64 SAML Certificate Name>
    set user-name username
    set group-name group
    end

   ```

   > [!NOTE]
   > **Adresa URL pro odhlášení Azure** obsahuje `?` znak. Je nutné zadat speciální posloupnost kláves pro správné zadání adresy URL ke konzole FortiGate Serial. Adresa URL je obvykle `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` .
   >
   > Pokud chcete zadat adresu URL pro odhlášení Azure v konzole sériového portu, zadejte `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation` .
   > 
   > Pak vyberte CTRL + V a vložte zbytek adresy URL, aby se řádek dokončí: `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` .

### <a name="configure-fortigate-for-group-matching"></a>Konfigurace FortiGate pro spárování skupin

V této části nakonfigurujete FortiGate pro rozpoznání ID objektu skupiny zabezpečení, ve které se nachází testovací uživatel. To umožní FortiGate rozhodnutím o přístupu na základě tohoto členství ve skupině.

K provedení těchto kroků budete potřebovat ID objektu **FortiGateAccess** skupiny zabezpečení, kterou jste vytvořili dříve.

1. Vytvořte relaci SSH pro FortiGate Applicance a přihlaste se pomocí účtu správce FortiGate.
1. Proveďte následující příkazy:

   ```
    config user group
    edit FortiGateAccess
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <Object Id>
    next
    end
    next
    end
   ```

### <a name="create-fortigate-vpn-portals-and-firewall-policy"></a>Vytváření portálů a zásad brány firewall pro FortiGate VPN

V této části nakonfigurujete portály sítě VPN FortiGate a zásady brány firewall, které udělují přístup skupině zabezpečení, **FortiGateAccess** vytvořené výše.

Spolupracujte s [týmem podpory Fortigate](mailto:tac_amer@fortinet.com) , abyste přidali portály VPN a zásady brány firewall na FORTIGATE platformu VPN. Tyto kroky je nutné provést před použitím jednotného přihlašování.

## <a name="test-single-sign-on"></a>Testování jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici FortiGate SSL VPN na přístupovém panelu, měli byste se automaticky přihlásit k síti VPN FortiGate SSL, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Microsoft a FortiGate doporučuje, abyste používali klienta VPN Fortinet, FortiClient pro optimální činnost koncového uživatele.

## <a name="additional-resources"></a>Další zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte FortiGate SSL VPN s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
