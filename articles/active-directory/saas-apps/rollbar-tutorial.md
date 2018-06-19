---
title: 'Kurz: Azure Active Directory integrace s oblouk | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a oblouk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 57537e54-9388-4272-a610-805ce45a451f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/04/2017
ms.author: jeedes
ms.openlocfilehash: 8dcf30a5dc018b396501563044a8c1ca1cb3b7d9
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35899262"
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Kurz: Azure Active Directory integrace s oblouk

V tomto kurzu zjistěte, jak integrovat oblouk s Azure Active Directory (Azure AD).

Integrace oblouk s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k oblouk.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k oblouk (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s oblouk, potřebujete následující položky:

- Předplatné služby Azure AD
- Oblouk jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání oblouk z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-rollbar-from-the-gallery"></a>Přidání oblouk z Galerie
Při konfiguraci integrace oblouk do služby Azure AD potřebujete přidat oblouk z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat oblouk z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **oblouk**, vyberte **oblouk** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![V seznamu výsledků oblouk](./media/rollbar-tutorial/tutorial_rollbar_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s oblouk podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v oblouk je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v oblouk musí navázat.

V oblouk, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s oblouk, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele oblouk](#create-a-rollbar-test-user)**  – Pokud chcete mít protějšek Britta Simon v oblouk propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci oblouk.

**Ke konfiguraci Azure AD jednotné přihlašování s oblouk, proveďte následující kroky:**

1. Na portálu Azure na **oblouk** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/rollbar-tutorial/tutorial_rollbar_samlbase.png)

3. Na **oblouk domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Oblouk domény a adresy URL jednotné přihlašování informace](./media/rollbar-tutorial/tutorial_rollbar_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL: `https://saml.rollbar.com`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://rollbar.com/<accountname>/saml/sso/azure/`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Oblouk domény a adresy URL jednotné přihlašování informace](./media/rollbar-tutorial/tutorial_rollbar_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://rollbar.com/<accountname>/saml/login/azure/`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečná adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [tým podpory oblouk klienta](mailto:support@rollbar.com) k získání těchto hodnot. 

5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/rollbar-tutorial/tutorial_rollbar_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/rollbar-tutorial/tutorial_general_400.png)
    
7. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti oblouk jako správce.

8. Klikněte na **nastavení profilu** v pravém horním rohu a pak klikněte na tlačítko **nastavení názvu účtu**.
    
    ![Konfigurace](./media/rollbar-tutorial/general.png)

9. Klikněte na tlačítko **zprostředkovatele Identity** v části zabezpečení.

    ![Konfigurace](./media/rollbar-tutorial/configure1.png)

10. V **poskytovatele Identity SAML** část, proveďte následující kroky:
    
    ![Konfigurace](./media/rollbar-tutorial/configure2.png)

    a. Vyberte **AZURE** z **poskytovatele Identity SAML** rozevíracího seznamu.

    b. V poznámkovém bloku otevřete váš soubor metadat, obsah je zkopírujte do schránky a vložte jej do **SAML Metadata** textové pole.

    c. Klikněte na **Uložit**.

11. Po kliknutí na toto tlačítko, na obrazovce bude takto:
    
    ![Konfigurace](./media/rollbar-tutorial/configure3.png)
    > [!NOTE] 
    > Aby bylo možné provést následující krok, je nejprve nutno přidat sami jako uživatel na oblouk aplikaci v Azure.
    a. Pokud chcete vyžadovat od všech uživatelů k ověřování prostřednictvím Azure a pak klikněte na **přihlásit pomocí zprostředkovatele identity** znovu provést ověření pomocí Azure.  

    b.  Jakmile jste vráceni na obrazovku, vyberte **vyžadují přihlášení prostřednictvím poskytovatele Identity SAML** zaškrtávací políčko.

    b. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/rollbar-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/rollbar-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/rollbar-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/rollbar-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-rollbar-test-user"></a>Vytvoření zkušebního uživatele oblouk

Pokud chcete povolit uživatelům Azure AD přihlášení na oblouk, musí být zřízená do oblouk. V případě oblouk zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k serveru vaší společnosti oblouk jako správce.

2. Klikněte na **nastavení profilu** v pravém horním rohu a pak klikněte na tlačítko **nastavení názvu účtu**.

    ![Uživatel](./media/rollbar-tutorial/general.png)

3. Klikněte na tlačítko **uživatelé**.
    
    ![Můžete přidat zaměstnance](./media/rollbar-tutorial/user1.png)

4. Klikněte na tlačítko **pozvat členové týmu**.

    ![Pozvat uživatele](./media/rollbar-tutorial/user2.png)

5. Do textového pole zadejte jméno uživatele, jako je **brittasimon@contoso.com** a kliknutím na možnost **přidat/pozvání**.

    ![Pozvat uživatele](./media/rollbar-tutorial/user3.png)

6. Uživatel obdrží Pozvánka a po jeho přijetí si vytvoří v systému.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu oblouk.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon oblouk, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **oblouk**.

    ![V seznamu aplikací na odkaz na oblouk](./media/rollbar-tutorial/tutorial_rollbar_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici oblouk na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci oblouk.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/rollbar-tutorial/tutorial_general_01.png
[2]: ./media/rollbar-tutorial/tutorial_general_02.png
[3]: ./media/rollbar-tutorial/tutorial_general_03.png
[4]: ./media/rollbar-tutorial/tutorial_general_04.png

[100]: ./media/rollbar-tutorial/tutorial_general_100.png

[200]: ./media/rollbar-tutorial/tutorial_general_200.png
[201]: ./media/rollbar-tutorial/tutorial_general_201.png
[202]: ./media/rollbar-tutorial/tutorial_general_202.png
[203]: ./media/rollbar-tutorial/tutorial_general_203.png

