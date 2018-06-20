---
title: 'Kurz: Azure Active Directory integrace s Boomi | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Boomi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: ee97eaf4624b1a34dbc66d1d4e8febab761b7b09
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221474"
---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>Kurz: Azure Active Directory integrace s Boomi

V tomto kurzu zjistěte, jak integrovat Boomi s Azure Active Directory (Azure AD).

Integrace Boomi s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Boomi.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Boomi (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Boomi, potřebujete následující položky:

- Předplatné služby Azure AD
- Boomi jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Boomi z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-boomi-from-the-gallery"></a>Přidání Boomi z Galerie
Při konfiguraci integrace Boomi do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Boomi z galerie.

**Pokud chcete přidat Boomi z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Boomi**, vyberte **Boomi** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Boomi v seznamu výsledků](./media/boomi-tutorial/tutorial_boomi_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Boomi podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Boomi je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Boomi musí navázat.

V Boomi, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Boomi, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Boomi](#create-a-boomi-test-user)**  – Pokud chcete mít protějšek Britta Simon v Boomi propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Boomi.

**Ke konfiguraci Azure AD jednotné přihlašování s Boomi, proveďte následující kroky:**

1. Na portálu Azure na **Boomi** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/boomi-tutorial/tutorial_boomi_samlbase.png)

3. Na **Boomi domény a adresy URL** část, proveďte následující kroky:

    ![Boomi domény a adresy URL jednotné přihlašování informace](./media/boomi-tutorial/tutorial_boomi_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL: `https://platform.boomi.com/`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://platform.boomi.com/sso/<boomi-tenant>/saml`

    > [!NOTE] 
    > Adresa URL odpovědi hodnota není skutečné. Aktualizujte hodnotu s skutečná adresa URL odpovědi. Obraťte se na [tým podpory Boomi](https://boomi.com/company/contact/) k získání hodnoty.
 
4. Aplikace Boomi očekává SAML kontrolní výrazy ve specifickém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z "**uživatelské atributy**" části na stránce integrace aplikace. Následující snímek obrazovky ukazuje příklad pro tento.
    
    ![Konfigurovat jednotné přihlašování](./media/boomi-tutorial/tutorial_attribute.png)

5. V **uživatelské atributy** části na **jednotného přihlašování** dialogu pro každý řádek v tabulce níže, proveďte následující kroky:

    | Název atributu | Hodnota atributu |
    | -------------- | --------------- |
    | FEDERATION_ID | User.Mail |
    
    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.
    
    ![Konfigurovat jednotné přihlašování](./media/boomi-tutorial/tutorial_officespace_04.png)
    
    ![Konfigurovat jednotné přihlašování](./media/boomi-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.
    
    d. Klikněte na tlačítko **OK**.

6. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/boomi-tutorial/tutorial_boomi_certificate.png) 

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/boomi-tutorial/tutorial_general_400.png)

8. Na **Boomi konfigurace** klikněte na tlačítko **konfigurace Boomi** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace Boomi](./media/boomi-tutorial/tutorial_boomi_configure.png) 

9. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti Boomi. 

10. Přejděte na **název společnosti** a přejděte na **nastavit**.

11. Klikněte **možnosti jednotného přihlašování k** kartě a proveďte následující kroky.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Zkontrolujte **povolit SAML jednotné přihlašování** zaškrtávací políčko.

    b. Klikněte na tlačítko **Import** na kterou odešlete certifikát stažený z Azure AD **certifikát zprostředkovatele Identity**.
    
    c. V **adresu URL pro přihlášení zprostředkovatele Identity** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** z okna konfigurace aplikace Azure AD.

    d. Jako **Federation Id umístění**, vyberte **federace je v elementu FEDERATION_ID atribut** přepínač. 

    e. Klikněte na tlačítko **Uložit** tlačítko.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/boomi-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/boomi-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/boomi-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/boomi-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-boomi-test-user"></a>Vytvoření zkušebního uživatele Boomi

Pokud chcete povolit uživatelům Azure AD přihlášení k Boomi, musí být zřízená do Boomi. V případě Boomi zřizování je ruční úloha.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>K poskytnutí uživatelského účtu, proveďte následující kroky:

1. Přihlaste se k serveru vaší společnosti Boomi jako správce.

2. Po přihlášení, přejděte na **Správa uživatelů** a přejděte na **uživatelé**.

    ![Uživatelé](./media/boomi-tutorial/tutorial_boomi_001.png "uživatelů")

3. Klikněte na tlačítko **+** ikonu a **role uživatele přidat/zachování** otevře se dialogové okno.

    ![Uživatelé](./media/boomi-tutorial/tutorial_boomi_002.png "uživatelů")

    ![Uživatelé](./media/boomi-tutorial/tutorial_boomi_003.png "uživatelů")

    a. V **e-mailová adresa uživatele** jako typ e-mailu uživatele k textovému poli, BrittaSimon@contoso.com.
    
    b. V **křestní jméno** textovému poli, název typu první uživatele jako Britta.

    c. V **příjmení** textovému poli, zadejte příjmení uživatele jako Simon.
    
    d. Zadejte uživatele **federace**. Každý uživatel musí mít ID federace, která jednoznačně identifikuje uživatele v rámci účtu.
    
    e. Přiřazení **standardní uživatel** role pro uživatele. Vzhledem k tomu, který by mu udělit normální prostředí přístup, jakož i přístup přihlašování není přiřadit role správce.
    
    f. Klikněte na **OK**.
    
    > [!NOTE]
    > Uživatel nebude přijímat úvodní oznamovací e-mail obsahující heslo, které lze použít k přihlášení k účtu AtomSphere, protože heslo je spravován pomocí zprostředkovatele identity. Může použít jakékoli jiné Boomi uživatele účtu vytvoření nástroje nebo rozhraní API poskytované Boomi zřídit AAD uživatelské účty.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Boomi.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Boomi, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Boomi**.

    ![V seznamu aplikací na Boomi odkaz](./media/boomi-tutorial/tutorial_boomi_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Boomi na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Boomi.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/boomi-tutorial/tutorial_general_01.png
[2]: ./media/boomi-tutorial/tutorial_general_02.png
[3]: ./media/boomi-tutorial/tutorial_general_03.png
[4]: ./media/boomi-tutorial/tutorial_general_04.png

[100]: ./media/boomi-tutorial/tutorial_general_100.png

[200]: ./media/boomi-tutorial/tutorial_general_200.png
[201]: ./media/boomi-tutorial/tutorial_general_201.png
[202]: ./media/boomi-tutorial/tutorial_general_202.png
[203]: ./media/boomi-tutorial/tutorial_general_203.png

