---
title: 'Kurz: Azure Active Directory integrace s Thoughtworks Mingle | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Thoughtworks Mingle.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: f4729440bf523cef3f2721111852f4e5f445b5fb
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35901020"
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Kurz: Azure Active Directory integrace s Thoughtworks Mingle

V tomto kurzu zjistěte, jak integrovat Thoughtworks Mingle s Azure Active Directory (Azure AD).

Integrace Thoughtworks Mingle s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Thoughtworks Mingle
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Thoughtworks Mingle (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Thoughtworks Mingle, potřebujete následující položky:

- Předplatné služby Azure AD
- Thoughtworks Mingle jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Thoughtworks Mingle z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>Přidání Thoughtworks Mingle z Galerie
Chcete-li nakonfigurovat integraci Thoughtworks Mingle do služby Azure AD, přidejte Thoughtworks Mingle z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Thoughtworks Mingle z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Thoughtworks Mingle**, vyberte **Thoughtworks Mingle** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Thoughtworks Mingle v seznamu výsledků](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Thoughtworks Mingle podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Thoughtworks Mingle je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Thoughtworks Mingle musí navázat.

V Thoughtworks Mingle, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Thoughtworks Mingle, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Thoughtworks Mingle](#create-a-thoughtworks-mingle-test-user)**  – Pokud chcete mít protějšek Britta Simon v Thoughtworks Mingle propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Thoughtworks Mingle.

**Ke konfiguraci Azure AD jednotné přihlašování s Thoughtworks Mingle, proveďte následující kroky:**

1. Na portálu Azure na **Thoughtworks Mingle** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_samlbase.png)

3. Na **Thoughtworks Mingle domény a adresy URL** část, proveďte následující kroky:

    ![Thoughtworks Mingle domény a adresy URL jednotné přihlašování informace](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_url.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE] 
    > Hodnota není skutečné. Aktualizujte hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory klienta Mingle Thoughtworks](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support) k získání hodnoty. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/thoughtworks-mingle-tutorial/tutorial_general_400.png)

6. Přihlaste se k vaší **Thoughtworks Mingle** společnosti lokality jako správce.

7. Klikněte **správce** kartě a potom klikněte na **Konfigurace jednotného přihlašování k**.
   
    ![Karta správce](./media/thoughtworks-mingle-tutorial/ic785157.png "Konfigurace jednotného přihlašování")

8. V **Konfigurace jednotného přihlašování k** část, proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování k](./media/thoughtworks-mingle-tutorial/ic785158.png "Konfigurace jednotného přihlašování")
    
    a. Chcete-li nahrát soubor metadat, klikněte na tlačítko **zvolte soubor**. 

    b. Klikněte na tlačítko **uložit změny**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](./media/thoughtworks-mingle-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/thoughtworks-mingle-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Tlačítko Přidat](./media/thoughtworks-mingle-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Dialogové okno uživatele](./media/thoughtworks-mingle-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-thoughtworks-mingle-test-user"></a>Vytvoření zkušebního uživatele Thoughtworks Mingle

Azure AD uživatelé moci přihlásit musí být zřízená do Thoughtworks Mingle aplikace pomocí jejich názvy uživatele Azure Active Directory. V případě Thoughtworks Mingle zřizování je ruční úloha.

**Pokud chcete konfigurovat, zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k serveru vaší společnosti Thoughtworks Mingle jako správce.

2. Klikněte na tlačítko **profil**.
   
    ![Svůj první projekt](./media/thoughtworks-mingle-tutorial/ic785160.png "prvního projektu")

3. Klikněte **správce** a pak klikněte **uživatelé**.
   
    ![Uživatelé](./media/thoughtworks-mingle-tutorial/ic785161.png "uživatelů")

4. Klikněte na tlačítko **nového uživatele**.
   
    ![Nový uživatel](./media/thoughtworks-mingle-tutorial/ic785162.png "nového uživatele")

5. Na **nového uživatele** dialogové okno stránky, proveďte následující kroky:
   
    ![Dialogové okno Nový uživatel](./media/thoughtworks-mingle-tutorial/ic785163.png "nového uživatele")  
 
    a. Typ **přihlašovací jméno**, **zobrazovaný název**, **zvolte heslo**, **potvrzení hesla** platný Azure AD účtu chcete mají být zahrnuty do související textových polí. 

    b. Jako **typ uživatele**, vyberte **úplné uživatelské**.

    c. Klikněte na tlačítko **vytvořit tento profil**.

>[!NOTE]
>Můžete použít všechny ostatní Thoughtworks Mingle uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované Thoughtworks Mingle zřídit AAD uživatelské účty.
> 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon k používání Azure jednotné přihlašování v Thoughtworks Mingle udělení přístupu.

![Přiřadit role uživatele][200] 

**Chcete-li Thoughtworks Mingle přiřadit Britta Simon, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Thoughtworks Mingle**.

    ![Odkaz Thoughtworks Mingle v seznamu aplikací](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Thoughtworks Mingle na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Thoughtworks Mingle.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/thoughtworks-mingle-tutorial/tutorial_general_01.png
[2]: ./media/thoughtworks-mingle-tutorial/tutorial_general_02.png
[3]: ./media/thoughtworks-mingle-tutorial/tutorial_general_03.png
[4]: ./media/thoughtworks-mingle-tutorial/tutorial_general_04.png

[100]: ./media/thoughtworks-mingle-tutorial/tutorial_general_100.png

[200]: ./media/thoughtworks-mingle-tutorial/tutorial_general_200.png
[201]: ./media/thoughtworks-mingle-tutorial/tutorial_general_201.png
[202]: ./media/thoughtworks-mingle-tutorial/tutorial_general_202.png
[203]: ./media/thoughtworks-mingle-tutorial/tutorial_general_203.png

