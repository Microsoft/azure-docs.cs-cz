---
title: 'Kurz: Azure Active Directory integrace s Pingboard | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Pingboard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: e07e85e60c8a4b93e4b0fd7bf43f470c4e3acc61
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221185"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Kurz: Azure Active Directory integrace s Pingboard

V tomto kurzu zjistěte, jak integrovat Pingboard s Azure Active Directory (Azure AD).

Integrace Pingboard s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Pingboard
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Pingboard (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Pingboard, potřebujete následující položky:

- Předplatné služby Azure AD
- Pingboard jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Pingboard z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-pingboard-from-the-gallery"></a>Přidání Pingboard z Galerie
Při konfiguraci integrace Pingboard do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Pingboard z galerie.

**Pokud chcete přidat Pingboard z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Podnikové aplikace][2]

3. Klikněte na tlačítko **přidat** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Pingboard**, vyberte **Pingboard** z panelu výsledek a pak klikněte na tlačítko **přidat** tlačítko Přidat aplikaci.

    ![Pingboard v seznamu výsledků](./media/pingboard-tutorial/tutorial_pingboard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Pingboard podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Pingboard je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Pingboard musí navázat.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Pingboard.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Pingboard, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Pingboard](#create-a-pingboard-test-user)**  – Pokud chcete mít protějšek Britta Simon v Pingboard propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Pingboard.

**Ke konfiguraci Azure AD jednotné přihlašování s Pingboard, proveďte následující kroky:**

1. Na portálu Azure na **Pingboard** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2.  Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Jediné přihlášení dialogové okno](./media/pingboard-tutorial/tutorial_pingboard_samlbase.png)

3. Na **Pingboard domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Pingboard domény a adresy URL jednotné přihlašování informace IDP](./media/pingboard-tutorial/tutorial_pingboard_url.png)

    a. V **identifikátor** textovému poli, zadejte hodnotu jako: `http://app.pingboard.com/sp`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<entity-id>.pingboard.com/auth/saml/consume`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL**, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Pingboard domény a adresy URL jednotné přihlašování informace SP](./media/pingboard-tutorial/tutorial_pingboard_sp_initiated01.png)

     V **přihlašovací adresa URL** textovému poli, zadejte adresu URL, pomocí následujícího vzorce: `https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE]
    > Upozorňujeme, že tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečná adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [tým podpory Pingboard klienta](https://support.pingboard.com/) k získání těchto hodnot.

5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor XML ve vašem počítači.

    ![Soubor xml s metadaty Pingboard](./media/pingboard-tutorial/tutorial_pingboard_certificate.png)

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/pingboard-tutorial/tutorial_general_400.png)

7. Na straně Pingboard nakonfigurovat jednotné přihlašování, otevřete nové okno prohlížeče a přihlaste se k účtu Pingboard. Musí být správcem Pingboard nastavit jednotné přihlašování.

8. V hlavní nabídce,, vyberte **aplikace > integrace**

    ![Konfigurovat jednotné přihlašování](./media/pingboard-tutorial/Pingboard_integration.png)

9. Na **integrace** stránky, vyhledejte **"Azure Active Directory"** dlaždici a klikněte na něj.

    ![Pingboard Single Sign-On integrace](./media/pingboard-tutorial/Pingboard_aad.png)

10. V modální, který následuje dále klikněte na tlačítko **"Konfigurace"**

    ![Tlačítko Pingboard konfigurace](./media/pingboard-tutorial/Pingboard_configure.png)

11. Na následující stránce si všimnete, že "Integrace se službou Azure jednotného přihlašování k dispozici". Otevřete soubor stažený soubor XML s metadaty v programu Poznámkový blok a vložte obsah **IDP Metadata**.

    ![Jednotné přihlašování Pingboard konfigurační obrazovce](./media/pingboard-tutorial/Pingboard_sso_configure.png)

12. Soubor se ověří a pokud se vše, co je správná, jednotné přihlašování bude nyní povolena.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](./media/pingboard-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/pingboard-tutorial/create_aaduser_02.png)

3. V horní části okna klikněte na položku **přidat** otevřete **uživatele** dialogové okno.

    ![Tlačítko Přidat](./media/pingboard-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/pingboard-tutorial/create_aaduser_04.png)

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-pingboard-test-user"></a>Vytvoření zkušebního uživatele Pingboard

Cílem této části je vytvoření uživatele v Pingboard nazývá Britta Simon. Pingboard podporuje automatické uživatele zřizování, který je ve výchozím nastavení povolené. Můžete najít další podrobnosti o [sem](pingboard-provisioning-tutorial.md) na tom, jak nakonfigurovat uživatele automatické zřizování.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

1. Přihlaste se k serveru vaší společnosti Pingboard jako správce.

2. Klikněte na tlačítko **"Přidat zaměstnancem"** tlačítko **Directory** stránky.

    ![Můžete přidat zaměstnance](./media/pingboard-tutorial/create_testuser_add.png)

3. Na **"Přidat zaměstnancem"** dialogové okno proveďte následující kroky:

    ![Pozvat uživatele](./media/pingboard-tutorial/create_testuser_name.png)

    a. V **úplný název** jako typ celé jméno uživatele k textovému poli, **Britta Simon**.

    b. V **e-mailu** jako typ e-mailovou adresu uživatele k textovému poli, **brittasimon@contoso.com**.

    c. V **funkce** textovému poli, zadejte název úlohy Britta Simon.

    d. V **umístění** rozevíracího seznamu, vyberte umístění Britta Simon.

    e. Klikněte na tlačítko **Add** (Přidat).

4. Obrazovka s potvrzením se dodává potvrďte přidání uživatele.

    ![Potvrďte](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mailu a dodržuje odkaz potvrďte svůj účet, pak se změní na aktivní.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Pingboard.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Pingboard, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Pingboard**.

    ![V seznamu aplikací na Pingboard odkaz](./media/pingboard-tutorial/tutorial_pingboard_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

Když kliknete na dlaždici Pingboard na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Pingboard.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfiguraci zřizování uživatelů](pingboard-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/pingboard-tutorial/tutorial_general_01.png
[2]: ./media/pingboard-tutorial/tutorial_general_02.png
[3]: ./media/pingboard-tutorial/tutorial_general_03.png
[4]: ./media/pingboard-tutorial/tutorial_general_04.png

[100]: ./media/pingboard-tutorial/tutorial_general_100.png

[200]: ./media/pingboard-tutorial/tutorial_general_200.png
[201]: ./media/pingboard-tutorial/tutorial_general_201.png
[202]: ./media/pingboard-tutorial/tutorial_general_202.png
[203]: ./media/pingboard-tutorial/tutorial_general_203.png