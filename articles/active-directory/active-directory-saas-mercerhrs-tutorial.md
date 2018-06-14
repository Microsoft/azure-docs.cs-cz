---
title: 'Kurz: Azure Active Directory integrace s BenefitsCentral Mercer (MBC) | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Mercer BenefitsCentral (MBC).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3788b28c-49aa-4208-9acd-630362008e89
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: jeedes
ms.openlocfilehash: c0a9fc6c906af96bd613c02e8b4248fc2125d9e1
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34346124"
---
# <a name="tutorial-azure-active-directory-integration-with-mercer-benefitscentral-mbc"></a>Kurz: Azure Active Directory integrace s BenefitsCentral Mercer (MBC)

V tomto kurzu zjistěte, jak integrovat Mercer BenefitsCentral (MBC) s Azure Active Directory (Azure AD).

Integrace Mercer BenefitsCentral (MBC) s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k BenefitsCentral Mercer (MBC).
- Můžete povolit uživatelům, aby automaticky získat přihlášeného k Mercer BenefitsCentral (MBC) (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s BenefitsCentral Mercer (MBC), potřebujete následující položky:

- Předplatné služby Azure AD
- Mercer BenefitsCentral (MBC) jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Mercer BenefitsCentral (MBC) z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-mercer-benefitscentral-mbc-from-the-gallery"></a>Přidání Mercer BenefitsCentral (MBC) z Galerie
Při konfiguraci integrace BenefitsCentral Mercer (MBC) do služby Azure AD, potřebujete přidat Mercer BenefitsCentral (MBC) z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Mercer BenefitsCentral (MBC) z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Mercer BenefitsCentral (MBC)**, vyberte **Mercer BenefitsCentral (MBC)** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Mercer BenefitsCentral (MBC) v seznamu výsledků](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s BenefitsCentral Mercer (MBC) podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějšku BenefitsCentral Mercer (MBC) je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah propojení mezi uživatele Azure AD a související uživatelské BenefitsCentral Mercer (MBC).

V Mercer BenefitsCentral (MBC), přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s BenefitsCentral Mercer (MBC), je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Mercer BenefitsCentral (MBC)](#create-a-mercer-benefitscentral-mbc-test-user)**  – Pokud chcete mít protějšek Britta Simon v Mercer BenefitsCentral (MBC) propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Mercer BenefitsCentral (MBC).

**Pokud chcete konfigurovat Azure AD jednotné přihlašování s BenefitsCentral Mercer (MBC), proveďte následující kroky:**

1. Na portálu Azure na **Mercer BenefitsCentral (MBC)** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_samlbase.png)

3. Na **Mercer BenefitsCentral (MBC) domény a adresy URL** část, proveďte následující kroky:

    ![Mercer BenefitsCentral (MBC) domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `stg.mercerhrs.com/saml2.0`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://ssous-stg.mercerhrs.com/SP2/Saml2AssertionConsumer.aspx`

    > [!NOTE] 
    > Hodnota adresy URL a odpovědi není skutečné. Aktualizujte tuto hodnotu s skutečná adresa URL odpovědi. Obraťte se na [tým podpory Mercer BenefitsCentral (MBC)](https://www.mercer.com/contact-us.html) získat tuto hodnotu.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_400.png)

6. Na **Mercer BenefitsCentral (MBC) konfigurace** klikněte na tlačítko **konfigurace BenefitsCentral Mercer (MBC)** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace mercer BenefitsCentral (MBC)](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_configure.png) 

7. Konfigurace jednotného přihlašování na **Mercer BenefitsCentral (MBC)** straně, budete muset odeslat stažené **soubor XML s metadaty** a **SAML jeden přihlašování adresa URL služby** k [Tým podpory mercer BenefitsCentral (MBC)](https://www.mercer.com/contact-us.html). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-mercerhrs-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-mercerhrs-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-mercerhrs-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-mercerhrs-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-mercer-benefitscentral-mbc-test-user"></a>Vytvoření zkušebního uživatele Mercer BenefitsCentral (MBC)

V této části vytvoříte uživatele volal Britta Simon v Mercer hodin. Práce s [tým podpory Mercer BenefitsCentral (MBC)](https://www.mercer.com/contact-us.html) přidat uživatele do platformy Mercer hodin. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon chcete použít Azure jednotného přihlašování k udělení přístupu k BenefitsCentral Mercer (MBC).

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon BenefitsCentral Mercer (MBC), proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Mercer BenefitsCentral (MBC)**.

    ![V seznamu aplikací na odkaz Mercer BenefitsCentral (MBC)](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Mercer BenefitsCentral (MBC) na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Mercer BenefitsCentral (MBC).
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_203.png

