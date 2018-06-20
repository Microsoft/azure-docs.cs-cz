---
title: 'Kurz: Azure Active Directory integrace s moconavi | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a moconavi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e1916224-e1c2-426f-b233-0a2518fa41db
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: jeedes
ms.openlocfilehash: 3467b823e6c91d34ebd48c7f8bc29558a79c59e5
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36229542"
---
# <a name="tutorial-azure-active-directory-integration-with-moconavi"></a>Kurz: Azure Active Directory integrace s moconavi

V tomto kurzu zjistěte, jak integrovat moconavi s Azure Active Directory (Azure AD).

Integrace moconavi s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k moconavi.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k moconavi (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s moconavi, potřebujete následující položky:

- Předplatné služby Azure AD
- Moconavi jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání moconavi z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-moconavi-from-the-gallery"></a>Přidání moconavi z Galerie
Při konfiguraci integrace moconavi do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS moconavi z galerie.

**Pokud chcete přidat moconavi z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **moconavi**, vyberte **moconavi** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![moconavi v seznamu výsledků](./media/moconavi-tutorial/tutorial_moconavi_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s moconavi podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v moconavi je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v moconavi musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s moconavi, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele moconavi](#create-a-moconavi-test-user)**  – Pokud chcete mít protějšek Britta Simon v moconavi propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci moconavi.

**Ke konfiguraci Azure AD jednotné přihlašování s moconavi, proveďte následující kroky:**

1. Na portálu Azure na **moconavi** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Jediné přihlášení dialogové okno](./media/moconavi-tutorial/tutorial_moconavi_samlbase.png)

3. Na **moconavi domény a adresy URL** část, proveďte následující kroky:

    ![moconavi domény a adresy URL jeden přihlašování informace](./media/moconavi-tutorial/tutorial_moconavi_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<yourserverurl>/moconavi-saml2/saml/login`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<yourserverurl>/moconavi-saml2`

    C. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<yourserverurl>/moconavi-saml2/saml/SSO`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečná adresa URL přihlašování, identifikátor a adresa URL odpovědi. Obraťte se na [tým podpory klienta moconavi](mailto:support@recomot.co.jp) k získání těchto hodnot.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/moconavi-tutorial/tutorial_moconavi_certificate.png)

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/moconavi-tutorial/tutorial_general_400.png)

6. Konfigurace jednotného přihlašování na **moconavi** straně, budete muset odeslat stažené **soubor XML s metadaty** k [tým podpory moconavi](mailto:support@recomot.co.jp). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/moconavi-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/moconavi-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/moconavi-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/moconavi-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-moconavi-test-user"></a>Vytvoření zkušebního uživatele moconavi

V této části vytvoříte volal Britta Simon v moconavi uživatele. Práce s [tým podpory moconavi](mailto:support@recomot.co.jp) přidat uživatele do moconavi platformy. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon chcete použít Azure jednotného přihlašování k udělení přístupu k moconavi.

![Přiřadit role uživatele][200]

**Pokud chcete přiřadit Britta Simon moconavi, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **moconavi**.

    ![V seznamu aplikací na moconavi odkaz](./media/moconavi-tutorial/tutorial_moconavi_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

1. Nainstalujte moconavi z Microsoft storu.

2. Spusťte moconavi.

3. Klikněte na tlačítko **připojit nastavení** tlačítko.

    ![Testování jednotné přihlašování](./media/moconavi-tutorial/testing1.png)

4. Zadejte `https://mcs-admin.moconavi.biz/gateway` do **připojit k adrese URL** textové pole a pak klikněte na **provádí** tlačítko.

    ![Testování jednotné přihlašování](./media/moconavi-tutorial/testing2.png)

5. Na následující snímek obrazovky proveďte následující kroky:

    ![Testování jednotné přihlašování](./media/moconavi-tutorial/testing3.png)

    a. Zadejte **vstup ověřovací klíč**:`azureAD` do **vstup ověřovací klíč** textové pole.

    b. Zadejte **ID uživatele vstup**: `your ad account` do **ID uživatele vstup** textové pole.

    c. Klikněte na tlačítko **přihlášení**.

6. Zadejte heslo do Azure AD **heslo** textové pole a pak klikněte na **přihlášení** tlačítko.

    ![Testování jednotné přihlašování](./media/moconavi-tutorial/testing4.png)

7. Ověřování služby Azure AD je úspěšné, když se zobrazí v nabídce.

    ![Testování jednotné přihlašování](./media/moconavi-tutorial/testing5.png)

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/moconavi-tutorial/tutorial_general_01.png
[2]: ./media/moconavi-tutorial/tutorial_general_02.png
[3]: ./media/moconavi-tutorial/tutorial_general_03.png
[4]: ./media/moconavi-tutorial/tutorial_general_04.png

[100]: ./media/moconavi-tutorial/tutorial_general_100.png

[200]: ./media/moconavi-tutorial/tutorial_general_200.png
[201]: ./media/moconavi-tutorial/tutorial_general_201.png
[202]: ./media/moconavi-tutorial/tutorial_general_202.png
[203]: ./media/moconavi-tutorial/tutorial_general_203.png

