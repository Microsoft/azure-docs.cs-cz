---
title: 'Kurz: Azure Active Directory integrace s Veracode | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Veracode.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: fe96581428b8a41219a4aff46ecc0316d97884ae
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36228665"
---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>Kurz: Azure Active Directory integrace s Veracode

V tomto kurzu zjistěte, jak integrovat Veracode s Azure Active Directory (Azure AD).

Integrace Veracode s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Veracode.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Veracode (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Veracode, potřebujete následující položky:

- Předplatné služby Azure AD
- Veracode jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidat Veracode z Galerie
2. Konfigurace a otestování Azure AD jednotné přihlašování

## <a name="add-veracode-from-the-gallery"></a>Přidat Veracode z Galerie
Při konfiguraci integrace Veracode do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Veracode z galerie.

**Pokud chcete přidat Veracode z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Veracode**, vyberte **Veracode** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Veracode v seznamu výsledků](./media/veracode-tutorial/tutorial_veracode_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Veracode podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Veracode je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Veracode musí navázat.

V Veracode, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Veracode, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Veracode](#create-a-veracode-test-user)**  – Pokud chcete mít protějšek Britta Simon v Veracode propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Veracode.

**Ke konfiguraci Azure AD jednotné přihlašování s Veracode, proveďte následující kroky:**

1. Na portálu Azure na **Veracode** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/veracode-tutorial/tutorial_veracode_samlbase.png)

3. Na **Veracode domény a adresy URL** části uživatel nemusí provádět žádné kroky, protože aplikace je už předem integrováno s Azure. 

    ![Konfigurovat jednotné přihlašování](./media/veracode-tutorial/tutorial_veracode_url.png)

4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/veracode-tutorial/tutorial_veracode_certificate.png) 

5. Cílem této části se popisují, jak uživatelům povolit ověřování na Veracode ke svému účtu ve službě Azure AD využívající federaci na základě protokolu SAML.

    Aplikace Veracode očekává SAML kontrolní výrazy ve specifickém formátu, který můžete přidat mapování vlastní atribut vyžaduje vaše **atributy tokenu saml** konfigurace. Následující snímek obrazovky ukazuje příklad pro tento.
    
    ![Atributy](./media/veracode-tutorial/tutorial_veracode_attr.png "atributy")

6. Pokud chcete přidat mapování požadovaný atribut, proveďte následující kroky:

    | Název atributu | Hodnota atributu |
    |--- |--- |
    | FirstName |User.givenName |
    | Příjmení |User.Surname |
    | e-mail |User.Mail |
    
    a. Pro každý řádek dat v předchozí tabulce, klikněte na tlačítko **přidat atribut uživatele**.
    
    ![Atributy](./media/veracode-tutorial/tutorial_veracode_addattr.png "atributy")
    
    ![Atributy](./media/veracode-tutorial/tutorial_veracode_addattr1.png "atributy")
    
    b. V **název atributu** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.
    
    c. V **hodnota atributu** textovému poli, vyberte hodnotu atributu zobrazený pro tento řádek.
    
    d. Klikněte na tlačítko **OK**.

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/veracode-tutorial/tutorial_general_400.png)

8. Na **Veracode konfigurace** klikněte na tlačítko **konfigurace Veracode** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID** z **Stručná referenční příručka části.**

    ![Konfigurace Veracode](./media/veracode-tutorial/tutorial_veracode_configure.png) 

9. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti Veracode.

10. V nabídce v horní části, klikněte na tlačítko **nastavení**a potom klikněte na **správce**.
   
    ![Správa](./media/veracode-tutorial/ic802911.png "správy")

11. Klikněte **SAML** kartě.

12. V **organizace SAML nastavení** část, proveďte následující kroky:
   
    ![Správa](./media/veracode-tutorial/ic802912.png "správy")
   
    a.  V **vystavitele** textovému poli, vložte hodnotu **SAML Entity ID** který jste zkopírovali z portálu Azure.
    
    b. Chcete-li nahrát svůj certifikát stažený z portálu Azure, klikněte na tlačítko **zvolit soubor**.
   
    c. Vyberte **povolit samoobslužné registrace**.

13. V **nastavení samoobslužné registrace** část, proveďte následující kroky a pak klikněte na tlačítko **Uložit**:
   
    ![Správa](./media/veracode-tutorial/ic802913.png "správy")
   
    a. Jako **nové aktivace uživatele**, vyberte **vyžaduje její aktivace ne**.
   
    b. Jako **aktualizace dat uživatele**, vyberte **předvoleb Veracode uživatelská Data**.
   
    c. Pro **SAML atribut podrobnosti**, vyberte následující:
      * **Role uživatele**
      * **Správce zásad**
      * **kontrolora**
      * **Realizace zabezpečení**
      * **Vedení**
      * **Odesílatel**
      * **Tvůrce**
      * **Všechny kontroly typy**
      * **Členství v týmu**
      * **Výchozí tým**

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/veracode-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/veracode-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/veracode-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/veracode-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-veracode-test-user"></a>Vytvoření zkušebního uživatele Veracode
Pokud chcete povolit uživatelům Azure AD přihlášení do Veracode, musí být zřízená do Veracode. V případě Veracode zřizování je automatizaci úloh. Neexistuje žádná položka akce za vás. Uživatelé jsou automaticky vytvářeny v případě potřeby při prvním jeden přihlašování pokusu.

> [!NOTE]
> Můžete použít všechny ostatní Veracode uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované Veracode ke zřízení uživatelských účtů Azure AD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Veracode.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Veracode, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Veracode**.

    ![V seznamu aplikací na Veracode odkaz](./media/veracode-tutorial/tutorial_veracode_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Veracode na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Veracode.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/veracode-tutorial/tutorial_general_01.png
[2]: ./media/veracode-tutorial/tutorial_general_02.png
[3]: ./media/veracode-tutorial/tutorial_general_03.png
[4]: ./media/veracode-tutorial/tutorial_general_04.png

[100]: ./media/veracode-tutorial/tutorial_general_100.png

[200]: ./media/veracode-tutorial/tutorial_general_200.png
[201]: ./media/veracode-tutorial/tutorial_general_201.png
[202]: ./media/veracode-tutorial/tutorial_general_202.png
[203]: ./media/veracode-tutorial/tutorial_general_203.png

