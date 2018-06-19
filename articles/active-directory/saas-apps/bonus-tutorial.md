---
title: 'Kurz: Azure Active Directory integrace s Bonusly | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Bonusly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: d396ccbd8594e525f9f0f9dc4dabc8805fa2364b
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35903171"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Kurz: Azure Active Directory integrace s Bonusly

V tomto kurzu zjistěte, jak integrovat Bonusly s Azure Active Directory (Azure AD).

Integrace Bonusly s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Bonusly
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Bonusly (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Bonusly, potřebujete následující položky:

- Předplatné služby Azure AD
- Bonusly jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Bonusly z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-bonusly-from-the-gallery"></a>Přidání Bonusly z Galerie
Při konfiguraci integrace Bonusly do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Bonusly z galerie.

**Pokud chcete přidat Bonusly z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Bonusly**, vyberte **Bonusly** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Bonusly v seznamu výsledků](./media/bonus-tutorial/tutorial_bonusly_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Bonusly podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Bonusly je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Bonusly musí navázat.

V Bonusly, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Bonusly, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření Bonusly zkušebního uživatele](#create-a-bonusly-test-user)**  – Pokud chcete mít protějšek Britta Simon v Bonusly propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Bonusly.

**Ke konfiguraci Azure AD jednotné přihlašování s Bonusly, proveďte následující kroky:**

1. Na portálu Azure na **Bonusly** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/bonus-tutorial/tutorial_bonusly_samlbase.png)

3. Na **Bonusly domény a adresy URL** část, proveďte následující kroky:

    ![Bonusly domény a adresy URL jednotné přihlašování informace](./media/bonus-tutorial/tutorial_bonusly_url.png)

    V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE] 
    > Hodnota není skutečné. Aktualizujte hodnotu s skutečná adresa URL odpovědi. Obraťte se na [tým podpory Bonusly](https://Bonusly/contact) k získání hodnoty.
 
4. Na **SAML podpisový certifikát** část, zkopírujte **kryptografický OTISK** hodnotu z certifikátu.

    ![Odkaz ke stažení certifikátu](./media/bonus-tutorial/tutorial_bonusly_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/bonus-tutorial/tutorial_general_400.png)

6. Na **Bonusly konfigurace** klikněte na tlačítko **konfigurace Bonusly** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Bonusly konfigurace](./media/bonus-tutorial/tutorial_bonusly_configure.png) 

7. V okně jiný prohlížeč, přihlaste se k vaší **Bonusly** klienta.

8. Na panelu nástrojů v horní části klikněte na tlačítko **nastavení**a potom vyberte **aplikace a integrace v rámci**.
   
    ![Bonusly sociálních části](./media/bonus-tutorial/ic773686.png "Bonusly")
9. V části **jednotné přihlašování**, vyberte **SAML**.

10. Na **SAML** dialogové okno stránky, proveďte následující kroky:
   
    ![Bonusly Saml dialogové okno stránky](./media/bonus-tutorial/ic773687.png "Bonusly")
   
    a. V **IdP SSO cílová adresa URL** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.
   
    b. V **IdP vystavitele** textovému poli, vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure. 

    c. V **IdP přihlašovací adresa URL** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.

    d. Vložení **kryptografický otisk** hodnota zkopírována z portálu Azure do **otisk prstu Cert** textové pole.
   
11. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](./media/bonus-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/bonus-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Tlačítko Přidat](./media/bonus-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Dialogové okno uživatele](./media/bonus-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-bonusly-test-user"></a>Vytvoření Bonusly zkušebního uživatele

Pokud chcete povolit uživatelům Azure AD přihlášení k Bonusly, musí být zřízená do Bonusly. V případě Bonusly zřizování je ruční úloha.

>[!NOTE]
>Můžete použít jakékoli jiné nástroje vytvoření Bonusly uživatelského účtu nebo rozhraní API poskytované Bonusly zřídit AAD uživatelské účty.
>  

**Pokud chcete konfigurovat, zřizování uživatelů, proveďte následující kroky:**

1. V okně webového prohlížeče Přihlaste se ke klientovi Bonusly.

2. Klikněte na tlačítko **nastavení**.
 
    ![Nastavení](./media/bonus-tutorial/ic781041.png "nastavení")

3. Klikněte **uživatelů a bonusy** kartě.
   
    ![Uživatelé a bonusy](./media/bonus-tutorial/ic781042.png "uživatelů a bonusy")

4. Klikněte na tlačítko **Správa uživatelů**.
   
    ![Správa uživatelů](./media/bonus-tutorial/ic781043.png "Správa uživatelů")

5. Klikněte na tlačítko **přidat uživatele**.
   
    ![Přidat uživatele](./media/bonus-tutorial/ic781044.png "přidat uživatele")

6. Na **přidat uživatele** dialogové okno, proveďte následující kroky:
   
    ![Přidat uživatele](./media/bonus-tutorial/ic781045.png "přidat uživatele")  

    a. V **křestní jméno** textovému poli, zadejte jméno uživatele jako **Britta**.

    b. V **příjmení** textovému poli, zadejte příjmení uživatele jako **Simon**.
 
    c. V **e-mailu** textovému poli, zadejte e-mailu uživatele jako **brittasimon@contoso.com**.

    d. Klikněte na **Uložit**.
   
     >[!NOTE]
     >Držitel účtu Azure AD obdrží e-mail, který obsahuje odkaz pro potvrzení účtu před stane aktivní.
     >  

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Bonusly.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Bonusly, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Bonusly**.

    ![Odkaz Bonusly v seznamu aplikací](./media/bonus-tutorial/tutorial_bonusly_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Bonusly na přístupovém panelu jste měli získat automaticky přihlášení k aplikaci Bonusly.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bonus-tutorial/tutorial_general_01.png
[2]: ./media/bonus-tutorial/tutorial_general_02.png
[3]: ./media/bonus-tutorial/tutorial_general_03.png
[4]: ./media/bonus-tutorial/tutorial_general_04.png

[100]: ./media/bonus-tutorial/tutorial_general_100.png

[200]: ./media/bonus-tutorial/tutorial_general_200.png
[201]: ./media/bonus-tutorial/tutorial_general_201.png
[202]: ./media/bonus-tutorial/tutorial_general_202.png
[203]: ./media/bonus-tutorial/tutorial_general_203.png

