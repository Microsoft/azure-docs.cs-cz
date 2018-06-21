---
title: 'Kurz: Azure Active Directory integrace s ScaleX Enterprise | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ScaleX Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2379a8d-a659-45f1-87db-9ba156d83183
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.openlocfilehash: 18ac9fcf5a8b0c66d3c9e3d3b4e3064099e201c4
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287605"
---
# <a name="tutorial-azure-active-directory-integration-with-scalex-enterprise"></a>Kurz: Azure Active Directory integrace s ScaleX Enterprise

V tomto kurzu zjistěte, jak integrovat ScaleX Enterprise s Azure Active Directory (Azure AD).

Integrace ScaleX Enterprise s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup do firemní sítě ScaleX
- Můžete povolit uživatelům, aby automaticky získat přihlášení k ScaleX Enterprise (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, přečtěte si téma. Co je přístup k aplikaci a jednotné přihlašování s [Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ScaleX Enterprise, potřebujete následující položky:

- Předplatné služby Azure AD
- ScaleX Enterprise jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud je to nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání ScaleX Enterprise z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-scalex-enterprise-from-the-gallery"></a>Přidání ScaleX Enterprise z Galerie
Při konfiguraci integrace ScaleX organizace v Azure AD, potřebujete přidat ScaleX Enterprise z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat ScaleX Enterprise z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Klikněte na tlačítko **přidat** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **ScaleX Enterprise**.

    ![Vytváření testovacího uživatele Azure AD](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_search.png)

5. Na panelu výsledků vyberte **ScaleX Enterprise**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurujete a testovací Azure AD jednotného přihlašování k ScaleX organizace podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v ScaleX Enterprise je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatele v podniku ScaleX musí navázat.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** ve ScaleX podniku.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování k ScaleX organizace, budete muset provést následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele ScaleX Enterprise](#creating-a-scalex-enterprise-test-user)**  – Pokud chcete mít protějšek Britta Simon v ScaleX organizace, která je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci ScaleX Enterprise.

**Ke konfiguraci Azure AD jednotného přihlašování k ScaleX organizace, proveďte následující kroky:**

1. Na portálu Azure na **ScaleX Enterprise** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogové okno, jako **režimu** vyberte **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_samlbase.png)

3. Na **ScaleX podnikové domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Konfigurovat jednotné přihlašování](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_url1.png)

    a. V **identifikátor** textovému poli, zadejte hodnotu pomocí následujícího vzorce: `https://platform.rescale.com/saml2/<company id>/`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://platform.rescale.com/saml2/<company id>/acs/`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL**, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Konfigurovat jednotné přihlašování](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_url2.png)

    V **přihlašovací adresa URL** textovému poli, zadejte hodnotu pomocí následujícího vzorce: `https://platform.rescale.com/saml2/<company id>/sso/`
     
    > [!NOTE] 
    > Tyto nejsou skutečné hodnoty. Tyto hodnoty aktualizujte pomocí skutečného identifikátoru, adresa URL odpovědi nebo přihlašovací adresa URL. Obraťte se na [tým podpory klient systému Enterprise ScaleX](http://info.rescale.com/contact_sales) k získání těchto hodnot. 

5. Aplikace ScaleX očekává SAML kontrolní výrazy ve specifickém formátu, který vyžaduje, abyste upravit mapování vlastních atributů ke konfiguraci atributy tokenu SAML. Klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** chcete otevřít vlastní atributy nastavení.

    ![Konfigurovat jednotné přihlašování](./media/scalexenterprise-tutorial/scalex_attributes.png)
    
    a. Klikněte pravým tlačítkem na atribut **název** a klikněte na příkaz odstranit.

    ![Konfigurovat jednotné přihlašování](./media/scalexenterprise-tutorial/delete_attribute_name.png)

    b. Klikněte na tlačítko **emailaddress** atribut a otevřete okno Úprava atributů. Změnit svou hodnotu z **user.mail** k **user.userprincipalname** a klikněte na tlačítko Ok.

    ![Konfigurovat jednotné přihlašování](./media/scalexenterprise-tutorial/edit_email_attribute.png) 
    
5. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/scalexenterprise-tutorial/tutorial_general_400.png)
    
7. Na **ScaleX podniková konfigurace** klikněte na tlačítko **konfigurace ScaleX Enterprise** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID** a **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_configure.png) 

8. Konfigurace jednotného přihlašování na **ScaleX Enterprise** straně, přihlášení na web společnosti ScaleX Enterprise jako správce.

9. Klikněte na nabídku v horním pravém rohu a vyberte **Contoso správy**.

    > [!NOTE] 
    > Contoso je jenom jako příklad. To by měl být skutečný název vaší společnosti. 

    ![Konfigurovat jednotné přihlašování](./media/scalexenterprise-tutorial/Test_Admin.png) 

10. Vyberte **integrace** z horní nabídce a vyberte **jednotné přihlašování**.

    ![Konfigurovat jednotné přihlašování](./media/scalexenterprise-tutorial/admin_sso.png) 

11. Vyplňte formulář následujícím způsobem:

    ![Konfigurovat jednotné přihlašování](./media/scalexenterprise-tutorial/scalex_admin_save.png) 
    
    a. Vyberte **"Vytvořit každý uživatel, který může ověřit pomocí jednotného přihlašování."**

    b. **Poskytovatel služeb saml**: Vložit hodnotu ***urn: oasis: názvy: tc: SAML:2.0:nameid-formátu: trvalé***

    c. **Název pole zprostředkovatele Identity e-mailu v odpovědi ACS**: Vložit hodnotu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **ID EntityDescriptor Entity zprostředkovatele identity:** vložení **SAML Entity ID** hodnota zkopírována z portálu Azure.

    e. **Adresa URL SingleSignOnService zprostředkovatele identity:** vložení **SAML jeden přihlašování adresa URL služby** z portálu Azure.

    f. **Certifikát identity poskytovatele veřejných X509:** otevřete X509 certifikát stáhne z Azure v poznámkovém bloku a vložit obsah v tomto poli. Zajistěte, že žádná zalomení uprostřed obsahu certifikátu.
    
    g. Zkontrolujte následující políček: **povoleno, šifrování NameID a AuthnRequests přihlášení.**

    h. Klikněte na tlačítko **nastavení jednotného přihlašování k aktualizaci** uložte nastavení.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/scalexenterprise-tutorial/create_aaduser_01.png) 

2. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.
    
    ![Vytváření testovacího uživatele Azure AD](./media/scalexenterprise-tutorial/create_aaduser_02.png) 

3. V horní části okna klikněte na položku **přidat** otevřete **uživatele** dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/scalexenterprise-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/scalexenterprise-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-scalex-enterprise-test-user"></a>Vytvoření zkušebního uživatele ScaleX Enterprise

Pokud chcete povolit uživatelům Azure AD přihlášení do firemní sítě ScaleX, se musí být zřízená v do firemní sítě ScaleX. V případě ScaleX Enterprise zřizování je automatická úloha a nejsou potřeba žádné ruční kroky. Každý uživatel, který může úspěšně ověřit pomocí přihlašovacích údajů jednotné přihlašování se automaticky zřídí na straně ScaleX.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování tak, že udělíte přístup uživatelů k ScaleX Enterprise.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon ScaleX Enterprise, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **ScaleX Enterprise**.

    ![Konfigurovat jednotné přihlašování](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.

### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Klikněte na dlaždici ScaleX Enterprise na přístupovém panelu, můžete se získat automaticky přihlášení k aplikaci ScaleX Enterprise. Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/scalexenterprise-tutorial/tutorial_general_01.png
[2]: ./media/scalexenterprise-tutorial/tutorial_general_02.png
[3]: ./media/scalexenterprise-tutorial/tutorial_general_03.png
[4]: ./media/scalexenterprise-tutorial/tutorial_general_04.png

[100]: ./media/scalexenterprise-tutorial/tutorial_general_100.png

[200]: ./media/scalexenterprise-tutorial/tutorial_general_200.png
[201]: ./media/scalexenterprise-tutorial/tutorial_general_201.png
[202]: ./media/scalexenterprise-tutorial/tutorial_general_202.png
[203]: ./media/scalexenterprise-tutorial/tutorial_general_203.png

