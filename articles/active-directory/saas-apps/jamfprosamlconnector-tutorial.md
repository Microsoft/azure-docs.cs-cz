---
title: 'Kurz: Integrace Azure Active Directory s Jamf Pro | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: d28e28a2c4f8144da16c4838f07c9b8bb5ce67f0
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268153"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>Kurz: Integrace Azure Active Directory s Jamf Pro

V tomto kurzu se dozvíte, jak integrace Jamf Pro s Azure Active Directory (Azure AD).

Integrace Jamf Pro s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Jamf Pro.
- Uživatele, aby automaticky získat přihlášení k Jamf Pro (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Jamf Pro, potřebujete následující položky:

- Předplatné Azure AD
- Jamf Pro jedno přihlášení povolený předplatné

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Jamf Pro přidání z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-jamf-pro-from-the-gallery"></a>Jamf Pro přidání z Galerie

Konfigurace integrace Jamf Pro do služby Azure AD, budete muset přidat Jamf Pro do seznamu spravovaných aplikací SaaS z galerie.

**Chcete-li přidat Jamf Pro z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![image](./media/jamfprosamlconnector-tutorial/selectazuread.png)

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![image](./media/jamfprosamlconnector-tutorial/a_select_app.png)
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![image](./media/jamfprosamlconnector-tutorial/a_new_app.png)

4. Do vyhledávacího pole zadejte **Jamf Pro**vyberte **Jamf Pro** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![image](./media/jamfprosamlconnector-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a test Azure AD jednotné přihlašování s Jamf Pro podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Jamf Pro je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Jamf Pro je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Jamf Pro, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Jamf Pro](#create-a-jamf-pro-test-user)**  – Pokud chcete mít protějšek Britta Simon v Jamf Pro, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Jamf Pro.

**Ke konfiguraci Azure AD jednotné přihlašování s Jamf Pro, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com/)na **Jamf Pro** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![image](./media/jamfprosamlconnector-tutorial/b1_b2_select_sso.png)

2. Klikněte na tlačítko **režim změnit jednotného přihlašování** nad obrazovky, vyberte **SAML** režimu.

      ![image](./media/jamfprosamlconnector-tutorial/b1_b2_saml_ssso.png)

3. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![image](./media/jamfprosamlconnector-tutorial/b1_b2_saml_sso.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **základní konfiguraci SAML** dialogového okna.

    ![image](./media/jamfprosamlconnector-tutorial/b1-domains_and_urlsedit.png)

5. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.jamfcloud.com/saml/metadata`.

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.jamfcloud.com/saml/SSO`.

    ![image](./media/jamfprosamlconnector-tutorial//b2-domains_and_urls.png)

    c. Klikněte na tlačítko **nastavit další adresy URL**.

    d. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.jamfcloud.com`.

    ![image](./media/jamfprosamlconnector-tutorial//b4-domains_and_urls.png)

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Zobrazí se skutečná hodnota identifikátoru z **Single Sign-On** části portálu Jamf Pro, který je vysvětlen později v tomto kurzu. Můžete rozbalit skutečnou **subdoménu** hodnoty z hodnoty identifikátor, který budete používat **subdoménu** informace v přihlašovací adresu URL a adresy URL odpovědi.

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a uložte ji na vaše počítač.

    ![image](./media/jamfprosamlconnector-tutorial/C2_certificate.png)

7. K automatizaci konfigurace v Jamf Pro, je potřeba nainstalovat **Moje aplikace zabezpečené přihlašování rozšíření prohlížeče** kliknutím **nainstalovat rozšíření**.

    ![image](./media/jamfprosamlconnector-tutorial/install_extension.png)
 
8. Po přidání rozšíření do prohlížeče, klikněte na **nastavení Jamf Pro** nasměruje na aplikaci Jamf Pro. Odtud zadejte přihlašovací údaje správce pro přihlášení do Jamf Pro. Rozšíření prohlížeče budou automaticky nakonfigurovat aplikaci za vás a automatizovat kroky 9-12.

    ![image](./media/jamfprosamlconnector-tutorial/d1_saml.png)

9. Pokud chcete nastavit Jamf Pro ručně, otevřete nové okno webového prohlížeče a protokolu Jamf Pro web společnosti jako správce a proveďte následující kroky:

10. Klikněte na **ikona nastavení** v pravém horním rohu stránky.

    ![Konfigurace Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

11. Klikněte na **jednotné přihlašování**.

    ![Konfigurace Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

12. Na **Single Sign-On** stránku, proveďte následující kroky:

    ![Jeden Jamf Pro](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Vyberte **Jamf Pro Server** umožňuje jednotné přihlašování přístup.

    b. Výběrem **povolit jednorázové přihlášení pro všechny uživatele** uživatelé nebudou přesměrováni na stránku pro přihlášení zprostředkovatele Identity pro ověřování, ale můžete přihlásit k Jamf Pro přímo místo. Když se uživatel pokusí o přístup k Jamf Pro pomocí zprostředkovatele Identity, dojde k autorizaci a ověřování jednotného přihlašování zahájené pomocí IdP.

    c. Vyberte **NameID** možnost **mapování uživatelů: SAML**. Ve výchozím nastavení, toto nastavení nastavené na **NameID** ale můžete definovat vlastní atribut.

    d. Vyberte **e-mailu** pro **mapování uživatelů: JAMF PRO**. Jamf Pro mapování atributů SAML odeslán ve zprostředkovateli identity následujícími způsoby: uživatelé a skupiny. Když se uživatel pokusí o přístup k Jamf Pro, ve výchozím nastavení Jamf Pro získá informace o uživateli od zprostředkovatele Identity a shoduje s Jamf Pro uživatelské účty. Pokud příchozí uživatelský účet v Jamf Pro neexistuje, pak odpovídající název skupiny vyvolá.

    e. Vložte hodnotu `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` v **název ATRIBUTU skupiny** textového pole.

13. Na stejné stránce procházejte až **zprostředkovatele IDENTITY** pod **Single Sign-On** části a proveďte následující kroky:

    ![Konfigurace Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Vyberte **jiných** jako možnost z **zprostředkovatele IDENTITY** rozevíracího seznamu.

    b. V **ostatní poskytovatele** textového pole zadejte **Azure AD**.

    c. Vyberte **adresa URL metadat** jako možnost z **zdroj METADAT zprostředkovatele IDENTITY** rozevíracího seznamu a v následujícím textovém poli, vložte **adresa Url federačních metadat aplikace** hodnotu, která jste zkopírovali z portálu Azure portal.

    d. Kopírovat **Entity ID** hodnotu a vložte ho do **identifikátor (Entity ID)** textového pole v **Jamf Pro domény a adresy URL** části na webu Azure portal.

    >[!NOTE]
    > Tady rozmazaný hodnota je součástí subdomény. K dokončení přihlašovací adresu URL a adresy URL odpovědi v použít tuto hodnotu **Jamf Pro domény a adresy URL** části na webu Azure portal.

    e. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![image](./media/jamfprosamlconnector-tutorial/d_users_and_groups.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![image](./media/jamfprosamlconnector-tutorial/d_adduser.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![image](./media/jamfprosamlconnector-tutorial/d_userproperties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.

### <a name="create-a-jamf-pro-test-user"></a>Vytvoření zkušebního uživatele Jamf Pro

Přihlaste se k Jamf Pro Azure AD uživatelům umožnit, musí být poskytnuty do Jamf Pro. V případě Jamf Pro zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se na web společnosti Jamf Pro jako správce.

2. Klikněte na **ikona nastavení** v pravém horním rohu stránky.

    ![Přidat zaměstnance](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Klikněte na **Jamf Pro uživatelské účty a skupiny**.

    ![Přidat zaměstnance](./media/jamfprosamlconnector-tutorial/user1.png)

4. Klikněte na možnost **Nové**.

    ![Přidat zaměstnance](./media/jamfprosamlconnector-tutorial/user2.png)

5. Vyberte **vytvořit standardní účet**.

    ![Přidat zaměstnance](./media/jamfprosamlconnector-tutorial/user3.png)

6. Na **nový účet** dailog, proveďte následující kroky:

    ![Přidat zaměstnance](./media/jamfprosamlconnector-tutorial/user4.png)

    a. V **uživatelské jméno** textového pole zadejte úplný název BrittaSimon.

    b. Vyberte příslušné možnosti podle vaší organizaci pro **úroveň přístupu**, **oprávnění NASTAVENA**a pro **stav přístupu**.

    c. V **jméno a příjmení** textového pole zadejte úplný název Britta Simon.

    d. V **e-MAILOVOU adresu** textového pole zadejte e-mailovou adresu účtu Britta Simon.

    e. V **heslo** textového pole zadejte heslo uživatele.

    f. V **OVĚŘTE heslo** textového pole zadejte heslo uživatele.

    g. Klikněte na **Uložit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Jamf Pro.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Jamf Pro**.

    ![image](./media/jamfprosamlconnector-tutorial/d_all_applications.png)

2. V seznamu aplikací vyberte **Jamf Pro**.

    ![image](./media/jamfprosamlconnector-tutorial/d_all_proapplications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![image](./media/jamfprosamlconnector-tutorial/d_leftpaneusers.png)

4. Vyberte **přidat** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![image](./media/jamfprosamlconnector-tutorial/d_assign_user.png)

4. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

5. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Jamf Pro na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Jamf Pro.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
