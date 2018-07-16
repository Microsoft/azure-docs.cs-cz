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
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: e5ef74329e2adb6f3b8b60f547231a245a03b1fe
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39050591"
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

- S předplatným služby Azure AD
- Zendesku jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Zendesku z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-zendesk-from-the-gallery"></a>Přidání Zendesku z Galerie
Pokud chcete nakonfigurovat integraci Zendesku do služby Azure AD, budete muset přidat Zendesku z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Zendesku z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Zendesku**vyberte **Zendesku** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Zendesku v seznamu výsledků](./media/zendesk-tutorial/tutorial_zendesk_addfromgallery.png)

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

1. Na webu Azure Portal na **Zendesku** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/zendesk-tutorial/tutorial_zendesk_samlbase.png)

3. Na **Zendesku domény a adresy URL** části, proveďte následující kroky:

    ![Zendesk domény a adresy URL jednotného přihlašování – informace](./media/zendesk-tutorial/tutorial_zendesk_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.zendesk.com`

    b. V **identifikátor** textového pole zadejte hodnotu pomocí následujícího vzorce: `<subdomain>.zendesk.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory Zendesku klienta](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) k získání těchto hodnot.

4. Na **podpisový certifikát SAML** tématu, zkopírujte **kryptografický OTISK** hodnota certifikátu.

    ![Odkaz ke stažení certifikátu](./media/zendesk-tutorial/tutorial_zendesk_certificate.png)

5. Zendesk očekává, že kontrolní výrazy SAML v určitém formátu. Neexistují žádné povinné atributy SAML, ale můžete volitelně přidat atribut z **atributy uživatele** části podle následujících kroků: 

     ![Konfigurace jednotného přihlašování](./media/zendesk-tutorial/tutorial_zendesk_attributes1.png)

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurovat Single Sign-On přidat](./media/zendesk-tutorial/tutorial_attribute_04.png)

    ![Konfigurovat Single Sign-On addattb](./media/zendesk-tutorial/tutorial_attribute_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.
    
    d. Klikněte na tlačítko **OK**.

    > [!NOTE]
    > Chcete-li přidat atributy, které nejsou ve službě Azure AD ve výchozím nastavení použijete atributy rozšíření. Klikněte na tlačítko [atributy uživatele, které je možné nastavit v SAML](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) zobrazíte úplný seznam atributů SAML, který **Zendesku** přijímá.

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/zendesk-tutorial/tutorial_general_400.png)

7. Na **Zendesku konfigurace** klikněte na tlačítko **nakonfigurovat Zendesku** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace systému Zendesk](./media/zendesk-tutorial/tutorial_zendesk_configure.png) 

8. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti Zendesku.

9. Klikněte na tlačítko **správce**.

10. V levém navigačním podokně klikněte na tlačítko **nastavení**a potom klikněte na tlačítko **zabezpečení**.

11. Na **zabezpečení** stránce, proveďte následující kroky: 

     ![Zabezpečení](./media/zendesk-tutorial/ic773089.png "zabezpečení")

    ![Jednotné přihlašování](./media/zendesk-tutorial/ic773090.png "jednotného přihlašování")

     a. Klikněte na tlačítko **agenti pro správu and** kartu.

     b. Vyberte **jednotné přihlašování (SSO) a SAML**a pak vyberte **SAML**.

     c. V **adresu URL jednotného přihlašování SAML** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal. 

     d. V **vzdálené adresy URL odhlašovací** textového pole vložte hodnotu **odhlašování URL** zkopírovanou z webu Azure portal.

     e. V **otisku certifikátu** vložit do textového pole **kryptografický otisk** hodnotu certifikát, který jste zkopírovali z portálu Azure portal.

     f. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/zendesk-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/zendesk-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/zendesk-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/zendesk-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-zendesk-test-user"></a>Vytvoření zkušebního uživatele Zendesku.

Cílem této části je vytvořte uživatele Britta Simon v systému Zendesk. Zendesk podporuje automatické zřizování uživatelů, což je ve výchozím nastavení povolená. Další podrobnosti můžete najít [tady](zendesk-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

> [!NOTE]
> **Koncový uživatel** účty se zřizují automaticky při přihlášení. **Agent** a **správce** účty musí být ručně zřízené v **Zendesku** před přihlášením.

1. Přihlaste se k vaší **Zendesku** tenanta.

2. Vyberte **seznam zákazníků** kartu.

3. Vyberte **uživatele** kartu a klikněte na tlačítko **přidat**.

    ![Přidat uživatele](./media/zendesk-tutorial/ic773632.png "přidat uživatele")
4. Typ **název** a **e-mailu** existujícího účtu služby Azure AD ke zřízení a potom klikněte na tlačítko **Uložit**.

    ![Nový uživatel](./media/zendesk-tutorial/ic773633.png "nového uživatele")

> [!NOTE]
> Můžete použít jakékoli jiné Zendesku uživatele účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Zendesku uživatelským účtům, zřídit AAD.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k službě Zendesk.

![Přiřazení role uživatele][200]

**Britta Simon přiřadit Zendesku, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **Zendesku**.

    ![Propojení Zendesku v seznamu aplikací](./media/zendesk-tutorial/tutorial_zendesk_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Zendesku na přístupovém panelu, vám by měl získat automaticky přihlášení k Zendesku aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace zřizování uživatelů](zendesk-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
[4]: ./media/zendesk-tutorial/tutorial_general_04.png

[100]: ./media/zendesk-tutorial/tutorial_general_100.png

[200]: ./media/zendesk-tutorial/tutorial_general_200.png
[201]: ./media/zendesk-tutorial/tutorial_general_201.png
[202]: ./media/zendesk-tutorial/tutorial_general_202.png
[203]: ./media/zendesk-tutorial/tutorial_general_203.png
