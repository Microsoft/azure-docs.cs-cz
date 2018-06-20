---
title: 'Kurz: Azure Active Directory integrace s hejna | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a hejna.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7b2c3ac5-17f1-49a0-8961-c541b258d4b1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: 633828cc7e244dca1e43c45852910211aeda68d9
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36229491"
---
# <a name="tutorial-azure-active-directory-integration-with-flock"></a>Kurz: Azure Active Directory integrace s hejna

V tomto kurzu zjistěte, jak integrovat hejna s Azure Active Directory (Azure AD).

Integrační hejna s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k hejna.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k hejna (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s hejna, potřebujete následující položky:

- Předplatné služby Azure AD
- Hejna jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání hejna z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-flock-from-the-gallery"></a>Přidání hejna z Galerie
Při konfiguraci integrace hejna do služby Azure AD, potřebujete přidat hejna z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat hejna z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **hejna**, vyberte **hejna** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Hejno v seznamu výsledků](./media/flock-tutorial/tutorial_flock_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s hejna podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co příslušného uživatele v hejna je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské ve stádě musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s hejna, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele hejna](#create-a-flock-test-user)**  – Pokud chcete mít protějšek Britta Simon v hejna propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci hejna.

**Ke konfiguraci Azure AD jednotné přihlašování s hejna, proveďte následující kroky:**

1. Na portálu Azure na **hejna** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Jediné přihlášení dialogové okno](./media/flock-tutorial/tutorial_flock_samlbase.png)

3. Na **hejna domény a adresy URL** část, proveďte následující kroky:

    ![Hejna domény a adresy URL jednotné přihlašování informace](./media/flock-tutorial/tutorial_flock_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.flock.com/`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.flock.com/`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory hejna klienta](mailto:support@flock.com) k získání těchto hodnot.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/flock-tutorial/tutorial_flock_certificate.png)

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/flock-tutorial/tutorial_general_400.png)

6. Na **hejna konfigurace** klikněte na tlačítko **konfigurace hejna** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresu URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace hejna](./media/flock-tutorial/tutorial_flock_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se na váš web společnosti hejna jako správce.

8. Vyberte **ověřování** z levém navigačním panelu a pak vyberte **ověřování SAML**.

    ![Konfigurace hejna](./media/flock-tutorial/configure1.png)

9. V **ověřování SAML** část, proveďte následující kroky:

    ![Konfigurace hejna](./media/flock-tutorial/configure2.png)

    a. V **SAML 2.0 Endpoint(HTTP)** textovému poli, vložte **SAML jeden přihlašování adresa URL služby** hodnotu, která jste zkopírovali z portálu Azure.

    b. V **vystavitele zprostředkovatele Identity** textovému poli, vložte **SAML Entity ID** hodnotu, která jste zkopírovali z portálu Azure.

    c. Otevřete stažené **Certificate(Base64)** z portálu Azure v programu Poznámkový blok, vložte obsah do **veřejný certifikát** textové pole.

    d. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/flock-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/flock-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/flock-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/flock-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-flock-test-user"></a>Vytvoření zkušebního uživatele hejna

Pokud chcete povolit uživatelům Azure AD přihlášení na stádo, musí být zřízená do hejna. V případě hejna zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k serveru vaší společnosti hejna jako správce.

2. Klikněte na tlačítko **spravovat Team** z levého navigačního panelu.

    ![Můžete přidat zaměstnance](./media/flock-tutorial/user1.png)

3. Klikněte na tlačítko **přidat člena** a pak vyberte **členové týmu**.

    ![Můžete přidat zaměstnance](./media/flock-tutorial/user2.png)

4. Zadejte e-mailovou adresu uživatele, jako je **Brittasimon@contoso.com** a pak vyberte **přidat uživatele**.

    ![Můžete přidat zaměstnance](./media/flock-tutorial/user3.png)

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu hejna.

![Přiřadit role uživatele][200]

**Pokud chcete přiřadit Britta Simon hejna, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **hejna**.

    ![V seznamu aplikací na hejna odkaz](./media/flock-tutorial/tutorial_flock_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici hejna na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci hejna.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/flock-tutorial/tutorial_general_01.png
[2]: ./media/flock-tutorial/tutorial_general_02.png
[3]: ./media/flock-tutorial/tutorial_general_03.png
[4]: ./media/flock-tutorial/tutorial_general_04.png

[100]: ./media/flock-tutorial/tutorial_general_100.png

[200]: ./media/flock-tutorial/tutorial_general_200.png
[201]: ./media/flock-tutorial/tutorial_general_201.png
[202]: ./media/flock-tutorial/tutorial_general_202.png
[203]: ./media/flock-tutorial/tutorial_general_203.png
