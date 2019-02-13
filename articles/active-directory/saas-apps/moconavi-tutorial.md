---
title: 'Kurz: Integrace Azure Active Directory s moconavi | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a moconavi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e1916224-e1c2-426f-b233-0a2518fa41db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76d1621834c5096743f6d37dad94754ec8b259a4
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56196242"
---
# <a name="tutorial-azure-active-directory-integration-with-moconavi"></a>Kurz: Integrace Azure Active Directory s moconavi

V tomto kurzu se dozvíte, jak integrovat moconavi s Azure Active Directory (Azure AD).

Moconavi integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k moconavi.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k moconavi (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s moconavi, potřebujete následující položky:

- Předplatné Azure AD
- Moconavi jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání moconavi z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-moconavi-from-the-gallery"></a>Přidání moconavi z Galerie
Konfigurace integrace moconavi do služby Azure AD, budete muset přidat moconavi z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat moconavi z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **moconavi**vyberte **moconavi** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![moconavi v seznamu výsledků](./media/moconavi-tutorial/tutorial_moconavi_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí moconavi podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v moconavi je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v moconavi potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s moconavi, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele moconavi](#create-a-moconavi-test-user)**  – Pokud chcete mít protějšek Britta Simon v moconavi, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci moconavi.

**Ke konfiguraci Azure AD jednotné přihlašování s moconavi, proveďte následující kroky:**

1. Na webu Azure Portal na **moconavi** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/moconavi-tutorial/tutorial_moconavi_samlbase.png)

3. Na **moconavi domény a adresy URL** části, proveďte následující kroky:

    ![moconavi domény a adresy URL jednotné přihlašování – informace](./media/moconavi-tutorial/tutorial_moconavi_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<yourserverurl>/moconavi-saml2/saml/login`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<yourserverurl>/moconavi-saml2`

    C. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<yourserverurl>/moconavi-saml2/saml/SSO`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL, identifikátoru a adresa URL odpovědi. Kontakt [tým podpory klienta moconavi](mailto:support@recomot.co.jp) k získání těchto hodnot.

4. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/moconavi-tutorial/tutorial_moconavi_certificate.png)

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/moconavi-tutorial/tutorial_general_400.png)

6. Ke konfiguraci jednotného přihlašování na **moconavi** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [tým podpory moconavi](mailto:support@recomot.co.jp). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/moconavi-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/moconavi-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/moconavi-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/moconavi-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-moconavi-test-user"></a>Vytvoření zkušebního uživatele moconavi

V této části vytvoříte uživatele v moconavi jako Britta Simon. Práce s [tým podpory moconavi](mailto:support@recomot.co.jp) přidat uživatele na platformě moconavi. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k moconavi.

![Přiřazení role uživatele][200]

**Přiřadit moconavi Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **moconavi**.

    ![Odkaz moconavi v seznamu aplikací](./media/moconavi-tutorial/tutorial_moconavi_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

1. Nainstalujte moconavi z Microsoft storu.

2. Začněte moconavi.

3. Klikněte na tlačítko **připojení nastavení** tlačítko.

    ![Testování jednotného přihlašování](./media/moconavi-tutorial/testing1.png)

4. Zadejte `https://mcs-admin.moconavi.biz/gateway` do **připojit k adrese URL** textového pole a potom klikněte na tlačítko **provádí** tlačítko.

    ![Testování jednotného přihlašování](./media/moconavi-tutorial/testing2.png)

5. Na následujícím snímku obrazovky proveďte následující kroky:

    ![Testování jednotného přihlašování](./media/moconavi-tutorial/testing3.png)

    a. Zadejte **vstup ověřovací klíč**:`azureAD` do **vstup ověřovací klíč** textového pole.

    b. Zadejte **ID vstupu uživatele**: `your ad account` do **ID vstupu uživatele** textového pole.

    c. Klikněte na tlačítko **přihlášení**.

6. Zadání hesla Azure AD **heslo** textového pole a potom klikněte na tlačítko **přihlášení** tlačítko.

    ![Testování jednotného přihlašování](./media/moconavi-tutorial/testing4.png)

7. Ověřování Azure AD je úspěšný, když se zobrazí v nabídce.

    ![Testování jednotného přihlašování](./media/moconavi-tutorial/testing5.png)

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

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

