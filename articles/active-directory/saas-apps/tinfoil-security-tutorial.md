---
title: 'Kurz: Azure Active Directory integrace s TINFOIL SECURITY | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TINFOIL SECURITY.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 3cd9d0c24ecb7dda01a4d36c013ebd4f6a484ddc
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35899361"
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Kurz: Azure Active Directory integrace s TINFOIL SECURITY

V tomto kurzu zjistěte, jak integrovat TINFOIL SECURITY s Azure Active Directory (Azure AD).

Integrace TINFOIL SECURITY s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k TINFOIL SECURITY
- Můžete povolit uživatelům, aby automaticky získat přihlášení k TINFOIL SECURITY (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s TINFOIL SECURITY, potřebujete následující položky:

- Předplatné služby Azure AD
- TINFOIL SECURITY jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidat TINFOIL SECURITY z Galerie
2. Konfigurace a otestování Azure AD jednotné přihlašování

## <a name="add-tinfoil-security-from-the-gallery"></a>Přidat TINFOIL SECURITY z Galerie
Při konfiguraci integrace TINFOIL SECURITY do služby Azure AD potřebujete přidat TINFOIL SECURITY z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat TINFOIL SECURITY z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **TINFOIL SECURITY**, vyberte **TINFOIL SECURITY** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![TINFOIL SECURITY z Galerie](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s TINFOIL SECURITY podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v TINFOIL SECURITY je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v TINFOIL SECURITY musí navázat.

V TINFOIL SECURITY přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s TINFOIL SECURITY, budete muset provést následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele TINFOIL SECURITY](#create-a-tinfoil-security-test-user)**  – Pokud chcete mít protějšek Britta Simon v TINFOIL SECURITY propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci TINFOIL SECURITY.

**Ke konfiguraci Azure AD jednotné přihlašování s TINFOIL SECURITY, proveďte následující kroky:**

1. Na portálu Azure na **TINFOIL SECURITY** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Přihlášení na základě SAML](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_samlbase.png)

3. Na **TINFOIL zabezpečení domény a adresy URL** části uživatel nemusí provádět žádné kroky, protože aplikace je už předem integrováno s Azure.

    ![Konfigurovat jednotné přihlašování](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_url.png)


4. Na **SAML podpisový certifikát** část, zkopírujte **kryptografický OTISK** hodnotu.

    ![Certifikát pro podpis SAML části](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_certificate.png) 

5. Pokud chcete přidat mapování požadovaný atribut, proveďte následující kroky:
    
    ![Atributy](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_attribute1.png "atributy")
    
    | Název atributu    |   Hodnota atributu |
    | ------------------- | -------------------- |
    | ID účtu | UXXXXXXXXXXXXX |
    
    a. Klikněte na tlačítko **přidat atribut uživatele**.
    
    ![Přidat atribut](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_attribute.png "atributy")
    
    ![Přidat atribut](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_addatt.png "atributy")
    
    b. V **název atributu** textovému poli, typ **accountid**.
    
    c. V **hodnota atributu** textovému poli, vložte ID účtu hodnotu, která budete mít později v kurzu.
    
    d. Klikněte na tlačítko **OK**.    

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Tlačítko Uložit](./media/tinfoil-security-tutorial/tutorial_general_400.png)

7. Na **TINFOIL SECURITY Configuration** klikněte na tlačítko **konfigurace TINFOIL SECURITY** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace zabezpečení aplikace TINFOIL](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_configure.png) 

8. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti TINFOIL SECURITY.

9. Na panelu nástrojů v horní části klikněte na tlačítko **Můj účet**.
   
    ![Řídicí panel](./media/tinfoil-security-tutorial/ic798971.png "řídicí panel")

10. Klikněte na tlačítko **zabezpečení**.
   
    ![Zabezpečení](./media/tinfoil-security-tutorial/ic798972.png "zabezpečení")

11. Na **jednotné přihlašování** konfigurace proveďte následující kroky:
   
    ![Jednotné přihlašování](./media/tinfoil-security-tutorial/ic798973.png "jednotného přihlašování")
   
    a. Vyberte **povolit SAML**.
   
    b. Klikněte na tlačítko **ruční konfigurace**.
   
    c. V **SAML Post URL** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure
   
    d. V **otisků certifikátu SAML** textovému poli, vložte hodnotu **kryptografický otisk** který jste zkopírovali z **SAML podpisový certifikát** části.
  
    e. Kopírování **vaše ID účtu** a vložte hodnotu v **hodnota atributu** textového pole pod **přidat atribut** části na portálu Azure.
   
    f. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/tinfoil-security-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Uživatelé a skupiny -> všichni uživatelé ](./media/tinfoil-security-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Uživatel](./media/tinfoil-security-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/tinfoil-security-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-tinfoil-security-test-user"></a>Vytvoření zkušebního uživatele TINFOIL SECURITY

Pokud chcete povolit uživatelům Azure AD přihlášení do TINFOIL SECURITY, musí být zřízená do TINFOIL SECURITY. V případě TINFOIL SECURITY zřizování je ruční úloha.

**Pokud chcete získat uživatele zřízený, proveďte následující kroky:**

1. Pokud uživatel je součástí účet podnikové sítě, budete muset [kontaktujte tým podpory TINFOIL SECURITY](https://www.tinfoilsecurity.com/contact) se získat uživatelský účet vytvořený.

2. Pokud je uživatel běžný uživatel TINFOIL SECURITY SaaS, pak uživatel může přidávat spolupracovníka pro žádného uživatele lokalit. Tím se spustí proces k odeslání pozvánky k zadané e-mailu k vytvoření nového uživatelského účtu TINFOIL SECURITY.

> [!NOTE]
> Další nástroje pro tvorbu TINFOIL SECURITY uživatelského účtu nebo rozhraní API poskytovaných TINFOIL SECURITY můžete použít ke zřízení uživatelských účtů Azure AD.
> 
> 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu TINFOIL SECURITY.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon TINFOIL SECURITY, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **TINFOIL SECURITY**.

    ![Vyberte TINFOIL SECURITY](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici TINFOIL SECURITY na přístupovém panelu, jste měli získat automaticky přihlášení k aplikace TINFOIL SECURITY. Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tinfoil-security-tutorial/tutorial_general_01.png
[2]: ./media/tinfoil-security-tutorial/tutorial_general_02.png
[3]: ./media/tinfoil-security-tutorial/tutorial_general_03.png
[4]: ./media/tinfoil-security-tutorial/tutorial_general_04.png

[100]: ./media/tinfoil-security-tutorial/tutorial_general_100.png

[200]: ./media/tinfoil-security-tutorial/tutorial_general_200.png
[201]: ./media/tinfoil-security-tutorial/tutorial_general_201.png
[202]: ./media/tinfoil-security-tutorial/tutorial_general_202.png
[203]: ./media/tinfoil-security-tutorial/tutorial_general_203.png

