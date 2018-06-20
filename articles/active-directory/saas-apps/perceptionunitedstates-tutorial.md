---
title: 'Kurz: Azure Active Directory integrace s dojem Spojených států (Non-UltiPro) | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a dojem USA (Non-UltiPro).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: b4a8f026-cb5f-41eb-9680-68eddc33565e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: e4f0fe0399b1230a27460df1718223736176ff01
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36228216"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Kurz: Azure Active Directory integrace s dojem Spojených států (Non-UltiPro)

V tomto kurzu zjistěte, jak integrovat dojem USA (Non-UltiPro) s Azure Active Directory (Azure AD).

Integrace dojem USA (Non-UltiPro) s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k dojem Spojených států (Non-UltiPro).
- Můžete povolit uživatelům, aby automaticky získat přihlášeného k dojem Spojených států (Non-UltiPro) (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s dojem Spojených států (Non-UltiPro), potřebujete následující položky:

- Předplatné služby Azure AD
- Dojem USA (Non-UltiPro) jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání dojem USA (Non-UltiPro) z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Přidání dojem USA (Non-UltiPro) z Galerie
Při konfiguraci integrace služby dojem Spojených států (Non-UltiPro) do služby Azure AD, musíte přidat dojem USA (Non-UltiPro) z Galerie váš seznam spravovaných aplikací SaaS.

**Pokud chcete přidat dojem USA (Non-UltiPro) z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **dojem USA (Non-UltiPro)**, vyberte **dojem USA (Non-UltiPro)** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Dojem Spojených států (Non-UltiPro) v seznamu výsledků](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části konfiguraci a testování Azure AD jednotné přihlašování s dojem USA (Non-UltiPro) podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v dojem Spojených států (Non-UltiPro) je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah propojení mezi uživatele Azure AD a související uživatelské v dojem Spojených států (Non-UltiPro).

V dojem USA (Non-UltiPro), přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s dojem Spojených států (Non-UltiPro), je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele dojem USA (Non-UltiPro)](#create-a-perception-united-states-non-ultipro-test-user)**  – Pokud chcete mít protějšek Britta Simon v dojem Spojené státy americké (Non-UltiPro), je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci dojem USA (Non-UltiPro).

**Pokud chcete konfigurovat Azure AD jednotné přihlašování s dojem Spojených států (Non-UltiPro), proveďte následující kroky:**

1. Na portálu Azure na **dojem USA (Non-UltiPro)** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_samlbase.png)

3. Na **doméně dojem USA (Non-UltiPro) a adresy URL** část, proveďte následující kroky:

    ![Dojem USA (Non-UltiPro) domény a adresy URL jednotné přihlašování informace](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL: `https://perception.kanjoya.com/sp`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://perception.kanjoya.com/sso?idp=<entity_id>`

    > [!NOTE] 
    > Hodnota není skutečné. Hodnota bude aktualizován skutečná adresa URL odpovědi, který je vysvětlen později v tomto kurzu.
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/perceptionunitedstates-tutorial/tutorial_general_400.png)

6. Na **konfigurace dojem USA (Non-UltiPro)** klikněte na tlačítko **konfigurace dojem Spojených států (Non-UltiPro)** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID** z **Stručná referenční příručka části.**

    a. **Dojem USA (Non-UltiPro)** aplikace vyžaduje **SAML Entity ID** hodnotu, kterou jste zkopírovali, být kódovaný identifikátor uri. Hodnota identifikátoru uri kódování, použijte následující odkaz:**http://www.url-encode-decode.com/**.

    b. Po získání identifikátor uri ho s spojovat šifrovanou hodnotu **adresa URL odpovědi** jak je uvedeno níže -

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    c. Vložte hodnotu uvedenou výše v **adresa URL odpovědi** textového pole v **doméně dojem USA (Non-UltiPro) a adresy URL** části.

    ![Konfigurace dojem USA (bez UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_configure.png) 

7. V jiném okně prohlížeče Přihlaste se k serveru vaší společnosti dojem USA (Non-UltiPro) jako správce.

8. Na hlavním panelu nástrojů klikněte na tlačítko **nastavení účtu**.

    ![Dojem uživatele USA (Non-UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

9. Na **nastavení účtu** proveďte následující kroky:

    ![Dojem uživatele USA (Non-UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. V **název společnosti** textovému poli, zadejte název **společnosti**.
    
    b. V **název účtu** textovému poli, zadejte název **účet**.

    c. V **výchozí odpověď pro e-mailu** text zadejte platném **e-mailu**.

    d. Vyberte **zprostředkovatele Identity jednotného přihlašování k** jako **SAML 2.0**.

10. Na **Konfigurace jednotného přihlašování k** proveďte následující kroky:

    ![SSOConfig dojem USA (bez UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Vyberte **SAML NameID typ** jako **e-MAILU**.

    b. V **název konfigurace jednotného přihlašování k** textovému poli, zadejte název vaší **konfigurace**.
    
    c. V **název zprostředkovatele Identity** textovému poli, vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure. 

    d. V **SAML domény textbox**, zadejte doménu jako **@contoso.com**.

    e. Klikněte na **nahrát znovu** nahrát **soubor XML s metadaty** souboru.

    f. Klikněte na tlačítko **aktualizace**.


> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/perceptionunitedstates-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/perceptionunitedstates-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/perceptionunitedstates-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/perceptionunitedstates-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-perception-united-states-non-ultipro-test-user"></a>Vytvoření zkušebního uživatele dojem USA (Non-UltiPro)

V této části vytvoříte uživatele volat Britta Simon v dojem Spojených států (Non-UltiPro). Práce s [tým podpory dojem USA (Non-UltiPro)](http://www.ultimatesoftware.com/Contact/ContactUs) přidat uživatele do platformy dojem USA (Non-UltiPro).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon chcete použít Azure jednotného přihlašování k udělení přístupu k dojem Spojených států (Non-UltiPro).

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon k dojem Spojených států (Non-UltiPro), proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **dojem USA (Non-UltiPro)**.

    ![V seznamu aplikací na odkaz dojem USA (Non-UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici dojem USA (Non-UltiPro) na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci dojem USA (Non-UltiPro).
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/perceptionunitedstates-tutorial/tutorial_general_01.png
[2]: ./media/perceptionunitedstates-tutorial/tutorial_general_02.png
[3]: ./media/perceptionunitedstates-tutorial/tutorial_general_03.png
[4]: ./media/perceptionunitedstates-tutorial/tutorial_general_04.png

[100]: ./media/perceptionunitedstates-tutorial/tutorial_general_100.png

[200]: ./media/perceptionunitedstates-tutorial/tutorial_general_200.png
[201]: ./media/perceptionunitedstates-tutorial/tutorial_general_201.png
[202]: ./media/perceptionunitedstates-tutorial/tutorial_general_202.png
[203]: ./media/perceptionunitedstates-tutorial/tutorial_general_203.png

