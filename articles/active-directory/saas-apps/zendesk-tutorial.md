---
title: 'Kurz: Integrace Azure Active Directory se službou Zendesku | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zendesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: 37d20eabfc8fb883cda346dc8b55a17b8b959218
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268170"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Kurz: Integrace Azure Active Directory s Zendesku.

V tomto kurzu se dozvíte, jak integrovat Zendesku s Azure Active Directory (Azure AD).

Integrace Zendesku s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k službě Zendesk.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Zendesku (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Zendesku, potřebujete následující položky:

- Předplatné Azure AD
- Zendesku jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Zendesku z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-zendesk-from-the-gallery"></a>Přidání Zendesku z Galerie

Pokud chcete nakonfigurovat integraci Zendesku do služby Azure AD, budete muset přidat Zendesku z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Zendesku z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![image](./media/zendesk-tutorial/selectazuread.png)

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![image](./media/zendesk-tutorial/a_select_app.png)
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![image](./media/zendesk-tutorial/a_new_app.png)

4. Do vyhledávacího pole zadejte **Zendesku**vyberte **Zendesku** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![image](./media/zendesk-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Zendesku podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v systému Zendesk je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatelem v systému Zendesk je potřeba navázat.

V systému Zendesk, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Zendesku, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Zendesku](#create-a-zendesk-test-user)**  – Pokud chcete mít protějšek Britta Simon v systému Zendesk, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Zendesku.

**Ke konfiguraci Azure AD jednotné přihlašování s Zendesku, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com/)na **Zendesku** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![image](./media/zendesk-tutorial/b1_b2_select_sso.png)

2. Klikněte na tlačítko **režim změnit jednotného přihlašování** nad obrazovky, vyberte **SAML** režimu.

      ![image](./media/zendesk-tutorial/b1_b2_saml_ssso.png)

3. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![image](./media/zendesk-tutorial/b1_b2_saml_sso.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **základní konfiguraci SAML** dialogového okna.

    ![image](./media/zendesk-tutorial/b1-domains_and_urlsedit.png)

5. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.zendesk.com`.

    b. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `<subdomain>.zendesk.com`.

    ![image](./media/zendesk-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory Zendesku klienta](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) k získání těchto hodnot.

6. Zendesk očekává, že kontrolní výrazy SAML v určitém formátu. Neexistují žádné povinné atributy SAML, ale můžete volitelně přidat atribut z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](./media/zendesk-tutorial/i4-attribute.png)

7. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](./media/zendesk-tutorial/i2-attribute.png)

    ![image](./media/zendesk-tutorial/i3-attribute.png)
    
    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.
    
    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.
    
    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

    > [!NOTE]
    > Chcete-li přidat atributy, které nejsou ve službě Azure AD ve výchozím nastavení použijete atributy rozšíření. Klikněte na tlačítko [atributy uživatele, které je možné nastavit v SAML](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) zobrazíte úplný seznam atributů SAML, který **Zendesku** přijímá.

8. V SAML oddílu podpisového certifikátu, v **podpisový certifikát SAML** tématu, zkopírujte **kryptografický otisk**a uložte ho do počítače.

    ![image](./media/zendesk-tutorial/C3_certificate.png)

    a. Vyberte příslušnou možnost pro **podepisování možnost** v případě potřeby.

    b. Vyberte příslušnou možnost pro **podpisový algoritmus** v případě potřeby.

    c. Klikněte na **Uložit**.

9. Na **nastavení Zendesku** klikněte na tlačítko **zobrazit podrobný** otevřete **nakonfigurovat přihlašování** okna. Kopie následující adresy URL, z **Stručná referenční příručka oddílu.**

    Všimněte si, že adresa url může třeba následující:

    a. Adresa URL služby jednotného přihlašování SAML

    b. ID entity

    c. Adresa URL pro odhlášení

    ![image](./media/zendesk-tutorial/d1_saml.png) 

10. Existují dva způsoby ve kterých by šlo Zendesku - automatické a ruční.
  
11. K automatizaci konfigurace v rámci Zendesku, je potřeba nainstalovat **Moje aplikace zabezpečené přihlašování rozšíření prohlížeče** kliknutím **nainstalovat rozšíření**.

    ![image](./media/zendesk-tutorial/install_extension.png)

12. Po přidání rozšíření do prohlížeče, klikněte na **nastavení Zendesku** nasměruje na aplikace platformy Zendesk. Odtud zadejte přihlašovací údaje správce pro přihlášení do Zendesku. Rozšíření prohlížeče budou automaticky nakonfigurovat aplikaci za vás a automatizovat kroku 13.

     ![image](./media/zendesk-tutorial/d2_saml.png) 

13. Pokud chcete nastavit Zendesku ručně, otevřete nové okno webového prohlížeče a přihlaste na webu společnosti Zendesku jako správce a proveďte následující kroky:

    * Klikněte na tlačítko **správce**.

    * V levém navigačním podokně klikněte na tlačítko **nastavení**a potom klikněte na tlačítko **zabezpečení**.

    * Na **zabezpečení** stránce, proveďte následující kroky:

      ![Zabezpečení](././media/zendesk-tutorial/ic773089.png "zabezpečení")

      ![Jednotné přihlašování](././media/zendesk-tutorial/ic773090.png "jednotného přihlašování")

      a. Klikněte na tlačítko **agenti pro správu and** kartu.

      b. Vyberte **jednotné přihlašování (SSO) a SAML**a pak vyberte **SAML**.

      c. V **adresu URL jednotného přihlašování SAML** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.

      d. V **vzdálené adresy URL odhlašovací** textového pole vložte hodnotu **odhlašování URL** zkopírovanou z webu Azure portal.

      e. V **otisku certifikátu** vložit do textového pole **kryptografický otisk** hodnotu certifikát, který jste zkopírovali z portálu Azure portal.

      f. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![image](./media/zendesk-tutorial/d_users_and_groups.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![image](./media/zendesk-tutorial/d_adduser.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![image](./media/zendesk-tutorial/d_userproperties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.

### <a name="create-a-zendesk-test-user"></a>Vytvoření zkušebního uživatele Zendesku.

Cílem této části je vytvořte uživatele Britta Simon v systému Zendesk. Zendesk podporuje automatické zřizování uživatelů, což je ve výchozím nastavení povolená. Další podrobnosti můžete najít [tady](Zendesk-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

> [!NOTE]
> **Koncový uživatel** účty se zřizují automaticky při přihlášení. **Agent** a **správce** účty musí být ručně zřízené v **Zendesku** před přihlášením.

1. Přihlaste se k vaší **Zendesku** tenanta.

2. Vyberte **seznam zákazníků** kartu.

3. Vyberte **uživatele** kartu a klikněte na tlačítko **přidat**.

    ![Přidat uživatele](././media/zendesk-tutorial/ic773632.png "přidat uživatele")
4. Typ **název** a **e-mailu** existujícího účtu služby Azure AD ke zřízení a potom klikněte na tlačítko **Uložit**.

    ![Nový uživatel](././media/zendesk-tutorial/ic773633.png "nového uživatele")

> [!NOTE]
> Můžete použít jakékoli jiné Zendesku uživatele účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Zendesku uživatelským účtům, zřídit AAD.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k službě Zendesk.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![image](./media/zendesk-tutorial/d_all_applications.png)

2. V seznamu aplikací vyberte **Zendesku**.

    ![image](./media/zendesk-tutorial/d_all_zendeskapplications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![image](./media/zendesk-tutorial/d_leftpaneusers.png)

4. Vyberte **přidat** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![image](./media/zendesk-tutorial/d_assign_user.png)

4. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

5. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Zendesku na přístupovém panelu, vám by měl získat automaticky přihlášení k Zendesku aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace zřizování uživatelů](zendesk-provisioning-tutorial.md)
