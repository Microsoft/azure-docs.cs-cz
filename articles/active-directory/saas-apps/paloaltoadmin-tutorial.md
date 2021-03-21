---
title: 'Kurz: Azure Active Directory integrace s Palo Alto Networks – uživatelské rozhraní pro správu | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Palo Alto Networks – uživatelské rozhraní pro správu.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 57b1d47fa40c0af4bced1e4169fe60cd759ee2f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97963634"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Kurz: Azure Active Directory integrace s Palo Alto Networks – uživatelské rozhraní pro správu

V tomto kurzu se naučíte, jak integrovat Palo Alto Networks – uživatelské rozhraní pro správu pomocí Azure Active Directory (Azure AD).
Integrace Palo Alto Networks – uživatelské rozhraní pro správu s Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k sítím Palo Alto Networks – uživatelské rozhraní pro správu.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Palo Alto Networks – uživatelské rozhraní správce (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Pokud chcete nakonfigurovat integraci Azure AD s uživatelským rozhraním Palo Alto Networks-admin, budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Palo Alto Networks – odběr s povoleným jednotným přihlašováním uživatelského rozhraní pro správu

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Palo Alto Networks – uživatelské rozhraní pro správu podporuje jednotné přihlašování se spuštěným **SP**
* Palo Alto Networks – uživatelské rozhraní pro správu podporuje **pouze dobu** zřizování uživatelů

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Přidání sítě Palo Alto – uživatelské rozhraní správce z Galerie

Pokud chcete nakonfigurovat integraci Palo Alto Networks – uživatelské rozhraní pro správu do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Palo Alto Networks – uživatelské rozhraní správce z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Palo Alto Networks-admin UI** .
1. Vyberte **Palo Alto Networks – uživatelské rozhraní pro správu** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Palo Alto Networks – uživatelské rozhraní správce založené na testovacím uživateli s názvem **B. Simon**.
Aby jednotné přihlašování fungovalo, musí se vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Palo Alto Networks – uživatelské rozhraní pro správu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Palo Alto Networks – uživatelské rozhraní pro správu, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Palo Alto Networks – uživatelské rozhraní správce SSO](#configure-palo-alto-networks---admin-ui-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvoření Palo Alto Networks – uživatel testu uživatelského rozhraní pro správce](#create-palo-alto-networks---admin-ui-test-user)** – bude mít protějšek B. Simon v Palo Alto Networks – uživatelské rozhraní pro správu, které je propojené s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce aplikace **Palo Alto Networks – pro správu uživatelského rozhraní pro správce** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<Customer Firewall FQDN>/php/login.php`

    b. Do pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<Customer Firewall FQDN>:443/SAML20/SP`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL služby assertion Consumer Service (ACS) v následujícím formátu: `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným Sign-On URL, identifikátor a adresu URL odpovědi. Chcete-li získat tyto hodnoty, kontaktujte [Palo Alto Networks – tým podpory klientů pro správu uživatelského rozhraní](https://support.paloaltonetworks.com/support) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.
    >
    > V **identifikátoru** je vyžadován port 443 a **Adresa URL odpovědi** , protože tyto hodnoty jsou pevně zakódované do brány firewall Palo Alto. Při odebrání čísla portu dojde k chybě během přihlašování, pokud je odebrán.

    > V **identifikátoru** je vyžadován port 443 a **Adresa URL odpovědi** , protože tyto hodnoty jsou pevně zakódované do brány firewall Palo Alto. Při odebrání čísla portu dojde k chybě během přihlašování, pokud je odebrán.

1. Aplikace Palo Alto Networks – uživatelské rozhraní správce sítě očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

   > [!NOTE]
   > Vzhledem k tomu, že hodnoty atributu jsou pouze příklady, namapujte příslušné hodnoty pro *username* a *adminrole*. K dispozici je jiný volitelný atribut *accessdomain*, který slouží k omezení přístupu správce ke konkrétním virtuálním systémům v bráně firewall.

1. Kromě toho očekává aplikace Palo Alto Networks-admin UI několik atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Name |  Zdrojový atribut|
    | --- | --- |
    | username | User. userPrincipalName |
    | adminrole | customadmin |
    | | |

    > [!NOTE]
    > Hodnota _adminrole_ by měla být stejná jako název role, která je nakonfigurovaná v **Palo Alto Networks** , jak je uvedeno v kroku 9. 

    > [!NOTE]
    > Další informace o atributech naleznete v následujících článcích:
    > * [Profil role správce pro uživatelské rozhraní správce (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Doména přístupu k zařízení pro uživatelské rozhraní správce (accessdomain)](https://docs.paloaltonetworks.com/pan-os/8-0/pan-os-web-interface-help/device/device-access-domain.html)

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **nastavení sítě Palo Alto Networks – uživatelské rozhraní pro správu** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Palo Alto Networks – uživatelské rozhraní správce.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Palo Alto Networks – uživatelské rozhraní pro správu**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-palo-alto-networks---admin-ui-sso"></a>Konfigurace sítí Palo Alto – jednotné přihlašování k uživatelskému rozhraní správce

1. Otevřete uživatelské rozhraní pro správu brány firewall Palo Alto Networks jako správce v novém okně.

2. Vyberte kartu **zařízení** .

    ![Karta zařízení](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

3. V levém podokně vyberte **zprostředkovatel identity SAML** a pak vyberte **importovat** a importujte soubor metadat.

    ![Tlačítko importovat soubor metadat](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

4. V okně pro **import profilu serveru pro identifikaci SAML** postupujte takto:

    ![Okno pro import profilu serveru poskytovatele s identifikací SAML](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. Do pole **název profilu** zadejte název (například **uživatelské rozhraní správce AzureAD**).

    b. V části **metadata zprostředkovatele identity** vyberte **Procházet** a vyberte soubor metadata.xml, který jste si stáhli dříve z Azure Portal.

    c. Zrušte zaškrtnutí políčka **ověřit certifikát poskytovatele identity** .

    d. Vyberte **OK**.

    e. Chcete-li potvrdit konfigurace v bráně firewall, vyberte **Potvrdit**.

5. V levém podokně vyberte **zprostředkovatel identity SAML** a potom vyberte profil zprostředkovatele identity SAML (například **uživatelské rozhraní správce AzureAD**), které jste vytvořili v předchozím kroku.

    ![Profil zprostředkovatele identity SAML](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

6. V okně **profil serveru zprostředkovatele identity SAML** udělejte toto:

    ![Okno profil serveru zprostředkovatele identity SAML](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. V poli **Adresa URL pro objekt slo zprostředkovatele identity** nahraďte dříve IMPORTOVANOU adresu URL objektu slo následující adresou URL: `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
    b. Vyberte **OK**.

7. V uživatelském rozhraní správce Palo Alto Networks firewallu vyberte **zařízení** a pak vyberte **role správce**.

8. Vyberte tlačítko **Přidat** .

9. V okně **profilu role správce** do pole **název** zadejte název role správce (například **fwadmin**). Název role správce by měl odpovídat názvu atributu role správce SAML, který byl odeslán zprostředkovatelem identity. Název a hodnota role správce se vytvořily v části **atributy uživatele** v Azure Portal.

    ![Konfigurace role správce sítě Palo Alto](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
10. V uživatelském rozhraní pro správu brány firewall vyberte **zařízení** a pak vyberte **profil ověřování**.

11. Vyberte tlačítko **Přidat** .

12. V okně **ověřovací profil** proveďte následující akce: 

    ![Okno ověřovací profil](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. Do pole **název** zadejte název (například **AzureSAML_Admin_AuthProfile**).

    b. V rozevíracím seznamu **typ** vyberte **SAML**. 

    c. V rozevíracím seznamu **profil serveru IDP** vyberte příslušný profil serveru poskytovatele identity SAML (například **uživatelské rozhraní správce AzureAD**).

    c. Zaškrtněte políčko **Povolit jednotné odhlášení** .

    d. Do pole **atribut role správce** zadejte název atributu (například **adminrole**).

    e. Vyberte kartu **Upřesnit** a potom v části **povolený seznam** vyberte **Přidat**.

    ![Tlačítko Přidat na kartě Upřesnit](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)

    f. Zaškrtněte políčko **vše** nebo vyberte uživatele a skupiny, které se mohou s tímto profilem ověřit.  
    Když se uživatel ověří, brána firewall se shoduje s přidruženým uživatelským jménem nebo skupinou na položkách v tomto seznamu. Pokud nepřidáte položky, nebudete moct ověřovat žádné uživatele.

    například Vyberte **OK**.

13. Pokud chcete správcům povolit použití jednotného přihlašování SAML pomocí Azure , vyberte  >  **Nastavení** zařízení. V podokně **Nastavení** vyberte kartu **Správa** a potom v části **nastavení ověřování** vyberte tlačítko **Nastavení** (ozubené kolo).

    ![Tlačítko nastavení](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

14. Vyberte profil ověřování SAML, který jste vytvořili v okně profilu ověřování (například **AzureSAML_Admin_AuthProfile**).

    ![Pole profil ověřování](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

15. Vyberte **OK**.

16. Nastavení potvrďte tak, že vyberete **Potvrdit**.

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Vytvoření Palo Alto Networks – uživatel testu uživatelského rozhraní pro správu

Palo Alto Networks – uživatelské rozhraní pro správu podporuje zřizování uživatelů za běhu. Pokud uživatel ještě neexistuje, automaticky se vytvoří v systému po úspěšném ověření. Od vás není potřeba nic dělat, abyste mohli vytvořit uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na Palo Alto Networks – přihlašovací adresa URL uživatelského rozhraní pro správu, kde můžete spustit tok přihlášení. 

* V Palo Alto Networks – přihlašovací adresa URL uživatelského rozhraní pro správu a zahájení toku přihlášení přímo z tohoto účtu.

* Můžete použít aplikaci Microsoft moje aplikace. Po kliknutí na dlaždici uživatelské rozhraní správce sítě Palo Alto v seznamu Moje aplikace byste měli být automaticky přihlášeni k uživatelskému rozhraní správce Palo Alto Networks – admin, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování Palo Alto Networks – uživatelské rozhraní správce můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).