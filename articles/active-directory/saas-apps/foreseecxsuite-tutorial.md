---
title: 'Kurz: Azure Active Directory integrace s předvídáte CX Suite | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a předvídáte CX Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5f4b7830-6186-4d17-b77b-504d4192bfde
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: jeedes
ms.openlocfilehash: 5cf217de4973fd06935d88381de86520df604c29
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35901808"
---
# <a name="tutorial-azure-active-directory-integration-with-foresee-cx-suite"></a>Kurz: Azure Active Directory integrace s předvídáte Suite CX

V tomto kurzu zjistěte, jak integrovat předvídáte Suite CX s Azure Active Directory (Azure AD).

Integrace s Azure AD předvídáte CX Suite nabízí následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k předvídáte CX Suite.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k předvídáte Suite CX (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace Azure AD integrace se sadou Suite CX předvídáte, potřebujete následující položky:

- Předplatné služby Azure AD
- Předvídáte Suite CX jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání předvídáte Suite CX z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-foresee-cx-suite-from-the-gallery"></a>Přidání předvídáte Suite CX z Galerie
Pokud chcete nakonfigurovat integraci sady CX předvídáte do služby Azure AD, potřebujete přidat předvídáte Suite CX z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat předvídáte Suite CX z galerie, postupujte takto:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **předvídáte Suite CX**, vyberte **předvídáte Suite CX** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![V seznamu výsledků předvídáte CX Suite](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s předvídáte CX Suite podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v sadě CX předvídáte, je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v sadě CX předvídáte, je potřeba vytvořit.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s předvídáte CX Suite, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele předvídáte Suite CX](#create-a-foresee-cx-suite-test-user)**  – Pokud chcete mít protějšek Britta Simon v předvídáte CX Suite propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci předvídáte CX Suite.

**Ke konfiguraci Azure AD jednotné přihlašování se sadou Suite CX předvídáte, proveďte následující kroky:**

1. Na portálu Azure na **předvídáte Suite CX** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Jediné přihlášení dialogové okno](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_samlbase.png)

3. Na **předvídáte CX Suite domény a adresy URL** část, pokud máte **soubor metadat poskytovatele služeb**, proveďte následující kroky:

    ![Předvídáte CX Suite adresy URL jeden přihlašování informace o doméně a](./media/foreseecxsuite-tutorial/upload.png)

    a. Klikněte na tlačítko **metadata souboru k odeslání**.

    ![Předvídáte CX Suite adresy URL jeden přihlašování informace o doméně a](./media/foreseecxsuite-tutorial/tutorial_foreseen_uploadconfig.png)

    b. Klikněte na **složky logo** vyberte soubor metadat a klikněte na tlačítko **nahrát**.

    c. Po úspěšném dokončení odesílání **soubor metadat poskytovatele služeb** **identifikátor** hodnotu get automaticky vložené do **předvídáte CX Suite domény a adresy URL** části textové pole, jak je uvedeno níže:

    ![Předvídáte CX Suite adresy URL jeden přihlašování informace o doméně a](./media/foreseecxsuite-tutorial/urlupload.png)

4. Pokud nemáte **soubor metadat poskytovatele služeb**, proveďte následující kroky:

    ![Předvídáte CX Suite adresy URL jeden přihlašování informace o doméně a](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL: `https://cxsuite.foresee.com/`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://www.okta.com/saml2/service-provider/<UniqueID>`

    > [!NOTE]
    > Hodnota identifikátoru není skutečné. Aktualizujte tato hodnota se skutečným identifikátorem. Obraťte se na [tým podpory předvídáte, klienta sady CX](mailto:support@foresee.com) získat tuto hodnotu.

5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_certificate.png)

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/foreseecxsuite-tutorial/tutorial_general_400.png)

7. Konfigurace jednotného přihlašování na **předvídáte Suite CX** straně, budete muset odeslat stažené **soubor XML s metadaty** k [tým podpory předvídáte Suite CX](mailto:support@foresee.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/foreseecxsuite-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/foreseecxsuite-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/foreseecxsuite-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/foreseecxsuite-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-foresee-cx-suite-test-user"></a>Vytvoření zkušebního uživatele předvídáte Suite CX

V této části vytvoříte uživatele volal Britta Simon v předvídáte CX Suite. Práce s [tým podpory předvídáte Suite CX](mailto:support@foresee.com) přidat uživatele nebo doménu, která je potřeba na seznam povolených adres v platformě předvídáte CX Suite. Pokud doména je přidána tým, se získat uživatelé automaticky zřizovat pro platformu předvídáte CX Suite. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k předvídáte CX Suite.

![Přiřadit role uživatele][200]

**Pokud chcete přiřadit Britta Simon předvídáte CX Suite, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **předvídáte Suite CX**.

    ![Odkaz předvídáte Suite CX v seznamu aplikací](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici předvídáte Suite CX na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci předvídáte CX Suite.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/foreseecxsuite-tutorial/tutorial_general_01.png
[2]: ./media/foreseecxsuite-tutorial/tutorial_general_02.png
[3]: ./media/foreseecxsuite-tutorial/tutorial_general_03.png
[4]: ./media/foreseecxsuite-tutorial/tutorial_general_04.png

[100]: ./media/foreseecxsuite-tutorial/tutorial_general_100.png

[200]: ./media/foreseecxsuite-tutorial/tutorial_general_200.png
[201]: ./media/foreseecxsuite-tutorial/tutorial_general_201.png
[202]: ./media/foreseecxsuite-tutorial/tutorial_general_202.png
[203]: ./media/foreseecxsuite-tutorial/tutorial_general_203.png

