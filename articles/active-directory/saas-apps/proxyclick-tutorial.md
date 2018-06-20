---
title: 'Kurz: Azure Active Directory integrace s Proxyclick | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: jeedes
ms.openlocfilehash: 6e0d8c5c178afc63c9c177d31d0a14104b127941
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36213334"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Kurz: Azure Active Directory integrace s Proxyclick

V tomto kurzu zjistěte, jak integrovat Proxyclick s Azure Active Directory (Azure AD).

Integrace Proxyclick s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Proxyclick.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Proxyclick (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Proxyclick, potřebujete následující položky:

- Předplatné služby Azure AD
- Proxyclick jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Proxyclick z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-proxyclick-from-the-gallery"></a>Přidání Proxyclick z Galerie
Při konfiguraci integrace Proxyclick do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Proxyclick z galerie.

**Pokud chcete přidat Proxyclick z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Proxyclick**, vyberte **Proxyclick** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Proxyclick v seznamu výsledků](./media/proxyclick-tutorial/tutorial_proxyclick_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Proxyclick podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Proxyclick je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Proxyclick musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Proxyclick, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Proxyclick](#create-a-proxyclick-test-user)**  – Pokud chcete mít protějšek Britta Simon v Proxyclick propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Proxyclick.

**Ke konfiguraci Azure AD jednotné přihlašování s Proxyclick, proveďte následující kroky:**

1. Na portálu Azure na **Proxyclick** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Jediné přihlášení dialogové okno](./media/proxyclick-tutorial/tutorial_proxyclick_samlbase.png)

3. Na **Proxyclick domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Proxyclick domény a adresy URL jednotné přihlašování informace](./media/proxyclick-tutorial/tutorial_proxyclick_url2.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://saml.proxyclick.com/init/<companyId>`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://saml.proxyclick.com/consume/<companyId>`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Proxyclick domény a adresy URL jednotné přihlašování informace](./media/proxyclick-tutorial/tutorial_proxyclick_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://saml.proxyclick.com/init/<companyId>`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL, který je vysvětlen později v tomto kurzu.

5. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/proxyclick-tutorial/tutorial_proxyclick_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/proxyclick-tutorial/tutorial_general_400.png)

7. Na **Proxyclick konfigurace** klikněte na tlačítko **konfigurace Proxyclick** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresu URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace Proxyclick](./media/proxyclick-tutorial/tutorial_proxyclick_configure.png)

8. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti Proxyclick jako správce.

9. Vyberte **účet & nastavení**.

    ![Konfigurace Proxyclick](./media/proxyclick-tutorial/configure1.png)

10. Přejděte dolů k položce **integrace** a vyberte **SAML**.

    ![Konfigurace Proxyclick](./media/proxyclick-tutorial/configure2.png)

11. V **SAML** část, proveďte následující kroky:

    ![Konfigurace Proxyclick](./media/proxyclick-tutorial/configure3.png)

    a. Kopírování **SAML příjemce URL** a vložte ji do **adresa URL odpovědi** textového pole v **Proxyclick domény a adresy URL** části na portálu Azure.

    b. Kopírování **SAML jednotného přihlašování k adresy URL pro přesměrování** a vložte ji do **přihlásit na adrese URL** a **identifikátor** textových polí v **Proxyclick domény a adresy URL** části na portálu Azure.

    c. Vyberte **metoda žádosti SAML** jako **přesměrování protokolu HTTP**.

    d. V **vystavitele** textovému poli, vložte hodnotu **SAML Entity ID** hodnotu, kterou jste zkopírovali z portálu Azure.

    e. V **adresu URL koncového bodu SAML 2.0** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** zkopírovaných z portálu Azure.

    f. Z portálu Azure v poznámkovém bloku otevřete váš soubor stažený certifikát a vložte jej do **certifikát** textové pole.

    g. Klikněte na tlačítko **uložit změny**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/proxyclick-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/proxyclick-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/proxyclick-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/proxyclick-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-proxyclick-test-user"></a>Vytvoření zkušebního uživatele Proxyclick

Pokud chcete povolit uživatelům Azure AD přihlášení k Proxyclick, musí být zřízená do Proxyclick. V případě Proxyclick zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k serveru vaší společnosti Proxyclick jako správce.

2. Klikněte na tlačítko **kolegy** horním navigačním panelu.

    ![Můžete přidat zaměstnance](./media/proxyclick-tutorial/user1.png)

3. Klikněte na tlačítko **přidat kolegu**

    ![Můžete přidat zaměstnance](./media/proxyclick-tutorial/user2.png)

4. V **přidat kolegu o** část, proveďte následující kroky:

    ![Můžete přidat zaměstnance](./media/proxyclick-tutorial/user3.png)

    a. V **e-mailu** jako typ e-mailovou adresu uživatele k textovému poli, **brittasimon@contoso.com**.

    b. V **křestní jméno** textovému poli, název typu první uživatele jako Britta.

    c. V **příjmení** textovému poli, zadejte příjmení uživatele jako Simon.

    d. Klikněte na tlačítko **přidat uživatele**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Proxyclick.

![Přiřadit role uživatele][200]

**Pokud chcete přiřadit Britta Simon Proxyclick, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Proxyclick**.

    ![V seznamu aplikací na Proxyclick odkaz](./media/proxyclick-tutorial/tutorial_proxyclick_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Proxyclick na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Proxyclick.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/proxyclick-tutorial/tutorial_general_01.png
[2]: ./media/proxyclick-tutorial/tutorial_general_02.png
[3]: ./media/proxyclick-tutorial/tutorial_general_03.png
[4]: ./media/proxyclick-tutorial/tutorial_general_04.png

[100]: ./media/proxyclick-tutorial/tutorial_general_100.png

[200]: ./media/proxyclick-tutorial/tutorial_general_200.png
[201]: ./media/proxyclick-tutorial/tutorial_general_201.png
[202]: ./media/proxyclick-tutorial/tutorial_general_202.png
[203]: ./media/proxyclick-tutorial/tutorial_general_203.png

