---
title: 'Kurz: Azure Active Directory integrace s Coupa | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Coupa.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 3e87502cbcdb0eff4310ad154083c7682d752e13
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34343721"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Kurz: Azure Active Directory integrace s Coupa

V tomto kurzu zjistěte, jak integrovat Coupa s Azure Active Directory (Azure AD).

Integrace Coupa s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Coupa.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Coupa (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Coupa, potřebujete následující položky:

- Předplatné služby Azure AD
- Coupa jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Coupa z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-coupa-from-the-gallery"></a>Přidání Coupa z Galerie
Při konfiguraci integrace Coupa do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Coupa z galerie.

**Pokud chcete přidat Coupa z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Coupa**, vyberte **Coupa** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Coupa v seznamu výsledků](./media/active-directory-saas-coupa-tutorial/tutorial_coupa_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Coupa podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Coupa je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Coupa musí navázat.

V Coupa, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Coupa, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Coupa](#create-a-coupa-test-user)**  – Pokud chcete mít protějšek Britta Simon v Coupa propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Coupa.

**Ke konfiguraci Azure AD jednotné přihlašování s Coupa, proveďte následující kroky:**

1. Na portálu Azure na **Coupa** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-coupa-tutorial/tutorial_coupa_samlbase.png)

3. Na **Coupa domény a adresy URL** část, proveďte následující kroky:

    ![Coupa domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-coupa-tutorial/tutorial_coupa_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `http://<companyname>.Coupa.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `<companyname>.coupahost.com`

    c. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.coupahost.com/sp/ACS.saml2`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte s skutečná adresa URL přihlašování, identifikátor a adresa URL odpovědi. Obraťte se na [tým podpory Coupa klienta](https://success.coupa.com/Support/Contact_Us?) k získání těchto hodnot. Zobrazí se hodnota adresa URL odpovědi z metadat, který je vysvětlen později v tomto kurzu.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-coupa-tutorial/tutorial_coupa_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-coupa-tutorial/tutorial_general_400.png)

6. Přihlásit se k serveru vaší společnosti Coupa jako správce.

7. Přejděte na **instalace \> řízení zabezpečení**.
   
   ![Ovládací prvky zabezpečení](./media/active-directory-saas-coupa-tutorial/ic791900.png "kontrolních mechanismů pro zabezpečení")

8. V **přihlásit pomocí přihlašovacích údajů Coupa** část, proveďte následující kroky:

    ![Metadata Coupa SP](./media/active-directory-saas-coupa-tutorial/ic791901.png "Coupa SP metadat")
    
    a. Vyberte **Přihlaste se pomocí SAML**.
    
    b. Chcete-li stáhnout soubor metadat Coupa do počítače, klikněte na tlačítko **stahování a import metadat SP**. Otevřete metadata a zkopírujte **AssertionConsumerService index nebo adresa URL** hodnotu, vložte hodnotu do **adresa URL odpovědi** textového pole v **Coupa domény a adresy URL** části. 
    
    c. Klikněte na tlačítko **Procházet** nahrát metadata stáhli z portálu Azure.
    
    d. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-coupa-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-coupa-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-coupa-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-coupa-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-coupa-test-user"></a>Vytvoření zkušebního uživatele Coupa

Pokud chcete povolit uživatelům Azure AD přihlášení do Coupa, musí být zřízená do Coupa.  

* V případě Coupa zřizování je ruční úloha.

**Pokud chcete konfigurovat, zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **Coupa** společnosti lokality jako správce.

2. V nabídce v horní části, klikněte na tlačítko **instalace**a potom klikněte na **uživatelé**.
   
   ![Uživatelé](./media/active-directory-saas-coupa-tutorial/ic791908.png "uživatelů")

3. Klikněte na možnost **Vytvořit**.
   
   ![Vytvoření uživatelů](./media/active-directory-saas-coupa-tutorial/ic791909.png "vytvoření uživatelů")

4. V **vytvořit uživateli** část, proveďte následující kroky:
   
   ![Podrobné informace o uživateli](./media/active-directory-saas-coupa-tutorial/ic791910.png "podrobné informace o uživateli")
   
   a. Typ **přihlášení**, **křestní jméno**, **příjmení**, **ID přihlášení**, **e-mailu** atributy platný účet služby Azure Active Directory, který má být zahrnuty do související textových polí.

   b. Klikněte na možnost **Vytvořit**.   
   
   >[!NOTE]
   >Držitel účtu Azure Active Directory dostane e-mail s odkazem pro potvrzení účtu před stane aktivní. 
   > 

>[!NOTE]
>Můžete použít všechny ostatní Coupa uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované Coupa zřídit AAD uživatelské účty. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Coupa.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Coupa, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Coupa**.

    ![V seznamu aplikací na Coupa odkaz](./media/active-directory-saas-coupa-tutorial/tutorial_coupa_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Coupa na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Coupa.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_203.png

