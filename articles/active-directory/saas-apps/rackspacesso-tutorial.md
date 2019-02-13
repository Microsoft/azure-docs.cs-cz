---
title: 'Kurz: Integrace Azure Active Directory s jednotným Přihlašováním Rackspace | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jednotné přihlašování Rackspace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 36b398be-2f7e-4ce8-9031-53587299bc4a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24864fd1e21efaf37f846fc95b9dae39ffb19e5f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56186827"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Kurz: Integrace Azure Active Directory s jednotným Přihlašováním Rackspace

V tomto kurzu se dozvíte, jak integrovat Rackspace jednotného přihlašování s Azure Active Directory (Azure AD).

Integrace Rackspace jednotného přihlašování s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Rackspace jednotného přihlašování.
- Uživatele, aby automaticky získat přihlášení k Rackspace jednotné přihlašování (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s jednotným Přihlašováním Rackspace, potřebujete následující položky:

- Předplatné Azure AD
- Jednotné přihlašování Rackspace jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání jednotného přihlašování Rackspace z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-rackspace-sso-from-the-gallery"></a>Přidání jednotného přihlašování Rackspace z Galerie

Konfigurace integrace Rackspace jednotné přihlašování do služby Azure AD, budete muset přidat Rackspace jednotného přihlašování na váš seznam spravovaných aplikací SaaS z galerie.

**Chcete-li přidat Rackspace jednotné přihlašování z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **jednotného přihlašování Rackspace**vyberte **jednotného přihlašování Rackspace** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Jednotné přihlašování Rackspace v seznamu výsledků](./media/rackspacesso-tutorial/tutorial_rackspacesso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a testovací služby Azure AD jednotné přihlašování s jednotným Přihlašováním Rackspace podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Rackspace jednotného přihlašování je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Rackspace jednotného přihlašování je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s jednotným Přihlašováním Rackspace, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele jednotného přihlašování Rackspace](#create-a-rackspace-sso-test-user)**  – Pokud chcete mít protějšek Britta Simon v Rackspace jednotného přihlašování, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Rackspace jednotného přihlašování.

**Ke konfiguraci Azure AD jednotné přihlašování s jednotným Přihlašováním Rackspace, proveďte následující kroky:**

1. Na webu Azure Portal na **jednotného přihlašování Rackspace** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/rackspacesso-tutorial/tutorial_rackspacesso_samlbase.png)

3. Na **Rackspace jednotného přihlašování k doméně a adresy URL** části, proveďte následující kroky:

    ![Rackspace jednotného přihlašování k doméně a adresy URL jednotného přihlašování – informace](./media/rackspacesso-tutorial/tutorial_rackspacesso_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL: `https://login.rackspace.com/federate/`

4. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/rackspacesso-tutorial/tutorial_rackspacesso_certificate.png)

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/rackspacesso-tutorial/tutorial_general_400.png)

6. Ke konfiguraci jednotného přihlašování na **jednotného přihlašování Rackspace** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [tým podpory jednotného přihlašování Rackspace](https://support.rackspace.com/). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/rackspacesso-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/rackspacesso-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/rackspacesso-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/rackspacesso-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-rackspace-sso-test-user"></a>Vytvoření zkušebního uživatele Rackspace jednotného přihlašování

V této části budete pracovat s [tým podpory jednotného přihlašování Rackspace](https://support.rackspace.com/) pro připojení vašeho účtu na platformě Rackspace jednotného přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Rackspace jednotného přihlašování.

![Přiřazení role uživatele][200]

**Pokud chcete přiřadit Britta Simon Rackspace jednotného přihlašování, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **jednotného přihlašování Rackspace**.

    ![Jednotné přihlašování Rackspace odkaz v seznamu aplikací](./media/rackspacesso-tutorial/tutorial_rackspacesso_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Rackspace jednotného přihlašování na přístupovém panelu, které by měl získat automaticky přihlášení k Rackspace jednotného přihlašování aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/rackspacesso-tutorial/tutorial_general_01.png
[2]: ./media/rackspacesso-tutorial/tutorial_general_02.png
[3]: ./media/rackspacesso-tutorial/tutorial_general_03.png
[4]: ./media/rackspacesso-tutorial/tutorial_general_04.png

[100]: ./media/rackspacesso-tutorial/tutorial_general_100.png

[200]: ./media/rackspacesso-tutorial/tutorial_general_200.png
[201]: ./media/rackspacesso-tutorial/tutorial_general_201.png
[202]: ./media/rackspacesso-tutorial/tutorial_general_202.png
[203]: ./media/rackspacesso-tutorial/tutorial_general_203.png
