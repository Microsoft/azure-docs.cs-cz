---
title: 'Kurz: Azure Active Directory integrace s SilkRoad životnosti Suite | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SilkRoad životnosti Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.openlocfilehash: 58cfd858641263eb0c678203f191451345a0ded9
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35901818"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Kurz: Azure Active Directory integrace s SilkRoad životnosti Suite

V tomto kurzu zjistěte, jak integrovat SilkRoad životnosti Suite s Azure Active Directory (Azure AD).

Integrace s Azure AD SilkRoad životnosti Suite nabízí následující výhody:

- Můžete ovládat ve službě Azure AD, kdo má přístup k SilkRoad životnosti Suite.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k SilkRoad Suite životnosti (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace Azure AD integrace se sadou Suite životnosti SilkRoad, potřebujete následující položky:

- Předplatné služby Azure AD
- SilkRoad životnosti Suite jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SilkRoad životnosti Suite z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Přidání SilkRoad životnosti Suite z Galerie
Při konfiguraci integrace sady životnosti SilkRoad do služby Azure AD, potřebujete přidat Suite životnosti SilkRoad z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Suite životnosti SilkRoad z galerie, postupujte takto:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Suite životnosti SilkRoad**, vyberte **Suite životnosti SilkRoad** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Sada životnosti SilkRoad v seznamu výsledků](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s SilkRoad životnosti Suite podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v sadě životnosti SilkRoad je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v sadě životnosti SilkRoad musí navázat.

V sadě životnosti SilkRoad, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SilkRoad životnosti Suite, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Suite životnosti SilkRoad](#create-a-silkroad-life-suite-test-user)**  – Pokud chcete mít protějšek Britta Simon životnosti sady SilkRoad, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci SilkRoad životnosti Suite.

**Ke konfiguraci Azure AD jednotné přihlašování s SilkRoad životnosti Suite, proveďte následující kroky:**

1. Na portálu Azure na **Suite životnosti SilkRoad** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_samlbase.png)

3. Na **SilkRoad životnosti Suite domény a adresy URL** část, proveďte následující kroky:

    ![SilkRoad životnosti Suite domény a adresy URL jednotné přihlašování informace](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_url1.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.silkroad-eng.com/Authentication/`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP` |
    | `https://<subdomain>.silkroad.com/Authentication/SP` |

    c. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/` |
    | `https://<subdomain>.silkroad.com/Authentication/` |
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [tým podpory SilkRoad životnosti Suite klienta](https://www.silkroad.com/locations/) k získání těchto hodnot. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/silkroad-life-suite-tutorial/tutorial_general_400.png)
    
6. Na **SilkRoad životnosti Suite konfigurace** klikněte na tlačítko **konfigurace Suite životnosti SilkRoad** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace sady SilkRoad životnosti](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_configure.png) 

7. Přihlašování k webu společnosti SilkRoad jako správce. 
 
    >[!NOTE] 
    > Pokud chcete získat přístup k aplikaci SilkRoad životnosti Suite ověřování pro konfiguraci federační služby Microsoft Azure AD, kontaktujte prosím podporu SilkRoad nebo vaším zástupcem SilkRoad služby.

8. Přejděte na **poskytovatele služeb**a potom klikněte na **Federation podrobnosti**. 
   
    ![Azure AD jednotné přihlášení][10]

9. Klikněte na tlačítko **stáhnout federačních metadat**a potom uložte soubor metadat ve vašem počítači.
   
    ![Azure AD jednotné přihlášení][11] 

10. Ve vaší **SilkRoad** aplikace, klikněte na tlačítko **ověřování zdrojů**.
   
    ![Azure AD jednotné přihlášení][12] 

11. Klikněte na tlačítko **přidat zdroj ověřování**. 
   
    ![Azure AD jednotné přihlášení][13] 

12. V **přidat zdroj ověřování** část, proveďte následující kroky: 
   
    ![Azure AD jednotné přihlášení][14]
  
    a. V části **možnost 2 – soubor metadat**, klikněte na tlačítko **Procházet** nahrát soubor stažený metadata z portálu Azure.
  
    b. Klikněte na tlačítko **vytvořit zprostředkovatelů Identity pomocí dat souboru**.

13. V **ověřování zdrojů** klikněte na tlačítko **upravit**. 
    
     ![Azure AD jednotné přihlášení][15] 

14. Na **upravit zdroj ověřování** dialogové okno, proveďte následující kroky: 
    
     ![Azure AD jednotné přihlášení][16] 

    a. Jako **povoleno**, vyberte **Ano**.

    b. V **EntityId** textovému poli, vložte hodnotu **SAML Entity ID** který jste zkopírovali z portálu Azure.
   
    c. V **IdP popis** textovému poli, zadejte popis pro konfiguraci (například: *Azure AD jednotného přihlašování k*).

    d. V **soubor metadat** textovému poli, odesílání **metadata** souboru, který jste si stáhli z portálu Azure.
  
    e. V **IdP název** textovému poli, zadejte název, která je specifická pro konfiguraci (například: *Azure SP*).
  
    f. V **adresa URL služby odhlášení** textovému poli, vložte hodnotu **Sign-Out URL** který jste zkopírovali z portálu Azure.

    g. V **adresa URL služby přihlašování** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.

    h. Klikněte na **Uložit**.

15. Zakažte všechny ostatní zdroje ověřování. 
    
     ![Azure AD jednotné přihlášení][17]

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/silkroad-life-suite-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/silkroad-life-suite-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/silkroad-life-suite-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/silkroad-life-suite-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-silkroad-life-suite-test-user"></a>Vytvoření zkušebního uživatele SilkRoad životnosti Suite

V této části vytvoříte uživatele názvem Britta Simon v sadě SilkRoad životnosti. Práce s [tým podpory SilkRoad životnosti Suite klienta](https://www.silkroad.com/locations/) přidat uživatele do platformy SilkRoad životnosti Suite. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k SilkRoad životnosti Suite.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon SilkRoad životnosti Suite, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **SilkRoad životnosti Suite**.

    ![Odkaz SilkRoad životnosti Suite v seznamu aplikací](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici SilkRoad životnosti Suite na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci SilkRoad životnosti Suite.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/silkroad-life-suite-tutorial/tutorial_general_04.png

[100]: ./media/silkroad-life-suite-tutorial/tutorial_general_100.png

[200]: ./media/silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/silkroad-life-suite-tutorial/tutorial_general_202.png
[203]: ./media/silkroad-life-suite-tutorial/tutorial_general_203.png
[10]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png
