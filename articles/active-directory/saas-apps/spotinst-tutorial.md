---
title: 'Kurz: Integrace Azure Active Directory s Spotinst | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Spotinst.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 679284783ac93662c1c96e813f9f028fffb434f0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56173653"
---
# <a name="tutorial-azure-active-directory-integration-with-spotinst"></a>Kurz: Integrace Azure Active Directory s Spotinst

V tomto kurzu se dozvíte, jak integrovat Spotinst s Azure Active Directory (Azure AD).

Spotinst integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Spotinst.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Spotinst (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Spotinst, potřebujete následující položky:

- Předplatné Azure AD
- Spotinst jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Spotinst z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-spotinst-from-the-gallery"></a>Přidání Spotinst z Galerie
Konfigurace integrace Spotinst do služby Azure AD, budete muset přidat Spotinst z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Spotinst z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Spotinst**vyberte **Spotinst** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Spotinst v seznamu výsledků](./media/spotinst-tutorial/tutorial_spotinst_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Spotinst podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Spotinst je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Spotinst potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Spotinst, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Spotinst](#create-a-spotinst-test-user)**  – Pokud chcete mít protějšek Britta Simon Spotinst, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Spotinst.

**Ke konfiguraci Azure AD jednotné přihlašování s Spotinst, proveďte následující kroky:**

1. Na webu Azure Portal na **Spotinst** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/spotinst-tutorial/tutorial_spotinst_samlbase.png)

3. Na **Spotinst domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v režimu iniciované zprostředkovatele identity:

    ![Spotinst domény a adresy URL jednotného přihlašování – informace](./media/spotinst-tutorial/tutorial_spotinst_url1.png)

    a. Zkontrolujte **zobrazit pokročilé nastavení URL**.

    b. V **stav přenosu** textového pole zadejte hodnotu: `<ID>`

    c. Pokud chcete nakonfigurovat aplikace v **SP** zahájeno v režimu **přihlašovací adresa URL** textového pole zadejte adresu URL: `https://console.spotinst.com`

    > [!NOTE]
    > Stav přenosu hodnota není skutečný. Stav přenosu hodnota aktualizuje o skutečnou hodnotu stav přenosu, který je vysvětlen později v tomto kurzu.

4. Spotinst aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro něj.

    ![Konfigurace jednotného přihlašování](./media/spotinst-tutorial/tutorial_Spotinst_attribute.png)

5. V **atributy uživatele** části na **jednotného přihlašování** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    | Název atributu | Hodnota atributu |
    | ---------------| --------------- |
    | Email | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |
    
    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/spotinst-tutorial/tutorial_attribute_04.png)

    ![Konfigurace jednotného přihlašování](./media/spotinst-tutorial/tutorial_attribute_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. Nechte **Namespace** prázdné.

    e. Klikněte na tlačítko **Ok**

6. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/spotinst-tutorial/tutorial_spotinst_certificate.png) 

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/spotinst-tutorial/tutorial_general_400.png)

8. V jiné okno webového prohlížeče, přihlaste se k Spotinst jako správce zabezpečení.

9. Klikněte na **ikonu uživatele** v horní pravé straně obrazovky a klikněte na tlačítko **nastavení**.

    ![Nastavení Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

10. Klikněte na **zabezpečení** kartě v horní části a pak vyberte **zprostředkovatelé Identity** a proveďte následující kroky:

    ![Spotinst zabezpečení](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Kopírovat **stav přenosu** hodnotu pro vaši instanci a vložte ji **stav přenosu** textového pole v **Spotinst domény a adresy URL** části na webu Azure portal.

    b. Klikněte na tlačítko **Procházet** nahrát soubor metadat xml, který jste si stáhli z webu Azure portal

    c. Klikněte na **ULOŽIT**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/spotinst-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/spotinst-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/spotinst-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/spotinst-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-spotinst-test-user"></a>Vytvoření zkušebního uživatele Spotinst

Cílem této části je vytvořte uživatele Britta Simon v Spotinst.

1. Pokud jste nakonfigurovali v aplikaci **SP** intiated režimu, proveďte následující kroky:

   a. V jiné okno webového prohlížeče, přihlaste se k Spotinst jako správce zabezpečení.

   b. Klikněte na **ikonu uživatele** v horní pravé straně obrazovky a klikněte na tlačítko **nastavení**.

    ![Nastavení Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Klikněte na tlačítko **uživatelé** a vyberte **přidat uživatele**.

    ![Nastavení Spotinst](./media/spotinst-tutorial/adduser1.png)

    d. V části přidat uživatele proveďte následující kroky:

    ![Nastavení Spotinst](./media/spotinst-tutorial/adduser2.png)

    * V **jméno a příjmení** textového pole zadejte celé jméno uživatele jako **BrittaSimon**.

    * V **e-mailu** textového pole zadejte e-mailovou adresu uživatele, jako je **brittasimon@contoso.com**.

    * Vyberte podrobnosti specifické pro organizaci **organizace Role, Role účtu a účty**.

2. Pokud jste nakonfigurovali v aplikaci **IDP** intiated režim, zde je položky žádná akce vám v této části. Spotinst podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Nový uživatel se vytvoří během pokusu o přístup k Spotinst, pokud ještě neexistuje.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Spotinst použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit Spotinst Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Spotinst**.

    ![Odkaz Spotinst v seznamu aplikací](./media/spotinst-tutorial/tutorial_spotinst_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Spotinst na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Spotinst.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/spotinst-tutorial/tutorial_general_01.png
[2]: ./media/spotinst-tutorial/tutorial_general_02.png
[3]: ./media/spotinst-tutorial/tutorial_general_03.png
[4]: ./media/spotinst-tutorial/tutorial_general_04.png

[100]: ./media/spotinst-tutorial/tutorial_general_100.png

[200]: ./media/spotinst-tutorial/tutorial_general_200.png
[201]: ./media/spotinst-tutorial/tutorial_general_201.png
[202]: ./media/spotinst-tutorial/tutorial_general_202.png
[203]: ./media/spotinst-tutorial/tutorial_general_203.png

