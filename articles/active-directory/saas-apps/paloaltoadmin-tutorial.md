---
title: 'Kurz: Azure Active Directory integrace se sítěmi Palo Alto - uživatelského rozhraní správce | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Palo Alto sítě - Správce uživatelského rozhraní.
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
ms.openlocfilehash: 7a296c1e358bffa70ed88ba40380266e8731afd8
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211583"
---
# <a name="integrate-azure-active-directory-with-palo-alto-networks---admin-ui"></a>Integraci služby Azure Active Directory s Palo Alto sítě - Správce uživatelského rozhraní

V tomto kurzu zjistěte, jak mají být integrovány Palo Alto Networks - uživatelského rozhraní Správce služby Azure Active Directory (Azure AD).

Integrační Azure AD s Palo Alto sítě - Správce uživatelského rozhraní, získáte následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k sítím Palo Alto - uživatelského rozhraní správce.
- Můžete povolit uživatelům získat automaticky přihlášeni k sítím Palo Alto - Admin UI (jednotné přihlašování a jednotné přihlašování) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě, portálu Azure.

Další informace o integraci aplikací SaaS v Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Palo Alto sítě - Správce uživatelského rozhraní, potřebujete následující položky:

- Předplatné služby Azure AD
- Brána Firewall příští generace Palo Alto sítě nebo – Panorama (centralizovanou správu systém pro bránu firewall)

> [!NOTE]
> Při testování kroky v tomto kurzu, doporučujeme, abyste provedli *není* použít provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. 

Scénář, který je popsané v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

* Přidávání sítí Palo Alto - uživatelského rozhraní správce z Galerie
* Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-palo-alto-networks---admin-ui-from-the-gallery"></a>Přidat Palo Alto sítě - Správce uživatelského rozhraní z Galerie
Při konfiguraci integrace služby Azure AD s Palo Alto sítě - Správce uživatelského rozhraní, přidejte Palo Alto Networks - uživatelského rozhraní správce z Galerie si na seznam spravovaných aplikací SaaS následujícím způsobem:

1. V [portál Azure](https://portal.azure.com), v levém podokně vyberte **Azure Active Directory**. 

    ![Tlačítko Azure Active Directory][1]

2. Vyberte **podnikové aplikace, které** > **všechny aplikace**.

    ![Okno "Podnikové aplikace"][2]
    
3. Chcete-li přidat novou aplikaci, vyberte **novou aplikaci** tlačítka v horní části okna.

    !["Nová aplikace" tlačítko][3]

4. Do vyhledávacího pole zadejte **Palo Alto Networks - uživatelského rozhraní správce**, vyberte **Palo Alto Networks - uživatelského rozhraní správce** v seznamu výsledků a potom vyberte **přidat**.

    ![Palo Alto sítě - Správce uživatelského rozhraní v seznamu výsledků](./media/paloaltoadmin-tutorial/tutorial_step4-add-from-the-gallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurujete a testu Azure AD jednotné přihlašování s Palo Alto sítě - Správce uživatelského rozhraní, podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí identifikovat Palo Alto Networks - uživatelského rozhraní Správce uživatelů a jeho protějšku ve službě Azure AD. Jinými slovy je nutné vytvořit vztah propojení mezi uživatele Azure AD a stejného uživatele v sítích Palo Alto - uživatelského rozhraní správce.

K navázání vztahu odkaz, přiřadit jako Palo Alto sítě - uživatelského rozhraní správce *uživatelské jméno* hodnotu *uživatelské jméno* ve službě Azure AD.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Palo Alto sítě - Správce uživatelského rozhraní, dokončete stavebních bloků v následujících pět částech.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

Povolení služby Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování ve svých sítích Palo Alto - aplikace uživatelského rozhraní správce následujícím způsobem:

1. Na portálu Azure na **Palo Alto Networks - uživatelského rozhraní správce** stránky integrace aplikací, vyberte **jednotného přihlašování**.

    ![Na odkaz "Jednotného přihlašování"][4]

2. V **jednotného přihlašování** okno v **režimu přihlašování** vyberte **na základě SAML přihlašování**.
 
    ![Okno "Jednotného přihlašování"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_samlbase.png)

3. V části **Palo Alto sítě - správce domény uživatelského rozhraní a adresy URL**, postupujte takto:

    !["Palo Alto sítě - správce domény uživatelského rozhraní a adresy URL" jednotné přihlašování informace](./media/paloaltoadmin-tutorial/tutorial_general_show_advanced_url.png)
    
    a. V **přihlašovací adresa URL** pole, zadejte adresu URL v následujícím formátu: *https://\<zákazníka brány Firewall plně kvalifikovaný název domény > /php/login.php*.

    b. V **identifikátor** pole, zadejte adresu URL v následujícím formátu: *https://\<zákazníka brány Firewall plně kvalifikovaný název domény >: 443/SAML20/SP*.
    
    c. V **adresa URL odpovědi** pole, zadejte adresu URL Assertion příjemce Service (ACS) v následujícím formátu: *https://\<zákazníka brány Firewall plně kvalifikovaný název domény >: 443/SAML20/SP/ACS*.
    
    > [!NOTE] 
    > Předchozí hodnoty nejsou skutečné. Aktualizaci s skutečné přihlašovací adresa URL a identifikátor. Chcete-li získat hodnoty, obraťte se na [Palo Alto sítě - Správce uživatelského rozhraní klienta tým podpory](https://support.paloaltonetworks.com/support). 
 
4. Vzhledem k tomu Palo Alto sítě - Správce uživatelského rozhraní aplikace očekává SAML kontrolní výrazy ve specifickém formátu, nakonfigurujte deklarace identity, jak je znázorněno na následujícím obrázku. Správa hodnot atributů v **uživatelské atributy** části **integraci aplikací** stránky následujícím způsobem:
    
    ![V seznamu atributů tokenu SAML](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_attribute.png)
    
   > [!NOTE]
   > Vzhledem k tomu, že hodnoty atributu jsou jenom příklady, mapování na odpovídající hodnoty pro *uživatelské jméno* a *adminrole*. Existuje jiný volitelný atribut *accessdomain*, který se používá k omezení přístupu správce ke konkrétní virtuálních systémů v bráně firewall.
   >
        
    | Název atributu | Hodnota atributu |
    | --- | --- |    
    | uživatelské jméno | user.userprincipalname |
    | adminrole | customadmin |

    a. Vyberte **přidat atribut**.  
    
    ![Tlačítko "Přidat atribut"](./media/paloaltoadmin-tutorial/tutorial_attribute_04.png)

    **Přidat atribut** otevře se okno.

    ![Okno "Přidat atribut"](./media/paloaltoadmin-tutorial/tutorial_attribute_05.png)
    
    b. V **název** zadejte název atributu, který je uvedený na příslušném řádku.
    
    c. V **hodnotu** zadejte hodnotu atributu, který je uvedený na příslušném řádku.
    
    d. Vyberte **OK**.

    > [!NOTE]
    > Další informace o atributech najdete v následujících článcích:
    > * [Profil pro správu role pro správce uživatelského rozhraní (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Doména přístupu zařízení pro správu uživatelského rozhraní (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)
    >

5. V části **SAML podpisový certifikát**, vyberte **soubor XML s metadaty**a potom vyberte **Uložit**.

    ![Soubor XML s metadaty stáhnout odkaz](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_certificate.png) 

    ![Tlačítko Uložit](./media/paloaltoadmin-tutorial/tutorial_general_400.png)

6. Otevřete uživatelské rozhraní Správce brány Firewall sítě Palo Alto jako správce v novém okně.

7. Vyberte **zařízení** kartě.

    ![Na kartě zařízení](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

8. V levém podokně vyberte **poskytovatele Identity SAML**a potom vyberte **importovat** importovat soubor metadat.

    ![Tlačítko importovat metadata souboru](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

9. V **SAML identifikovat zprostředkovatele Server profil importovat** okno, postupujte takto:

    ![Okno "SAML identifikovat zprostředkovatele serveru profil Import"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. V **název profilu** pole, zadejte název (například **uživatelského rozhraní správce AzureAD**).
    
    b. V části **metadat zprostředkovatelů Identity**, vyberte **Procházet**a vyberte soubor metadata.xml, který jste předtím stáhli z portálu Azure.
    
    c. Vymazat **ověřit certifikát zprostředkovatele Identity** zaškrtávací políčko.
    
    d. Vyberte **OK**.
    
    e. Chcete-li provést konfiguraci brány firewall, vyberte **potvrzení**.

10. V levém podokně vyberte **poskytovatele Identity SAML**a potom vyberte profil poskytovatele Identity SAML (například **uživatelského rozhraní správce AzureAD**), který jste vytvořili v předchozím kroku. 

    ![Profil poskytovatele Identity SAML](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

11. V **profil serveru poskytovatele Identity SAML** okno, postupujte takto:

    ![Okno "Profil serveru poskytovatele Identity SAML"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. V **adresa URL SLO zprostředkovatele Identity** pole, nahraďte adresu URL dřív naimportovaný SLO následující adresu URL: **https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0**.
  
    b. Vyberte **OK**.

12. Na bráně Firewall sítě Palo Alto správce uživatelského rozhraní, vyberte **zařízení**a potom vyberte **rolí správce**.

13. Vyberte **přidat** tlačítko. 

14. V **profil Role správce** okno v **název** pole, zadejte název pro roli správce (například **fwadmin**).  
    Název role Správce by měl odpovídat Role správce SAML název atributu, který vám byl zaslán poskytovatele identit. Název role správce a hodnotou byly vytvořeny v kroku 4.

    ![Konfigurace Role správce sítě Palo Alto](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
15. Na bráně Firewall správce uživatelského rozhraní, vyberte **zařízení**a potom vyberte **profil ověření**.

16. Vyberte **přidat** tlačítko. 

17. V **profil ověření** okno, postupujte takto: 

    ![Okno "Profil ověření"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. V **název** pole, zadejte název (například **AzureSAML_Admin_AuthProfile**).
    
    b. V **typ** rozevíracího seznamu vyberte **SAML**. 
   
    c. V **profil serveru IdP** rozevíracího seznamu vyberte příslušný profil serveru poskytovatele Identity SAML (například **uživatelského rozhraní správce AzureAD**).
   
    c. Vyberte **povolit jednoho Odhlásit** zaškrtávací políčko.
    
    d. V **atributu Role správce** pole, zadejte název atributu (například **adminrole**). 
    
    e. Vyberte **Upřesnit** kartu a pak v části **povolit seznamu**, vyberte **přidat**. 
    
    ![Tlačítko Přidat na kartě Upřesnit](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)
    
    f. Vyberte **všechny** zaškrtněte políčko, nebo vyberte uživatele a skupiny, které může ověřit s tímto profilem.  
    Když se uživatel ověřuje, brána firewall odpovídá přidružené uživatelské jméno nebo skupinu pro položky v tomto seznamu. Pokud si nepřidáte položky, můžete ověřovat žádní uživatelé.

    g. Vyberte **OK**.

18. Aby správci mohli používat jednotné přihlašování SAML pomocí služby Azure, vyberte **zařízení** > **instalační program**. V **instalace** podokně, vyberte **správy** kartu a pak v části **nastavení ověřování**, vyberte **nastavení** tlačítko ("převod") . 

 ![Tlačítko Nastavení](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

19. Vyberte profil ověřování SAML, který jste vytvořili v kroku 17 (například **AzureSAML_Admin_AuthProfile**).

 ![Profil ověření pole](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

20. Vyberte **OK**.

21. Pro potvrzení konfigurace, vyberte **potvrzení**.


> [!TIP]
> Jak při instalaci aplikace, můžete si přečíst stručným verzi podle předchozích pokynů v [portál Azure](https://portal.azure.com). Po přidání aplikace v **služby Active Directory** > **podnikové aplikace, které** vyberte **jednotné přihlašování** kartě a potom přejdete vložené v dokumentaci **konfigurace** v dolní části. Další informace o funkci embedded dokumentaci najdete v tématu [Azure AD vložených dokumentaci]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

V této části vytvoříte testovacího uživatele Britta Simon na portálu Azure, následujícím způsobem:

![Vytvořit testovací uživatele Azure AD][100]

1. Na portálu Azure, v levém podokně, vyberte **Azure Active Directory**.

    ![Odkaz Azure Active Directory](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam aktuálního uživatele, vyberte **uživatelů a skupin** > **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

3. V horní části **všichni uživatelé** vyberte **přidat**.

    ![Tlačítko Přidat](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    **Uživatele** otevře se okno.

4. V **uživatele** okno, postupujte takto:

    ![Okno uživatele](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-palo-alto-networks---admin-ui-test-user"></a>Vytvořte sítě Palo Alto - uživatelského rozhraní správce testovacího uživatele

Palo Alto sítě - Správce uživatelského rozhraní podporuje, zřizování uživatelů za běhu. Pokud uživatel ještě neexistuje, automaticky vytvoří se v systému po úspěšném ověření. Není třeba z vám umožní vytvořit uživateli žádné akce.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolit uživatele Britta Simon používat tak, že udělíte přístup k sítím Palo Alto - uživatelského rozhraní správce Azure jednotné přihlašování. Chcete-li to provést, postupujte takto:

![Přiřadit role uživatele][200] 

1. Na portálu Azure otevřete **aplikace** zobrazení, přejděte na **Directory** zobrazit a potom vyberte **podnikové aplikace, které** > **všechny aplikace**.

    !["Podnikové aplikace" a "Všechny aplikace" odkazy][201] 

2. V **aplikace** seznamu, vyberte **Palo Alto Networks - uživatelského rozhraní správce**.

    ![Sítě Palo Alto - odkaz uživatelského rozhraní správce](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_app.png)  

3. V levém podokně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Vyberte **přidat** a pak na **přidat přiřazení** podokně, vyberte **uživatelů a skupin**.

    ![V podokně Přidat přiřazení][203]

5. V **uživatelů a skupin** okno v **uživatelé** seznamu, vyberte **Britta Simon**.

6. Vyberte **vyberte** tlačítko.

7. V **přidat přiřazení** vyberte **přiřadit**.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části otestovat vaše konfigurace Azure AD jeden přihlašování pomocí přístupového panelu.

Když vyberete Palo Alto Networks - dlaždice správce uživatelského rozhraní na přístupovém panelu budete by měl být přihlášení automaticky k Palo Alto sítě - Správce uživatelského rozhraní aplikace.

Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS do Azure Active Directory](tutorial-list.md)
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

