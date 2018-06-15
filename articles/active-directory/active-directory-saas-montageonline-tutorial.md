---
title: 'Kurz: Azure Active Directory integrace se službou Fotomontáž Online | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Fotomontáž Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5c5e8c6f-e4fb-43fe-8841-e371f568ebed
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: jeedes
ms.openlocfilehash: e5cabbd26c978bc8bcdabeadfca31ae99c951558
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34342999"
---
# <a name="tutorial-azure-active-directory-integration-with-montage-online"></a>Kurz: Azure Active Directory integrace se službou Fotomontáž Online

V tomto kurzu zjistěte, jak integrovat Fotomontáž Online s Azure Active Directory (Azure AD).

Integrace Fotomontáž Online s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Fotomontáž Online.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Fotomontáž Online (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Fotomontáž Online, potřebujete následující položky:

- Předplatné služby Azure AD
- Fotomontáž Online jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Fotomontáž Online z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-montage-online-from-the-gallery"></a>Přidání Fotomontáž Online z Galerie
Při konfiguraci integrace služby Fotomontáž Online do služby Azure AD, musíte přidat Fotomontáž Online z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Fotomontáž Online z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Fotomontáž Online**, vyberte **Fotomontáž Online** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Fotomontáž Online v seznamu výsledků](./media/active-directory-saas-montageonline-tutorial/tutorial_montageonline_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Fotomontáž Online na základě testovací uživatele, nazývá "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Fotomontáž Online je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah propojení mezi uživatele Azure AD a související uživatelské v Fotomontáž Online.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Fotomontáž Online, musíte dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Fotomontáž Online](#create-a-montage-online-test-user)**  – Pokud chcete mít protějšek Britta Simon v Fotomontáž Online propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Fotomontáž Online.

**Ke konfiguraci Azure AD jednotné přihlašování s Fotomontáž Online, proveďte následující kroky:**

1. Na portálu Azure na **Fotomontáž Online** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-montageonline-tutorial/tutorial_montageonline_samlbase.png)

3. Na **Fotomontáž Online domény a adresy URL** část, proveďte následující kroky:

    ![Fotomontáž Online domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-montageonline-tutorial/tutorial_montageonline_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce:

    Pro produkční prostředí: `https://<subdomain>.montageonline.co.nz/`

    Pro testovací prostředí: `https://build-<subdomain>.montageonline.co.nz/`

    b. V **identifikátor** textovému poli, zadejte adresu URL:

    Pro produkční prostředí: `MOL_Azure`

    Pro testovací prostředí: `MOL_Azure_Build`

    > [!NOTE] 
    > Hodnota přihlašovací adresa URL není skutečné. Aktualizujte hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory Online klienta Fotomontáž](https://www.montage.co.nz/contact-us/) k získání hodnoty. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-montageonline-tutorial/tutorial_montageonline_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-montageonline-tutorial/tutorial_general_400.png)

6. Na **Online konfigurace Fotomontáž** klikněte na tlačítko **konfigurace Fotomontáž Online** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace Online Fotomontáž](./media/active-directory-saas-montageonline-tutorial/tutorial_montageonline_configure.png) 

7. Konfigurace jednotného přihlašování na **Fotomontáž Online** straně, budete muset odeslat stažené **certifikát (Base64), adresu URL Sign-Out, SAML Entity ID a SAML jeden přihlašování adresa URL služby** k [Fotomontáž Tým podpory online](https://www.montage.co.nz/contact-us/). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-montageonline-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-montageonline-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-montageonline-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-montageonline-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-montage-online-test-user"></a>Vytvoření Online Fotomontáž zkušebního uživatele

V této části vytvoříte uživatele volal Britta Simon v Fotomontáž Online. Práce s [Fotomontáž Online tým podpory](https://www.montage.co.nz/contact-us/) přidat uživatele do Fotomontáž Online platformy. Uživatelé musí být vytvořen a aktivovat před použitím jednotného přihlašování

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon tak, že udělíte přístup k Fotomontáž Online používat Azure jednotné přihlašování.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Fotomontáž Online, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Fotomontáž Online**.

    ![V seznamu aplikací na Fotomontáž Online odkaz](./media/active-directory-saas-montageonline-tutorial/tutorial_montageonline_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Fotomontáž Online na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Fotomontáž Online.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-montageonline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-montageonline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-montageonline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-montageonline-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-montageonline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-montageonline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-montageonline-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-montageonline-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-montageonline-tutorial/tutorial_general_203.png

