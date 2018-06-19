---
title: 'Kurz: Azure Active Directory integrace s síti na pracovišti Autotask | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Autotask síti na pracovišti.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a9a7ff71-c389-4169-aafd-d7a505244797
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 5bc72964b24ffd3b8c53f752e822b95d6694e44e
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35898999"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Kurz: Azure Active Directory integrace s Autotask síti na pracovišti

V tomto kurzu zjistěte, jak k síti na pracovišti Autotask integraci s Azure Active Directory (Azure AD).

Integrace Autotask síti na pracovišti s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k síti na pracovišti Autotask
- Můžete povolit uživatelům, aby automaticky získat přihlášení k síti na pracovišti Autotask (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Autotask síti na pracovišti, potřebujete následující položky:

- Předplatné služby Azure AD
- Síti na pracovišti Autotask jednotného přihlašování povolené předplatné
- Musíte být správce nebo správce super v síti na pracovišti.
- Musí mít účet správce ve službě Azure AD.
- Uživatele, kteří budou tuto funkci využít, musí mít účty v síti na pracovišti a Azure AD a jejich e-mailové adresy pro obě se musí shodovat.

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Autotask síti na pracovišti z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-autotask-workplace-from-the-gallery"></a>Přidání Autotask síti na pracovišti z Galerie
Při konfiguraci integrace síti na pracovišti Autotask do služby Azure AD potřebujete přidat Autotask síti na pracovišti z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Autotask síti na pracovišti z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **síti na pracovišti Autotask**, vyberte **síti na pracovišti Autotask** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Autotask síti na pracovišti v seznamu výsledků](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurujete a testovací Azure AD jednotné přihlašování se Autotask pracovišti podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v síti na pracovišti Autotask je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživateli v síti na pracovišti Autotask musí navázat.

V síti na pracovišti Autotask, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Autotask síti na pracovišti, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele síti na pracovišti Autotask](#create-an-autotask-workplace-test-user)**  – Pokud chcete mít protějšek Britta Simon v síti na pracovišti Autotask propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Autotask síti na pracovišti.

**Ke konfiguraci Azure AD jednotné přihlašování s Autotask síti na pracovišti, proveďte následující kroky:**

1. Na portálu Azure na **síti na pracovišti Autotask** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_samlbase.png)

3. Pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu, proveďte následující kroky na **Autotask síti na pracovišti domény a adresy URL** části:

    ![Autotask síti na pracovišti domény a adresy URL jeden přihlašování informace o rozšíření IDP](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`

4. Pokud chcete nakonfigurovat aplikace **SP** initiated režimu, zkontrolujte **zobrazit upřesňující nastavení adresy URL** a proveďte následující kroky:

    ![Autotask síti na pracovišti domény a adresy URL jeden přihlašování informace pro poskytovatele služeb](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.awp.autotask.net/loginsso`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [tým podpory Autotask pracoviště klienta](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) k získání těchto hodnot. 

5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/autotaskworkplace-tutorial/tutorial_general_400.png)

7. V okně prohlížeče jiný web Přihlaste se k síti na pracovišti Online pomocí přihlašovacích údajů správce.

    >[!Note]
    >Při konfiguraci rozšíření IdP, bude potřeba zadat subdoména. Potvrďte správné subdomény přihlášení k síti na pracovišti Online. Po přihlášení, poznamenejte si poddomény v adrese URL.
    >Subdoméně je součástí mezi "https://" a ".awp.autotask.net/" a musí být v USA, Evropa, certifikační autority nebo au.

8. Přejděte na **konfigurace** > **jednotné přihlašování** a proveďte následující kroky:

    ![Autotask Single Sign-on konfigurace](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig1.png)
 
    a. Vyberte **Metadata souboru XML** možnost a pak nahrajte **soubor XML s metadaty** stáhli z portálu Azure.

    b. Klikněte na tlačítko **povolení jednotného přihlašování**.
    
    ![Autotask jednotné přihlášení schválit konfigurace](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig2.png)

    c. Vyberte **potvrzuji, tyto informace jsou správné a důvěřovat této IdP** zaškrtávací políčko.

    d. Klikněte na tlačítko **schválit**.
     
>[!Note]
>Pokud potřebujete pomoc s konfigurací Autotask síti na pracovišti, najdete v tématu [tuto stránku](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) získat pomoc s pracovního účtu.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/autotaskworkplace-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/autotaskworkplace-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/autotaskworkplace-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/autotaskworkplace-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-an-autotask-workplace-test-user"></a>Vytvoření zkušebního uživatele síti na pracovišti Autotask

V této části vytvoříte volal Britta Simon v Autotask uživatele. Spojte se s [tým podpory síti na pracovišti Autotask](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) přidání uživatelů v síti na pracovišti Autotask platformy.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k síti na pracovišti Autotask.

![Přiřadit role uživatele][200] 

**Britta Simon přiřadit k síti na pracovišti Autotask, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Autotask síti na pracovišti**.

    ![V seznamu aplikací na odkaz Autotask síti na pracovišti](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Autotask síti na pracovišti na přístupovém panelu, můžete by měl získat automaticky přihlášení k síti na pracovišti Autotask aplikace.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/autotaskworkplace-tutorial/tutorial_general_01.png
[2]: ./media/autotaskworkplace-tutorial/tutorial_general_02.png
[3]: ./media/autotaskworkplace-tutorial/tutorial_general_03.png
[4]: ./media/autotaskworkplace-tutorial/tutorial_general_04.png

[100]: ./media/autotaskworkplace-tutorial/tutorial_general_100.png

[200]: ./media/autotaskworkplace-tutorial/tutorial_general_200.png
[201]: ./media/autotaskworkplace-tutorial/tutorial_general_201.png
[202]: ./media/autotaskworkplace-tutorial/tutorial_general_202.png
[203]: ./media/autotaskworkplace-tutorial/tutorial_general_203.png

