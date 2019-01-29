---
title: 'Kurz: Integrace Azure Active Directory s Dome9 oblouk | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Dome9 oblouk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2018
ms.author: jeedes
ms.openlocfilehash: a313acecf0660e527508f28e1ea86485996cc4f9
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55191393"
---
# <a name="tutorial-azure-active-directory-integration-with-dome9-arc"></a>Kurz: Integrace Azure Active Directory s Dome9 oblouk

V tomto kurzu se dozvíte, jak integrovat Dome9 oblouk s Azure Active Directory (Azure AD).

Integrace Dome9 oblouk s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Dome9 oblouk.
- Uživatele, aby automaticky získat přihlášení k Dome9 oblouku (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Dome9 Arc, potřebujete následující položky:

- Předplatné Azure AD
- Oblouk Dome9 jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Dome9 oblouk z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-dome9-arc-from-the-gallery"></a>Přidání Dome9 oblouk z Galerie

Konfigurace integrace Dome9 oblouku do služby Azure AD, budete muset přidat Dome9 oblouk na váš seznam spravovaných aplikací SaaS z galerie.

**Přidání Dome9 oblouk z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Dome9 oblouk**vyberte **Dome9 oblouk** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Dome9 oblouk v seznamu výsledků](./media/dome9arc-tutorial/tutorial_dome9arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Dome9 oblouk na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Dome9 oblouku je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Dome9 oblouk musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Dome9 Arc, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Dome9 oblouk](#create-a-dome9-arc-test-user)**  – Pokud chcete mít protějšek Britta Simon Dome9 Arc, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Dome9 oblouk.

**Ke konfiguraci Azure AD jednotné přihlašování s Dome9 Arc, proveďte následující kroky:**

1. Na webu Azure Portal na **Dome9 oblouk** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/dome9arc-tutorial/tutorial_dome9arc_samlbase.png)

3. Na **Dome9 oblouk domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Dome9 oblouk domény a adresy URL jednotného přihlašování – informace](./media/dome9arc-tutorial/tutorial_dome9arc_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL: `https://secure.dome9.com/`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > Hodnota názvu vaší společnosti vybere v dome9 portál pro správu, který je vysvětlen později v tomto kurzu.

4. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Dome9 oblouk domény a adresy URL jednotného přihlašování – informace](./media/dome9arc-tutorial/tutorial_dome9arc_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://secure.dome9.com/sso/saml/<yourcompanyname>`
 
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečná adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory Dome9 oblouk klienta](https://dome9.com/about/contact-us/) k získání těchto hodnot. 

5. Dome9 oblouk softwarová aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z "**atributy uživatele**" části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro tuto.

    ![Konfigurovat Single Sign-On attb](./media/dome9arc-tutorial/tutorial_dome9arc_attribute.png)

6. V **atributy uživatele** části na **jednotného přihlašování** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu  | Hodnota atributu | 
    | --------------- | --------------- | 
    | memberof | user.assignedroles |
    
    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurovat Single Sign-On přidat attb](./media/dome9arc-tutorial/tutorial_dome9_04.png)

    ![Konfigurovat Single Sign-On attb úpravy](./media/dome9arc-tutorial/tutorial_attribute_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. Klikněte na tlačítko **OK**.
    
    > [!NOTE]
    > Tomto [odkaz](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) o tom, jak konfigurovat a nastavit role pro aplikaci.

7. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/dome9arc-tutorial/tutorial_dome9arc_certificate.png) 

8. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/dome9arc-tutorial/tutorial_general_400.png)

9. Na **Dome9 oblouk konfigurace** klikněte na tlačítko **konfigurace oblouk Dome9** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace Dome9 oblouk](./media/dome9arc-tutorial/tutorial_dome9arc_configure.png) 

10. V okně jiný webový prohlížeč přihlaste jako správce serveru vaší společnosti Dome9 oblouk.

11. Klikněte na **nastavení profilu** v pravém horním rohu a pak klikněte na tlačítko **nastavení účtu**. 

    ![Konfigurace Dome9 oblouk](./media/dome9arc-tutorial/configure1.png)

12. Přejděte do **jednotného přihlašování** a potom klikněte na tlačítko **povolit**.

    ![Konfigurace Dome9 oblouk](./media/dome9arc-tutorial/configure2.png)

13. V části Konfigurace jednotného přihlašování proveďte následující kroky:

    ![Konfigurace Dome9 oblouk](./media/dome9arc-tutorial/configure3.png)

    a. Zadejte název společnosti **ID účtu** textového pole. Tato hodnota se použije v odpovědi, adresy url uvedené v části Adresa URL Azure portal.

    b. V **vystavitele** textového pole vložte hodnotu **SAML Entity ID**, který jste zkopírovali z webu Azure Portal.

    c. V **adresu url koncového bodu Idp** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z webu Azure Portal.

    d. Otevřete váš stažený certifikát kódovaný v Base64 v poznámkovém bloku, zkopírujte obsah ho do schránky a a vložte ho do **certifikát X.509** textového pole.

    e. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/dome9arc-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/dome9arc-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/dome9arc-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/dome9arc-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-dome9-arc-test-user"></a>Vytvoření zkušebního uživatele Dome9 oblouk

Pokud chcete povolit Azure AD uživatelům přihlášení na oblouk Dome9, musí být poskytnuty do aplikace. Dome9 oblouk podporuje just-in-time zřizování, ale to fungovalo správně, uživatel muset vybrat konkrétní **Role** a přiřaďte jiný uživatel.

   >[!Note]
   >Pro **Role** vytváření a další podrobnosti o kontaktu [tým podpory Dome9 oblouk klienta](https://dome9.com/about/contact-us/).

**Zřídit účet uživatele ručně, proveďte následující kroky:**

1. Přihlaste se na web společnosti Dome9 oblouk jako správce.

2. Klikněte na **uživatelů a rolí** a potom klikněte na tlačítko **uživatelé**.

    ![Přidat zaměstnance](./media/dome9arc-tutorial/user1.png)

3. Klikněte na tlačítko **přidat uživatele**.

    ![Přidat zaměstnance](./media/dome9arc-tutorial/user2.png)

4. V **vytvořit uživatele** části, proveďte následující kroky:

    ![Přidat zaměstnance](./media/dome9arc-tutorial/user3.png)

    a. V **e-mailu** , jako je textové pole, typ e-mailu uživatele Brittasimon@contoso.com.

    b. V **křestní jméno** textového pole zadejte jméno uživatele, jako je Britta.

    c. V **příjmení** textového pole zadejte příjmení uživatele, jako je Simon.

    d. Ujistěte se, **jednotného přihlašování uživatele** jako **na**.

    e. Klikněte na tlačítko **vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu na oblouk Dome9.

![Přiřazení role uživatele][200] 

**Přiřadit Dome9 oblouk Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Dome9 oblouk**.

    ![Odkaz Dome9 oblouk v seznamu aplikací](./media/dome9arc-tutorial/tutorial_dome9arc_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Dome9 oblouk na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Dome9 oblouk.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/dome9arc-tutorial/tutorial_general_01.png
[2]: ./media/dome9arc-tutorial/tutorial_general_02.png
[3]: ./media/dome9arc-tutorial/tutorial_general_03.png
[4]: ./media/dome9arc-tutorial/tutorial_general_04.png

[100]: ./media/dome9arc-tutorial/tutorial_general_100.png

[200]: ./media/dome9arc-tutorial/tutorial_general_200.png
[201]: ./media/dome9arc-tutorial/tutorial_general_201.png
[202]: ./media/dome9arc-tutorial/tutorial_general_202.png
[203]: ./media/dome9arc-tutorial/tutorial_general_203.png

