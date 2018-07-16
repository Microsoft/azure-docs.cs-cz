---
title: 'Kurz: Integrace Azure Active Directory se službou Palo Alto Networks – uživatelské rozhraní správce | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Palo Alto Networks – uživatelské rozhraní správce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: b39879cc8548139879a3039c5e0c6b924e83c107
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39046667"
---
# <a name="integrate-azure-active-directory-with-palo-alto-networks---admin-ui"></a>Integrace Azure Active Directory s Palo Alto Networks – uživatelské rozhraní správce

V tomto kurzu se dozvíte, jak integrovat Palo Alto Networks – uživatelské rozhraní Správce služby Azure Active Directory (Azure AD).

Integrace Azure AD s Palo Alto Networks - uživatelskému rozhraní správce, získáte následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Palo Alto Networks – uživatelské rozhraní správce.
- Můžete povolit uživatelům získat automaticky přihlášeni k Palo Alto Networks – uživatelské rozhraní správce (jednotné přihlašování a jednotné přihlašování) s jejich účty Azure AD.
- Můžete spravovat své účty v jednom centrálním místě na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Palo Alto Networks - uživatelskému rozhraní správce, potřebujete následující položky:

- S předplatným služby Azure AD
- Firewall nové generace Palo Alto sítě nebo Panorama (centralizovaná správa systému pro bránu firewall)

> [!NOTE]
> Při testování kroky v tomto kurzu doporučujeme, abyste udělali *není* použijte produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. 

Scénář, který je popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

* Přidání Palo Alto Networks – uživatelské rozhraní správce z Galerie
* Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-palo-alto-networks---admin-ui-from-the-gallery"></a>Přidat Palo Alto Networks – uživatelské rozhraní správce z Galerie
Konfigurace integrace Azure AD s Palo Alto Networks - uživatelskému rozhraní správce, přidejte Palo Alto Networks – uživatelské rozhraní správce z Galerie na váš seznam spravovaných aplikací SaaS následujícím způsobem:

1. V [webu Azure portal](https://portal.azure.com), v levém podokně vyberte **Azure Active Directory**. 

    ![Tlačítko Azure Active Directory][1]

2. Vyberte **podnikové aplikace** > **všechny aplikace**.

    ![V okně "Podnikové aplikace"][2]
    
3. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko v horní části okna.

    !["Nové aplikace" tlačítko][3]

4. Do vyhledávacího pole zadejte **Palo Alto Networks – uživatelské rozhraní správce**vyberte **Palo Alto Networks – uživatelské rozhraní správce** v seznamu výsledků a pak vyberte **přidat**.

    ![Palo Alto Networks – uživatelské rozhraní správce v seznamu výsledků](./media/paloaltoadmin-tutorial/tutorial_step4-add-from-the-gallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a testu s Palo Alto Networks – uživatelské rozhraní Správce služby Azure AD jednotné přihlašování založené na testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje k identifikaci Palo Alto Networks – uživatelské rozhraní správce a jeho protějšek ve službě Azure AD. Jinými slovy musí vytvořit vztah odkazu mezi uživatele služby Azure AD a že jeden uživatel Palo Alto Networks – uživatelské rozhraní správce.

K navázání vztahu odkazu, přiřaďte jako Palo Alto Networks – uživatelské rozhraní správce *uživatelské jméno* hodnotu *uživatelské jméno* ve službě Azure AD.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Palo Alto Networks – uživatelské rozhraní správce dokončení stavební bloky v příštích pěti oddílů.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v Palo Alto Networks – uživatelské rozhraní správce aplikace následujícím způsobem:

1. Na webu Azure Portal na **Palo Alto Networks – uživatelské rozhraní správce** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Odkaz "Jednotné přihlašování"][4]

2. V **jednotného přihlašování** okno v **režim jednotného přihlašování** vyberte **přihlašování na základě SAML**.
 
    ![V okně "Jednotné přihlašování"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_samlbase.png)

3. V části **Palo Alto Networks - domény uživatelské rozhraní správce a adresy URL**, postupujte takto:

    !["Palo Alto Networks - domény uživatelské rozhraní správce a adresy URL" jednotné přihlašování – informace](./media/paloaltoadmin-tutorial/tutorial_general_show_advanced_url.png)
    
    a. V **přihlašovací adresa URL** pole, zadejte adresu URL v následujícím formátu: *https://\<zákazníka brány Firewall na plně kvalifikovaný název domény > /php/login.php*.

    b. V **identifikátor** pole, zadejte adresu URL v následujícím formátu: *https://\<zákazníka brány Firewall na plně kvalifikovaný název domény >: 443/SAML20/SP*.
    
    c. V **adresy URL odpovědi** pole, zadejte adresu URL Assertion Consumer Service (ACS) v následujícím formátu: *https://\<zákazníka brány Firewall na plně kvalifikovaný název domény >: 443/SAML20/SP/ACS*.
    
    > [!NOTE] 
    > Předchozí hodnoty nejsou skutečný. Aktualizujte je s skutečné přihlašovací adresu URL a identifikátorem. K získání hodnot, kontaktujte [Palo Alto Networks - tým podpory klient uživatelského rozhraní pro správu](https://support.paloaltonetworks.com/support). 
 
4. Vzhledem k tomu, že Palo Alto Networks – uživatelské rozhraní správce aplikace očekává, že kontrolní výrazy SAML v určitém formátu, nakonfigurujte deklarace identity, jak je znázorněno na následujícím obrázku. Spravovat v hodnoty atributů **atributy uživatele** část **Application Integration** stránky následujícím způsobem:
    
    ![V seznamu atributy tokenu SAML](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_attribute.png)
    
   > [!NOTE]
   > Vzhledem k tomu, že hodnoty atributů jsou jen příklady, namapujte příslušné hodnoty pro *uživatelské jméno* a *adminrole*. Existuje jiný volitelný atribut, *accessdomain*, který se používá k omezení přístupu správce ke konkrétní virtuálních systémů v bráně firewall.
   >
        
    | Název atributu | Hodnota atributu |
    | --- | --- |    
    | uživatelské jméno | user.userprincipalname |
    | adminrole | customadmin |

    a. Vyberte **přidat atribut**.  
    
    ![Tlačítko "Přidat atribut"](./media/paloaltoadmin-tutorial/tutorial_attribute_04.png)

    **Přidat atribut** otevře se okno.

    ![V okně "Přidat atribut"](./media/paloaltoadmin-tutorial/tutorial_attribute_05.png)
    
    b. V **název** zadejte název atributu, který je zobrazený pro tento řádek.
    
    c. V **hodnotu** zadejte hodnotu atributu, který je zobrazený pro tento řádek.
    
    d. Vyberte **OK**.

    > [!NOTE]
    > Další informace o atributech najdete v následujících článcích:
    > * [Profil pro správu role pro uživatelské rozhraní správce (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Doména zařízení přístup k uživatelskému rozhraní správce (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)
    >

5. V části **podpisový certifikát SAML**vyberte **soubor XML s metadaty**a pak vyberte **Uložit**.

    ![Odkaz ke stažení soubor XML s metadaty](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_certificate.png) 

    ![Tlačítko Uložit](./media/paloaltoadmin-tutorial/tutorial_general_400.png)

6. Otevřete uživatelské rozhraní Správce brány Firewall sítě Palo Alto jako správce v novém okně.

7. Vyberte **zařízení** kartu.

    ![Na kartě zařízení](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

8. V levém podokně vyberte **zprostředkovatele Identity SAML**a pak vyberte **importovat** importovat soubor metadat.

    ![Tlačítko importovat metadata souboru](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

9. V **SAML identifikovat zprostředkovatele Server profilu Import** okno, postupujte takto:

    ![V okně "SAML identifikovat zprostředkovatele Server profilu Import"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. V **název profilu** pole, zadejte název (například **uživatelskému rozhraní správce Azure AD**).
    
    b. V části **metadat zprostředkovatele Identity**vyberte **Procházet**a vyberte soubor metadata.xml, který jste předtím stáhli z portálu Azure portal.
    
    c. Zrušte **ověřit certifikát poskytovatele Identity** zaškrtávací políčko.
    
    d. Vyberte **OK**.
    
    e. K provedení konfigurace v bráně firewall, vyberte **potvrzení**.

10. V levém podokně vyberte **zprostředkovatele Identity SAML**a potom vyberte profil SAML zprostředkovatele Identity (třeba **uživatelskému rozhraní správce Azure AD**), který jste vytvořili v předchozím kroku. 

    ![Profil SAML zprostředkovatele Identity](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

11. V **profilu serveru zprostředkovatele Identity SAML** okno, postupujte takto:

    ![V okně "Profil serveru zprostředkovatele Identity SAML"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. V **adresa URL cíle na úrovni služby pro zprostředkovatele Identity** přepište dříve importovaná adresa URL cíle na úrovni služby s následující adresou URL: **https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0**.
  
    b. Vyberte **OK**.

12. Na bránu Firewall sítě Palo Alto uživatelskému rozhraní správce, vyberte **zařízení**a pak vyberte **rolí administrátora**.

13. Vyberte **přidat** tlačítko. 

14. V **profil Role správce** okno v **název** zadejte název pro roli správce (například **fwadmin**).  
    Název role Správce by měl odpovídat názvu atributu Role správce SAML, odeslané poskytovatelem Identity. Název role správce a hodnota vytvořené v kroku 4.

    ![Konfigurace Role správce sítě Palo Alto](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
15. Na uživatelské rozhraní Správce brány Firewall, vyberte **zařízení**a pak vyberte **ověřování profilu**.

16. Vyberte **přidat** tlačítko. 

17. V **ověřování profilu** okno, postupujte takto: 

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

18. Aby správci mohli používat jednotné přihlašování SAML pomocí služby Azure, vyberte **zařízení** > **nastavení**. V **nastavení** podokně, vyberte **správy** kartu a potom v nabídce **nastavení ověřování**, vyberte **nastavení** tlačítko ("zařízení") . 

 ![Tlačítko Nastavení](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

19. Vyberte profil ověřování SAML, který jste vytvořili v kroku 17 (například **AzureSAML_Admin_AuthProfile**).

 ![Pole ověřování profilu](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

20. Vyberte **OK**.

21. Pro potvrzení konfigurace, vyberte **potvrzení**.


> [!TIP]
> Jak při nastavování aplikace, můžete si přečíst stručné verzi podle předchozích pokynů v [webu Azure portal](https://portal.azure.com). Po přidání aplikace ve **služby Active Directory** > **podnikové aplikace** vyberte **Single Sign-On** kartu a potom přejdete dokumentace ke službě v Embedded **konfigurace** oblast v dolní části. Další informace o funkci vložený dokumentaci najdete v tématu [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části můžete vytvořit testovacího uživatele Britta Simon na webu Azure Portal následujícím způsobem:

![Vytvořit testovacího uživatele Azure AD][100]

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**.

    ![Propojení služby Azure Active Directory](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam aktuálních uživatelů, vyberte **uživatelů a skupin** > **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

3. V horní části **všichni uživatelé** okně **přidat**.

    ![Tlačítko Přidat](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    **Uživatele** otevře se okno.

4. V **uživatele** okno, postupujte takto:

    ![V okně uživatele](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-palo-alto-networks---admin-ui-test-user"></a>Vytvoření Palo Alto Networks – uživatelské rozhraní správce testovacího uživatele

Palo Alto Networks – uživatelské rozhraní správce podporuje zřizování uživatelů just-in-time. Pokud uživatel ještě neexistuje, automaticky vytvoří se v systému po úspěšném ověření. Nemusíte nic dělat z k vytvoření uživatele.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolit uživatele Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Palo Alto Networks – uživatelské rozhraní správce. Chcete-li to provést, postupujte takto:

![Přiřazení role uživatele][200] 

1. Na webu Azure Portal, otevřete **aplikací** zobrazení, přejděte na **Directory** zobrazit a pak vyberte **podnikové aplikace** > **všechny aplikace**.

    !["Podnikové aplikace" a "Všechny aplikace" odkazy][201] 

2. V **aplikací** seznamu vyberte **Palo Alto Networks – uživatelské rozhraní správce**.

    ![Sítě Palo Alto – uživatelské rozhraní správce odkazů](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_app.png)  

3. V levém podokně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Vyberte **přidat** a pak na **přidat přiřazení** vyberte **uživatelů a skupin**.

    ![Podokno Přidat přiřazení][203]

5. V **uživatelů a skupin** okno v **uživatelé** seznamu vyberte **Britta Simon**.

6. Vyberte **vyberte** tlačítko.

7. V **přidat přiřazení** okně **přiřadit**.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když vyberete Palo Alto Networks – uživatelské rozhraní správce dlaždici na přístupovém panelu, by měl být přihlásíte automaticky pro Palo Alto Networks – uživatelské rozhraní správce aplikace.

Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS se službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/paloaltoadmin-tutorial/tutorial_general_01.png
[2]: ./media/paloaltoadmin-tutorial/tutorial_general_02.png
[3]: ./media/paloaltoadmin-tutorial/tutorial_general_03.png
[4]: ./media/paloaltoadmin-tutorial/tutorial_general_04.png

[100]: ./media/paloaltoadmin-tutorial/tutorial_general_100.png

[200]: ./media/paloaltoadmin-tutorial/tutorial_general_200.png
[201]: ./media/paloaltoadmin-tutorial/tutorial_general_201.png
[202]: ./media/paloaltoadmin-tutorial/tutorial_general_202.png
[203]: ./media/paloaltoadmin-tutorial/tutorial_general_203.png

