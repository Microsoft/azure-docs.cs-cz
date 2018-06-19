---
title: 'Kurz: Azure Active Directory integrace s Druva | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Druva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2017
ms.author: jeedes
ms.openlocfilehash: 65633fc083798157f949b90c8424e656fa27a2f5
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35901655"
---
# <a name="tutorial-azure-active-directory-integration-with-druva"></a>Kurz: Azure Active Directory integrace s Druva

V tomto kurzu zjistěte, jak integrovat Druva s Azure Active Directory (Azure AD).

Integrace Druva s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Druva.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Druva (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Druva, potřebujete následující položky:

- Předplatné služby Azure AD
- Druva jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Druva z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-druva-from-the-gallery"></a>Přidání Druva z Galerie
Při konfiguraci integrace Druva do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Druva z galerie.

**Pokud chcete přidat Druva z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Druva**, vyberte **Druva** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Druva v seznamu výsledků](./media/druva-tutorial/tutorial_druva_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Druva podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Druva je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Druva musí navázat.

V Druva, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Druva, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Druva](#create-a-druva-test-user)**  – Pokud chcete mít protějšek Britta Simon v Druva propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Druva.

**Ke konfiguraci Azure AD jednotné přihlašování s Druva, proveďte následující kroky:**

1. Na portálu Azure na **Druva** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/druva-tutorial/tutorial_druva_samlbase.png)

3. Na **Druva domény a adresy URL** část, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Konfigurovat jednotné přihlašování](./media/druva-tutorial/tutorial_druva_url.png)

    V **identifikátor** textovému poli, zadejte hodnotu řetězce: `druva-cloud`
    
4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL**. Pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Konfigurovat jednotné přihlašování](./media/druva-tutorial/tutorial_druva_url1.png)
    
    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL: `https://cloud.druva.com/home`

5. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/druva-tutorial/tutorial_druva_certificate.png) 

6. Aplikace Druva očekává SAML kontrolní výrazy ve specifickém formátu, který můžete přidat mapování vlastní atribut vyžaduje vaše **atributy tokenu SAML** konfigurace. 

    ![Konfigurovat jednotné přihlašování](./media/druva-tutorial/tutorial_druva_attribute.png)

7. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je vidět na předchozím obrázku a proveďte následující kroky:

    | Název atributu      | Hodnota atributu      |
    | ------------------- | -------------------- |
    | synchronizována\_auth\_tokenu |Zadejte token generované hodnoty |
    
    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.
    
    ![Konfigurovat jednotné přihlašování](./media/druva-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurovat jednotné přihlašování](./media/druva-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku. Hodnota tokenu generovaného se vysvětluje dále v kurzu.
    
    d. Klikněte na tlačítko **OK**.    

8. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/druva-tutorial/tutorial_general_400.png)

9. Na **Druva konfigurace** klikněte na tlačítko **konfigurace Druva** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL a SAML jeden přihlašování služby URL** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/druva-tutorial/tutorial_druva_configure.png) 

10. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti Druva jako správce.

11. Přejděte na **spravovat \> nastavení**.

    ![Nastavení](./media/druva-tutorial/ic795091.png "nastavení")

12. V dialogovém okně Nastavení jednotného přihlašování proveďte následující kroky:

    ![Jednotné přihlašování v nastavení](./media/druva-tutorial/ic795092.png "jednotné přihlašování v nastavení")
    
    a. V **ID zprostředkovatele přihlašovací adresa URL** textovému poli, vložte hodnotu **jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.
        
    b. V **adresy URL odhlašovací ID zprostředkovatele** textovému poli, vložte hodnotu **Sign-Out URL**, který jste zkopírovali z portálu Azure
        
    c. V poznámkovém bloku otevřete váš kódování base-64 kódovaného certifikátu, zkopírujte obsah ho do schránky a vložte jej do **certifikát poskytovatele ID** textbox
     
    d. Chcete-li otevřít **nastavení** klikněte na tlačítko **Uložit**.

13. Na **nastavení** klikněte na tlačítko **vygenerovat Token jednotného přihlašování k**.

    ![Nastavení](./media/druva-tutorial/ic795093.png "nastavení")

14. Na **jeden přihlašování ověřování tokenu** dialogové okno, proveďte následující kroky:

    ![Token jednotného přihlašování k](./media/druva-tutorial/ic795094.png "tokenu jednotného přihlašování")
    
    a. Klikněte na tlačítko **kopie**, vložte zkopírovat hodnotu v **hodnotu** textového pole v **přidat atribut** části portálu Azure.
    
    b. Klikněte na **Zavřít**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/druva-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/druva-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/druva-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/druva-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-druva-test-user"></a>Vytvoření zkušebního uživatele Druva

Pokud chcete povolit uživatelům Azure AD přihlášení k Druva, musí být zřízená do Druva. V případě Druva zřizování je ruční úloha.

**Pokud chcete konfigurovat, zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **Druva** společnosti lokality jako správce.

2. Přejděte na **spravovat \> uživatelé**.
   
   ![Správa uživatelů](./media/druva-tutorial/ic795097.png "Správa uživatelů")

3. Klikněte na tlačítko **vytvořit nový**.
   
   ![Správa uživatelů](./media/druva-tutorial/ic795098.png "Správa uživatelů")

4. V dialogovém okně Vytvořit nového uživatele proveďte následující kroky:
   
   ![Vytvoření NewUser](./media/druva-tutorial/ic795099.png "vytvořit NewUser")
   
   a. V **e-mailová adresa** textovému poli, zadejte e-mailu uživatele jako **brittasimon@contoso.com**.
   
   b. V **název** textovému poli, zadejte jméno uživatele, jako je **BrittaSimon**.
   
   c. Klikněte na tlačítko **vytvořit uživatele**.

>[!NOTE]
>Můžete použít všechny ostatní Druva uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované Druva ke zřízení uživatelských účtů Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Druva.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Druva, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Druva**.

    ![V seznamu aplikací na Druva odkaz](./media/druva-tutorial/tutorial_druva_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Druva na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Druva.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/druva-tutorial/tutorial_general_01.png
[2]: ./media/druva-tutorial/tutorial_general_02.png
[3]: ./media/druva-tutorial/tutorial_general_03.png
[4]: ./media/druva-tutorial/tutorial_general_04.png

[100]: ./media/druva-tutorial/tutorial_general_100.png

[200]: ./media/druva-tutorial/tutorial_general_200.png
[201]: ./media/druva-tutorial/tutorial_general_201.png
[202]: ./media/druva-tutorial/tutorial_general_202.png
[203]: ./media/druva-tutorial/tutorial_general_203.png

