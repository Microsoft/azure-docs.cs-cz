---
title: 'Kurz: Azure Active Directory integrace s PagerDuty | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a PagerDuty.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 6ba7e1fc2f58fd8fb7a05935ce2c4b99709d642c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34351280"
---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Kurz: Azure Active Directory integrace s PagerDuty

V tomto kurzu zjistěte, jak integrovat PagerDuty s Azure Active Directory (Azure AD).

Integrace PagerDuty s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k PagerDuty
- Můžete povolit uživatelům, aby automaticky získat přihlášení k PagerDuty (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s PagerDuty, potřebujete následující položky:

- Předplatné služby Azure AD
- PagerDuty jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání PagerDuty z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-pagerduty-from-the-gallery"></a>Přidání PagerDuty z Galerie
Při konfiguraci integrace PagerDuty do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS PagerDuty z galerie.

**Pokud chcete přidat PagerDuty z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **PagerDuty**, vyberte **PagerDuty** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s PagerDuty podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v PagerDuty je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v PagerDuty musí navázat.

V PagerDuty, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s PagerDuty, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele PagerDuty](#create-a-pagerduty-test-user)**  – Pokud chcete mít protějšek Britta Simon v PagerDuty propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci PagerDuty.

**Ke konfiguraci Azure AD jednotné přihlašování s PagerDuty, proveďte následující kroky:**

1. Na portálu Azure na **PagerDuty** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_samlbase.png)

3. Na **PagerDuty domény a adresy URL** část, proveďte následující kroky:

    ![PagerDuty domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<tenant-name>.pagerduty.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<tenant-name>.pagerduty.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory PagerDuty klienta](https://www.pagerduty.com/support/) k získání těchto hodnot. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-pagerduty-tutorial/tutorial_general_400.png)

6. Na **PagerDuty konfigurace** klikněte na tlačítko **konfigurace PagerDuty** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL a SAML jeden přihlašování služby URL** z **Stručná referenční příručka části.**

    ![Konfigurace PagerDuty](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti Pagerduty.

8. V nabídce v horní části, klikněte na tlačítko **nastavení účtu**.
   
    ![Nastavení účtu](./media/active-directory-saas-pagerduty-tutorial/ic778535.png "nastavení účtu")

9. Klikněte na tlačítko **jednotného přihlašování**.
   
    ![Jednotné přihlašování](./media/active-directory-saas-pagerduty-tutorial/ic778536.png "jednotného přihlašování")

10. Na **povolit jednotné přihlašování (SSO)** proveďte následující kroky:
   
    ![Povolit jednotné přihlašování](./media/active-directory-saas-pagerduty-tutorial/ic778537.png "povolit jednotné přihlašování")
   
    a. Otevření kódovaného certifikátu kódování base-64 stáhli z portálu Azure v programu Poznámkový blok, zkopírujte obsah ho do schránky a vložte jej do **certifikát X.509** textbox
  
    b. V **přihlašovací adresa URL** textovému poli, vložte **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.
  
    c. V **adresy URL odhlašovací** textovému poli, vložte **Sign-Out URL** který jste zkopírovali z portálu Azure.
 
    d. Vyberte **zapnout Single Sign-on**.
 
    e. Klikněte na tlačítko **uložit změny**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-pagerduty-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-pagerduty-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Tlačítko Přidat](./media/active-directory-saas-pagerduty-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Dialogové okno uživatele](./media/active-directory-saas-pagerduty-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-pagerduty-test-user"></a>Vytvoření zkušebního uživatele PagerDuty

Pokud chcete povolit uživatelům Azure AD přihlášení k PagerDuty, musí být zřízená do PagerDuty.  
V případě PagerDuty zřizování je ruční úloha.

>[!NOTE]
>Můžete použít všechny ostatní Pagerduty uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované Pagerduty zřídit služby Azure Active Directory uživatelské účty.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k vaší **Pagerduty** klienta.

2. V nabídce v horní části, klikněte na tlačítko **uživatelé**.

3. Klikněte na tlačítko **přidat uživatele**.
   
    ![Přidání uživatelů](./media/active-directory-saas-pagerduty-tutorial/ic778539.png "přidat uživatele")

4.  Na **pozvat váš tým** dialogové okno, proveďte následující kroky:
   
    ![Pozvěte váš tým](./media/active-directory-saas-pagerduty-tutorial/ic778540.png "pozvat váš tým")

    a. Typ **první a poslední název** uživatele jako **Britta Simon**. 
   
    b. Zadejte **e-mailu** adresu uživatele, například **brittasimon@contoso.com**.
   
    c. Klikněte na tlačítko **přidat**a potom klikněte na **odesílání žádostí**.
   
    >[!NOTE]
    >Všechny přidaní uživatelé dostanou pozvání k vytvoření účtu PagerDuty.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu PagerDuty.

![Přiřadit role uživatele][200]

**Pokud chcete přiřadit Britta Simon PagerDuty, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **PagerDuty**.

    ![V seznamu aplikací na PagerDuty odkaz](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Po kliknutí na tlačítko dlaždici PagerDuty v Panelyou přístup by měl získat automaticky přihlášení k aplikaci PagerDuty.

Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_203.png

