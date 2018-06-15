---
title: 'Kurz: Azure Active Directory integrace s Dome9 oblouk | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Dome9 oblouk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: de164f6a439fd68b4746dc96c43ff5cb0740ffe8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34344142"
---
# <a name="tutorial-azure-active-directory-integration-with-dome9-arc"></a>Kurz: Azure Active Directory integrace s Dome9 oblouk

V tomto kurzu zjistěte, jak integrovat Dome9 oblouk s Azure Active Directory (Azure AD).

Integrace Dome9 oblouk s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup na oblouk Dome9.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Dome9 oblouk (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Dome9 oblouk, potřebujete následující položky:

- Předplatné služby Azure AD
- Oblouk Dome9 jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Dome9 oblouk z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-dome9-arc-from-the-gallery"></a>Přidání Dome9 oblouk z Galerie
Při konfiguraci integrace Dome9 oblouku do služby Azure AD potřebujete přidat Dome9 oblouk z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Dome9 oblouk z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Dome9 oblouk**, vyberte **Dome9 oblouk** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Dome9 oblouk v seznamu výsledků](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Dome9 oblouk podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Dome9 oblouk je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Dome9 oblouk musí navázat.

V Dome9 oblouk přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Dome9 oblouk, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele oblouk Dome9](#create-a-dome9-arc-test-user)**  – Pokud chcete mít protějšek Britta Simon v Dome9 oblouk propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Dome9 oblouk.

**Ke konfiguraci Azure AD jednotné přihlašování s Dome9 oblouk, proveďte následující kroky:**

1. Na portálu Azure na **Dome9 oblouk** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_samlbase.png)

3. Na **Dome9 oblouk domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Dome9 oblouk domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL: `https://secure.dome9.com/`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > Vyberete hodnotu název vaší společnosti na portálu správy dome9, který je vysvětlen později v tomto kurzu.

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Dome9 oblouk domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://secure.dome9.com/sso/saml/<yourcompanyname>`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečná adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [tým podpory Dome9 oblouk klienta](https://dome9.com/about/contact-us/) k získání těchto hodnot. 

5. Dome9 oblouk softwarová aplikace očekává SAML kontrolní výrazy ve specifickém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z "**uživatelské atributy**" části na stránce integrace aplikace. Následující snímek obrazovky ukazuje příklad pro tento.

    ![Konfigurovat jednotné přihlašování attb](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_attribute.png)

6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu  | Hodnota atributu | 
    | --------------- | --------------- | 
    | memberof | user.assignedroles | 
    
    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování přidat attb](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9_04.png)

    ![Konfigurovat jednotné přihlašování attb úpravy](./media/active-directory-saas-dome9arc-tutorial/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.
    
    d. Klikněte na tlačítko **OK**.

7. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_certificate.png) 

8. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-dome9arc-tutorial/tutorial_general_400.png)
    
9. Na **Dome9 oblouk konfigurace** klikněte na tlačítko **konfigurace oblouk Dome9** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresu URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace Dome9 oblouk](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_configure.png) 

10. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti Dome9 oblouk.

11. Klikněte na **nastavení profilu** v pravém horním rohu a pak klikněte na tlačítko **nastavení účtu**. 

    ![Konfigurace Dome9 oblouk](./media/active-directory-saas-dome9arc-tutorial/configure1.png)

12. Přejděte na **jednotného přihlašování k** a pak klikněte na **povolit**.

    ![Konfigurace Dome9 oblouk](./media/active-directory-saas-dome9arc-tutorial/configure2.png)

13. V části Konfigurace jednotného přihlašování proveďte následující kroky:

    ![Konfigurace Dome9 oblouk](./media/active-directory-saas-dome9arc-tutorial/configure3.png)

    a. Zadejte název společnosti **ID účtu** textové pole. Tato hodnota se použije v odpovědi na adresy url uvedené v části Adresa URL Azure portálu.

    b. V **vystavitele** textovému poli, vložte hodnotu **SAML Entity ID**, které jste zkopírovali formulář portálu Azure.

    c. V **adresu url koncového bodu Idp** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, které jste zkopírovali formulář portálu Azure.

    d. V poznámkovém bloku otevřete váš stažený certifikát kódovaný v Base64, zkopírujte obsah ho do schránky a vložte jej do **certifikát X.509** textové pole.

    e. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-dome9-arc-test-user"></a>Vytvoření zkušebního uživatele Dome9 oblouk

Pokud chcete povolit uživatelům Azure AD přihlášení na oblouk Dome9, musí být zřízená do aplikace. Dome9 oblouk podporuje zřizování za běhu, ale fungovalo správně, takže uživatel muset vybrat konkrétní **Role** a uživateli přiřadit stejné.

   >[!Note] 
   >Pro **Role** vytvoření a další podrobnosti, obraťte se na [tým podpory Dome9 oblouk klienta](https://dome9.com/about/contact-us/).

**K poskytnutí uživatelského účtu ručně, proveďte následující kroky:**

1. Přihlaste se k serveru vaší společnosti Dome9 oblouk jako správce.

2. Klikněte na **uživatelé a role** a pak klikněte na **uživatelé**.

    ![Můžete přidat zaměstnance](./media/active-directory-saas-dome9arc-tutorial/user1.png)

3. Klikněte na tlačítko **přidat uživatele**.

    ![Můžete přidat zaměstnance](./media/active-directory-saas-dome9arc-tutorial/user2.png)

4. V **vytvořit uživatele** část, proveďte následující kroky:
    
    ![Můžete přidat zaměstnance](./media/active-directory-saas-dome9arc-tutorial/user3.png)

    a. V **e-mailu** jako typ e-mailu uživatele k textovému poli, Brittasimon@contoso.com.

    b. V **křestní jméno** textovému poli, typ křestní jméno uživatele, jako je Britta.

    c. V **příjmení** textovému poli, zadejte příjmení uživatele jako Simon.

    d. Ujistěte se, **jednotné přihlašování uživatele** jako **na**.

    e. Klikněte na tlačítko **vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat tak, že udělíte přístup na oblouk Dome9 Azure jednotné přihlašování.

![Přiřadit role uživatele][200] 

**Přiřadit Britta Simon na oblouk Dome9, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Dome9 oblouk**.

    ![V seznamu aplikací na odkaz Dome9 oblouk](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Dome9 oblouk na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Dome9 oblouk.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_203.png

