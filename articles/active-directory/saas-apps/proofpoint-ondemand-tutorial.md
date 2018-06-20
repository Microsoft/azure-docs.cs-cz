---
title: 'Kurz: Azure Active Directory integrace s Proofpoint na vyžádání | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Proofpoint na vyžádání.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 773e7f7d-ec31-411b-860d-6a6633335d43
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: jeedes
ms.openlocfilehash: ee38ea88ef5f74e4e5a9a78f5253812ecc27698f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36224092"
---
# <a name="tutorial-azure-active-directory-integration-with-proofpoint-on-demand"></a>Kurz: Azure Active Directory integrace s Proofpoint na vyžádání

V tomto kurzu zjistěte, jak integrovat Proofpoint na vyžádání s Azure Active Directory (Azure AD).

Integrace Proofpoint na vyžádání s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Proofpoint na vyžádání.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Proofpoint na vyžádání (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Proofpoint na vyžádání, potřebujete následující položky:

- Předplatné služby Azure AD
- Proofpoint na vyžádání jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Proofpoint na vyžádání z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-proofpoint-on-demand-from-the-gallery"></a>Přidání Proofpoint na vyžádání z Galerie
Chcete-li nakonfigurovat integraci Proofpoint na vyžádání do služby Azure AD, přidejte Proofpoint na vyžádání z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Proofpoint na vyžádání z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Proofpoint na vyžádání**, vyberte **Proofpoint na vyžádání** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Proofpoint na vyžádání v seznamu výsledků](./media/proofpoint-ondemand-tutorial/tutorial_proofpointondemand_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části konfiguraci a testování Azure AD jednotné přihlašování s Proofpoint na vyžádání v testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Proofpoint na vyžádání je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah propojení mezi uživatele Azure AD a související uživatelské v Proofpoint na vyžádání.

V Proofpoint na vyžádání, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Proofpoint na vyžádání, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření Proofpoint na vyžádání testovacího uživatele](#create-a-proofpoint-on-demand-test-user)**  – Pokud chcete mít protějšek Britta Simon v Proofpoint na vyžádání, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v vaší Proofpoint na vyžádání aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s Proofpoint na vyžádání, proveďte následující kroky:**

1. Na portálu Azure na **Proofpoint na vyžádání** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/proofpoint-ondemand-tutorial/tutorial_proofpointondemand_samlbase.png)

3. Na **Proofpoint na vyžádání domény a adresy URL** část, proveďte následující kroky:

    ![Proofpoint na vyžádání adresy URL jeden přihlašování informace o doméně a](./media/proofpoint-ondemand-tutorial/tutorial_proofpointondemand_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<hostname>.pphosted.com/ppssamlsp_hostname`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<hostname>.pphosted.com/ppssamlsp`

    c. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<hostname>.pphosted.com:portnumber/v1/samlauth/samlconsumer`
    
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [Proofpoint na tým podpory vyžádání klienta](https://www.proofpoint.com/us/support-services) k získání těchto hodnot.

5. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/proofpoint-ondemand-tutorial/tutorial_proofpointondemand_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/proofpoint-ondemand-tutorial/tutorial_general_400.png)
    
7. Na **Proofpoint konfiguraci na vyžádání** klikněte na tlačítko **Proofpoint konfiguraci na vyžádání** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Proofpoint konfiguraci na vyžádání](./media/proofpoint-ondemand-tutorial/tutorial_proofpointondemand_configure.png) 

8. Konfigurace jednotného přihlašování na **Proofpoint na vyžádání** straně, budete muset odeslat stažené **Certificate(Base64)**, **Sign-Out URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** k [Proofpoint na tým podpory vyžádání](https://www.proofpoint.com/us/support-services). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/proofpoint-ondemand-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/proofpoint-ondemand-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/proofpoint-ondemand-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/proofpoint-ondemand-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-proofpoint-on-demand-test-user"></a>Vytvoření Proofpoint na vyžádání testovacího uživatele

V této části vytvoříte uživatele volal Britta Simon v Proofpoint na vyžádání. Práce s [Proofpoint na tým podpory vyžádání klienta](https://www.proofpoint.com/us/support-services) pro přidání uživatelů Proofpoint na vyžádání platformě.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Proofpoint na vyžádání.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Proofpoint na vyžádání, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Proofpoint na vyžádání**.

    ![Proofpoint na vyžádání odkaz v seznamu aplikací](./media/proofpoint-ondemand-tutorial/tutorial_proofpointondemand_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Po kliknutí na tlačítko Proofpoint na vyžádání dlaždici na přístupovém panelu, jste měli získat automaticky přihlášení k vaší Proofpoint na vyžádání aplikace.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/proofpoint-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/proofpoint-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/proofpoint-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/proofpoint-ondemand-tutorial/tutorial_general_04.png

[100]: ./media/proofpoint-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/proofpoint-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/proofpoint-ondemand-tutorial/tutorial_general_201.png
[202]: ./media/proofpoint-ondemand-tutorial/tutorial_general_202.png
[203]: ./media/proofpoint-ondemand-tutorial/tutorial_general_203.png

