---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s protokolem SSL VPN v FortiGate | Microsoft Docs'
description: Seznamte se s kroky, které je třeba provést při integraci FortiGate SSL VPN s Azure Active Directory (Azure AD).
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
ms.openlocfilehash: 187903bfbf75ada45b9a539acd1157dfe730747a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91331110"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s protokolem SSL VPN FortiGate

V tomto kurzu se dozvíte, jak integrovat FortiGate SSL VPN s Azure Active Directory (Azure AD). Když integrujete FortiGate SSL VPN s Azure AD, můžete:

* Pomocí Azure AD můžete řídit, kdo má přístup k FortiGate SSL VPN.
* Umožněte uživatelům, aby se automaticky přihlásili k FortiGate SSL VPN pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné VPN FortiGate SSL s povoleným jednotným přihlašováním (SSO).

## <a name="tutorial-description"></a>Popis kurzu

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

FortiGate SSL VPN podporuje jednotné přihlašování iniciované pomocí SP.

Po nakonfigurování FortiGate SSL VPN můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-fortigate-ssl-vpn-from-the-gallery"></a>Přidání FortiGate SSL VPN z Galerie

Pokud chcete nakonfigurovat integraci FortiGate SSL VPN do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat FortiGate SSL VPN z Galerie:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo pomocí osobního účet Microsoft.
1. V levém podokně vyberte **Azure Active Directory**.
1. Přejít na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Fortigate SSL VPN** .
1. Na panelu výsledků vyberte **FORTIGATE SSL VPN** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro FortiGate SSL VPN

Nakonfigurujete a otestujete jednotné přihlašování Azure AD s FortiGate SSL VPN pomocí testovacího uživatele s názvem B. Simon. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a odpovídajícím uživatelem v FortiGate SSL VPN.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s FortiGate SSL VPN, dokončete tyto kroky vysoké úrovně:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , abyste funkci povolili uživatelům.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD.
    1. **[Udělte testovacímu uživateli přístup](#grant-access-to-the-test-user)** pro povolení jednotného přihlašování Azure AD pro tohoto uživatele.
1. **[NAKONFIGURUJTE jednotné přihlašování FORTIGATE SSL VPN](#configure-fortigate-ssl-vpn-sso)** na straně aplikace.
    1. **Vytvořte uživatele FORTIGATE SSL VPN test** jako protějšek pro reprezentaci uživatele v Azure AD.
1. **[Otestujte jednotné přihlašování](#test-single-sign-on)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **VPN Fortigate SSL** v části **Spravovat** vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyberte tlačítko tužky pro **základní konfiguraci SAML** a upravte nastavení:

   ![Snímek obrazovky, který zobrazuje tlačítko tužky pro úpravu základní konfigurace SAML.](common/edit-urls.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** zadejte následující hodnoty:

    a. Do pole **přihlašovací adresa URL** zadejte adresu URL ve vzoru `https://<FQDN>/remote/login` .

    b. Do pole **identifikátor** zadejte adresu URL ve vzoru `https://<FQDN>/remote/saml/metadata` .

    c. Do pole **Adresa URL odpovědi** zadejte adresu URL ve vzoru `https://<FQDN>/remote/saml/login` .

    d. Do pole **Adresa URL pro odhlášení** zadejte adresu URL ve vzoru `https://<FQDN>/remote/saml/logout` .

    > [!NOTE]
    > Tyto hodnoty jsou jenom vzory. Musíte použít vlastní **přihlašovací adresu URL**, **identifikátor**, **adresu URL odpovědi**a **adresu URL pro odhlášení**. Pokud chcete získat skutečné hodnoty, obraťte se na [tým podpory FORTIGATE SSL VPN Client](mailto:tac_amer@fortinet.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aplikace FortiGate SSL VPN očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![Snímek obrazovky, který zobrazuje výchozí atributy.](common/default-attributes.png)

1. Další dvě deklarace identity FortiGate SSL VPN jsou uvedené v následující tabulce. Názvy těchto deklarací se musí shodovat s názvy použitými v **konfiguračním oddílu provést Fortigate** v tomto kurzu. 

   | Name |  Zdrojový atribut|
   | ------------ | --------- |
   | username | User. userPrincipalName |
   | group | User. Groups |
   
   Vytvoření těchto dalších deklarací identity:
   
   1. Vedle **atributů uživatele & deklarace identity**vyberte **Upravit**.
   1. Vyberte **Přidat novou deklaraci identity**.
   1. Jako **název**zadejte **uživatelské jméno**.
   1. V případě **zdrojového atributu**vyberte **User. userPrincipalName**.
   1. Vyberte **Uložit**.
   1. Vyberte **přidat deklaraci skupiny**.
   1. Vyberte **Všechny skupiny**.
   1. Seect **upravte název pole deklarace skupiny** .
   1. Jako **název**zadejte **Skupina**.
   1. Vyberte **Uložit**.   

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyberte odkaz **ke stažení** vedle **certifikátu (Base64)** a Stáhněte certifikát a uložte ho do počítače:

    ![Snímek obrazovky, který zobrazuje odkaz na stažení certifikátu.](common/certificatebase64.png)

1. V části **set FORTIGATE SSL VPN** zkopírujte příslušnou adresu URL nebo adresy URL na základě vašich požadavků:

    ![Snímek obrazovky se zobrazenými adresami URL konfigurace](common/copy-configuration-urls.png)

#### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem B. Simon ve Azure Portal.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**. Vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. V části vlastnosti **uživatele** proveďte tyto kroky:
   1. Do pole **název** zadejte **B. Simon**.  
   1. Do pole **uživatelské jméno** zadejte \<username> @ \<companydomain> . \<extension> . Například, `B.Simon@contoso.com`.
   1. Vyberte možnost **Zobrazit heslo**a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Vyberte **Vytvořit**.

#### <a name="grant-access-to-the-test-user"></a>Udělení přístupu testovacímu uživateli

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte tomuto uživateli přístup k FortiGate SSL VPN.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **FORTIGATE SSL VPN**.
1. Na stránce Přehled aplikace v části **Spravovat** vyberte **Uživatelé a skupiny**:

   ![Snímek obrazovky, který zobrazuje možnost Uživatelé a skupiny.](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** :

    ![Snímek obrazovky zobrazující tlačítko Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

#### <a name="create-a-security-group-for-the-test-user"></a>Vytvořit skupinu zabezpečení pro testovacího uživatele

V této části vytvoříte skupinu zabezpečení v Azure Active Directory pro testovacího uživatele. FortiGate bude tuto skupinu zabezpečení používat k udělení přístupu uživatele k síti prostřednictvím sítě VPN.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**. Pak vyberte **skupiny**.
1. V horní části obrazovky vyberte **Nová skupina** .
1. V okně vlastnosti **nové skupiny** proveďte tyto kroky:
   1. V seznamu **typ skupiny** vyberte **zabezpečení**.
   1. Do pole **název skupiny** zadejte **FortiGateAccess**.
   1. Do pole **Popis skupiny** zadejte **skupina pro udělení přístupu Fortigate VPN**.
   1. Pro **role Azure AD se dají přiřadit nastavení skupiny (Preview)** a vybrat **ne**.
   1. V poli **typ členství** vyberte **přiřazeno**.
   1. V části **Členové**vyberte **žádné vybrané členy**.
   1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
   1. Vyberte **Vytvořit**.
1. Až se vrátíte do části **skupiny** v Azure Active Directory, najděte skupinu **přístupu Fortigate** a poznamenejte si **ID objektu**. Budete ho potřebovat později.

### <a name="configure-fortigate-ssl-vpn-sso"></a>Konfigurace FortiGate SSL VPN SSO

#### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>Nahrajte na zařízení FortiGate certifikát SAML base64.

Po dokončení konfigurace SAML aplikace FortiGate ve vašem tenantovi jste stáhli certifikát SAML kódovaný v kódování Base64. Tento certifikát je potřeba nahrát do zařízení FortiGate:

1. Přihlaste se na portál pro správu zařízení FortiGate.
1. V levém podokně vyberte možnost **systém**.
1. V části **systém**vyberte **certifikáty**.
1. Vyberte **importovat**  >  **vzdálený certifikát**.
1. V tenantovi Azure přejděte na certifikát stažený z nasazení aplikace FortiGate, vyberte ho a pak vyberte **OK**.

Po nahrání certifikátu si poznamenejte jeho název pod **System**  >  **Certificates**  >  **vzdáleným certifikátem**systémové certifikáty. Ve výchozím nastavení bude pojmenován REMOTE_Cert_*N*, kde *N* je celočíselná hodnota.

#### <a name="complete-fortigate-command-line-configuration"></a>Dokončit konfiguraci příkazového řádku FortiGate

Následující kroky vyžadují, abyste nakonfigurovali adresu URL pro odhlášení Azure. Tato adresa URL obsahuje znak otazníku (?). Chcete-li úspěšně odeslat tento znak, je nutné provést konkrétní kroky. Tyto kroky nemůžete dokončit z konzoly CLI FortiGate. Místo toho vytvořte relaci SSH k zařízení FortiGate pomocí nástroje, jako je například výstup. Pokud je vaše zařízení FortiGate virtuálním počítačem Azure, můžete z konzoly sériového prostředí pro virtuální počítače Azure provést následující kroky.

K provedení těchto kroků budete potřebovat hodnoty, které jste si poznamenali dříve:

- ID entity
- Adresa URL odpovědi
- Odhlašovací adresa URL
- Přihlašovací adresa URL Azure
- Identifikátor Azure AD
- Adresa URL pro odhlášení Azure
- Base64 – název certifikátu SAML (REMOTE_Cert_*N*)

1. Navažte na zařízení FortiGate relaci SSH a přihlaste se pomocí účtu správce FortiGate.
1. Spusťte tyto příkazy:

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
   > Adresa URL pro odhlášení Azure obsahuje `?` znak. Je nutné zadat speciální klíčovou sekvenci pro správné zadání této adresy URL do konzoly FortiGate sériového portu. Adresa URL je obvykle `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` .
   >
   > Pokud chcete zadat adresu URL pro odhlášení Azure v konzole sériového portu, zadejte `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation` .
   > 
   > Pak vyberte CTRL + V a vložte zbytek adresy URL, aby se řádek dokončí: `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` .

#### <a name="configure-fortigate-for-group-matching"></a>Konfigurace FortiGate pro spárování skupin

V této části nakonfigurujete FortiGate pro rozpoznání ID objektu skupiny zabezpečení, která obsahuje testovacího uživatele. Tato konfigurace umožní FortiGate rozhodování o přístupu na základě členství ve skupinách.

K provedení těchto kroků budete potřebovat ID objektu FortiGateAccess skupiny zabezpečení, kterou jste vytvořili dříve v tomto kurzu.

1. Navažte na zařízení FortiGate relaci SSH a přihlaste se pomocí účtu správce FortiGate.
1. Spusťte tyto příkazy:

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

#### <a name="create-a-fortigate-vpn-portals-and-firewall-policy"></a>Vytvoření FortiGatech portálů VPN a zásad brány firewall

V této části nakonfigurujete portály sítě VPN FortiGate a zásadu brány firewall, která uděluje přístup ke skupině zabezpečení FortiGateAccess, kterou jste vytvořili dříve v tomto kurzu.

Spolupracujte s [týmem podpory Fortigate](mailto:tac_amer@fortinet.com) , abyste přidali portály VPN a zásady brány firewall na FORTIGATE platformu VPN. Tento krok je nutné provést před použitím jednotného přihlašování.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když vyberete dlaždici FortiGate SSL VPN na přístupovém panelu, měli byste se automaticky přihlásit k síti VPN FortiGate SSL, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Microsoft a FortiGate doporučuje, abyste používali klienta VPN Fortinet FortiClient, abyste mohli co nejlépe využívat.

## <a name="additional-resources"></a>Další zdroje

- [Kurzy k integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte FortiGate SSL VPN s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
