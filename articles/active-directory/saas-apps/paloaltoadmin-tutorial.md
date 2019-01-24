---
title: 'Kurz: Integrace Azure Active Directory s Palo Alto Networks – uživatelské rozhraní správce | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Palo Alto Networks – uživatelské rozhraní správce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.openlocfilehash: 06c32dbd9ee0d960e4cd17476349ba3d1a784d81
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54818995"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Kurz: Integrace Azure Active Directory s Palo Alto Networks – uživatelské rozhraní správce

V tomto kurzu se dozvíte, jak integrovat Palo Alto Networks – uživatelské rozhraní správce se službou Azure Active Directory (Azure AD).
Integrace Palo Alto Networks – uživatelské rozhraní správce Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Palo Alto Networks – uživatelské rozhraní správce.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k Palo Alto Networks – uživatelské rozhraní správce (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Palo Alto Networks - uživatelskému rozhraní správce, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Palo Alto Networks - uživatelskému rozhraní správce jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje uživatelské rozhraní správce Palo Alto Networks - **SP** jednotné přihlašování zahájené pomocí
* Podporuje uživatelské rozhraní správce Palo Alto Networks - **JIT** zřizování uživatelů

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Přidání Palo Alto Networks – uživatelské rozhraní správce z Galerie

Konfigurace integrace Palo Alto Networks – uživatelské rozhraní správce do služby Azure AD, budete muset přidat Palo Alto Networks – uživatelské rozhraní správce z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Palo Alto Networks – uživatelské rozhraní správce z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Palo Alto Networks – uživatelské rozhraní správce**vyberte **Palo Alto Networks – uživatelské rozhraní správce** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Palo Alto Networks – uživatelské rozhraní správce v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, nakonfigurovat a otestovat Azure AD jednotné přihlašování s Palo Alto Networks – uživatelské rozhraní správce podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování k práci, vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Palo Alto Networks – je potřeba navázat uživatelskému rozhraní správce.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Palo Alto Networks - uživatelskému rozhraní správce, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Palo Alto Networks - správce uživatelského rozhraní Single Sign-On](#configure-palo-alto-networks---admin-ui-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření Palo Alto Networks - uživatelskému rozhraní správce testovacího uživatele](#create-palo-alto-networks---admin-ui-test-user)**  – Pokud chcete mít protějšek Britta Simon Palo Alto Networks - uživatelskému rozhraní správce, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Palo Alto Networks - uživatelskému rozhraní správce, postupujte následovně:

1. V [webu Azure portal](https://portal.azure.com/)na **Palo Alto Networks – uživatelské rozhraní správce** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Palo Alto sítě - správce domény uživatelského rozhraní a adresy URL jednotné přihlašování – informace](common/sp-identifier-reply.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<Customer Firewall FQDN>/php/login.php`

    b. V **identifikátor** pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<Customer Firewall FQDN>:443/SAML20/SP`

    c. V **adresy URL odpovědi** textové pole, zadejte adresu URL Assertion Consumer Service (ACS) v následujícím formátu: `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL, identifikátoru a adresa URL odpovědi. Kontakt [Palo Alto Networks - tým podpory klient uživatelského rozhraní pro správu](https://support.paloaltonetworks.com/support) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Palo Alto Networks – uživatelské rozhraní správce aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

   > [!NOTE]
   > Vzhledem k tomu, že hodnoty atributů jsou jen příklady, namapujte příslušné hodnoty pro *uživatelské jméno* a *adminrole*. Existuje jiný volitelný atribut, *accessdomain*, který se používá k omezení přístupu správce ke konkrétní virtuálních systémů v bráně firewall.
   >

6. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    | Název |  Zdrojový atribut|
    | --- | --- |
    | uživatelské jméno | user.userprincipalname |
    | adminrole | customadmin |
    | | |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

    > [!NOTE]
    > Další informace o atributech najdete v následujících článcích:
    > * [Profil pro správu role pro uživatelské rozhraní správce (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Doména zařízení přístup k uživatelskému rozhraní správce (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

8. Na **nastavení Palo Alto Networks – uživatelské rozhraní správce** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-palo-alto-networks---admin-ui-single-sign-on"></a>Konfigurace Palo Alto Networks – uživatelské rozhraní správce jednotného přihlašování

1. Otevřete uživatelské rozhraní Správce brány Firewall sítě Palo Alto jako správce v novém okně.

2. Vyberte **zařízení** kartu.

    ![Na kartě zařízení](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

3. V levém podokně vyberte **zprostředkovatele Identity SAML**a pak vyberte **importovat** importovat soubor metadat.

    ![Tlačítko importovat metadata souboru](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

4. V **SAML identifikovat zprostředkovatele Server profilu Import** okno, postupujte takto:

    ![V okně "SAML identifikovat zprostředkovatele Server profilu Import"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. V **název profilu** pole, zadejte název (například **uživatelskému rozhraní správce Azure AD**).
    
    b. V části **metadat zprostředkovatele Identity**vyberte **Procházet**a vyberte soubor metadata.xml, který jste předtím stáhli z portálu Azure portal.
    
    c. Zrušte **ověřit certifikát poskytovatele Identity** zaškrtávací políčko.
    
    d. Vyberte **OK**.
    
    e. K provedení konfigurace v bráně firewall, vyberte **potvrzení**.

5. V levém podokně vyberte **zprostředkovatele Identity SAML**a potom vyberte profil SAML zprostředkovatele Identity (třeba **uživatelskému rozhraní správce Azure AD**), který jste vytvořili v předchozím kroku.

    ![Profil SAML zprostředkovatele Identity](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

6. V **profilu serveru zprostředkovatele Identity SAML** okno, postupujte takto:

    ![V okně "Profil serveru zprostředkovatele Identity SAML"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. V **adresa URL cíle na úrovni služby pro zprostředkovatele Identity** přepište dříve importovaná adresa URL cíle na úrovni služby s následující adresou URL: `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
    b. Vyberte **OK**.

7. Na bránu Firewall sítě Palo Alto uživatelskému rozhraní správce, vyberte **zařízení**a pak vyberte **rolí administrátora**.

8. Vyberte **přidat** tlačítko.

9. V **profil Role správce** okno v **název** zadejte název pro roli správce (například **fwadmin**). Název role Správce by měl odpovídat názvu atributu Role správce SAML, odeslané poskytovatelem Identity. Název role správce a hodnoty, které byly vytvořeny v **atributy uživatele** části webu Azure Portal.

    ![Konfigurace Role správce sítě Palo Alto](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
10. Na uživatelské rozhraní Správce brány Firewall, vyberte **zařízení**a pak vyberte **ověřování profilu**.

11. Vyberte **přidat** tlačítko.

12. V **ověřování profilu** okno, postupujte takto: 

    ![V okně "Ověřování profilu"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. V **název** pole, zadejte název (například **AzureSAML_Admin_AuthProfile**).

    b. V **typ** rozevíracího seznamu vyberte **SAML**. 

    c. V **profilu serveru zprostředkovatele identity** rozevírací seznam, vyberte příslušný profil serveru zprostředkovatele Identity SAML (například **uživatelskému rozhraní správce Azure AD**).

    c. Vyberte **povolit jednotné odhlašování** zaškrtávací políčko.

    d. V **atributu Role správce** pole, zadejte název (například **adminrole**).

    e. Vyberte **Upřesnit** kartu a potom v nabídce **seznam povolených disků**vyberte **přidat**.

    ![Tlačítko Přidat na kartě Upřesnit](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)

    f. Vyberte **všechny** zaškrtněte políčko, nebo vyberte uživatele a skupiny, kteří můžou ověřovat s tímto profilem.  
    Při ověření uživatele, brána firewall odpovídá přidružené uživatelské jméno nebo skupinu pro položky v tomto seznamu. Pokud nechcete přidat položky, můžete ověřovat žádní uživatelé.

    g. Vyberte **OK**.

13. Aby správci mohli používat jednotné přihlašování SAML pomocí služby Azure, vyberte **zařízení** > **nastavení**. V **nastavení** podokně, vyberte **správy** kartu a potom v nabídce **nastavení ověřování**, vyberte **nastavení** tlačítko ("zařízení") .

    ![Tlačítko Nastavení](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

14. Vyberte profil ověřování SAML, který jste vytvořili v okně profilu ověření systému (například **AzureSAML_Admin_AuthProfile**).

    ![Pole ověřování profilu](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

15. Vyberte **OK**.

16. Pro potvrzení konfigurace, vyberte **potvrzení**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon udělení přístupu k Palo Alto Networks – uživatelské rozhraní správce používá jednotné přihlašování Azure.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Palo Alto Networks – uživatelské rozhraní správce**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Palo Alto Networks – uživatelské rozhraní správce**.

    ![Sítě Palo Alto – uživatelské rozhraní správce odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Vytvoření Palo Alto Networks - uživatelskému rozhraní správce testovacího uživatele

Palo Alto Networks – uživatelské rozhraní správce podporuje zřizování uživatelů just-in-time. Pokud uživatel ještě neexistuje, automaticky vytvoří se v systému po úspěšném ověření. Nemusíte nic dělat z k vytvoření uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na Palo Alto Networks – uživatelské rozhraní správce dlaždici na přístupovém panelu, vám by měl být automaticky přihlášeni ke Palo Alto Networks – uživatelské rozhraní správce, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)