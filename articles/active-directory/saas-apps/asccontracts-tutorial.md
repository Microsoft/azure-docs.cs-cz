---
title: 'Kurz: Integrace Azure Active Directory s kontrakty ASC | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a kontrakty ASC.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: f7f54202-1581-4e55-a97e-02633ff9382d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: jeedes
ms.openlocfilehash: 5737bd76e7809035f21103f45a20fa8306061f30
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54823296"
---
# <a name="tutorial-azure-active-directory-integration-with-asc-contracts"></a>Kurz: Integrace Azure Active Directory s kontrakty ASC

V tomto kurzu se dozvíte, jak integrovat kontrakty ASC se službou Azure Active Directory (Azure AD).

Kontrakty ASC integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k ASC smlouvy
- Uživatele, aby automaticky získat přihlášení k ASC kontrakty (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s kontrakty ASC, potřebujete následující položky:

- Předplatné Azure AD
- ASC kontrakty jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání smluv ASC z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-asc-contracts-from-the-gallery"></a>Přidání smluv ASC z Galerie
Ke konfiguraci integrace ASC smluv do služby Azure AD, budete muset přidat smlouvy ASC z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidat smlouvy ASC z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **ASC kontrakty**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/asccontracts-tutorial/tutorial_asccontracts_search.png)

1. Na panelu výsledků vyberte **ASC kontrakty**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/asccontracts-tutorial/tutorial_asccontracts_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotného přihlašování u kontraktů ASC podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v kontraktech ASC je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v kontraktech ASC.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v kontraktech ASC.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování u kontraktů ASC, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření testovacího uživatele ASC kontrakty](#creating-an-asc-contracts-test-user)**  – Pokud chcete mít protějšek Britta Simon v ASC kontrakty, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci kontrakty ASC.

**Ke konfiguraci Azure AD jednotného přihlašování u kontraktů ASC, proveďte následující kroky:**

1. Na webu Azure Portal na **ASC kontrakty** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/asccontracts-tutorial/tutorial_asccontracts_samlbase.png)

1. Na **ASC kontrakty domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/asccontracts-tutorial/tutorial_asccontracts_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.asccontracts.com/shibboleth`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.asccontracts.com/shibboleth.sso/login`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Contact ASC Networks Inc. (ASC) tým na adrese **613.599.6178** k získání těchto hodnot.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/asccontracts-tutorial/tutorial_asccontracts_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/asccontracts-tutorial/tutorial_general_400.png)

1. Ke konfiguraci jednotného přihlašování na **ASC kontrakty** straně, zavolejte ASC Networks, Inc. Podpora (ASC) na **613.599.6178** a umožnit jim na stažený **soubor XML s metadaty**. Nastavené této aplikace SAML SSO připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/asccontracts-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/asccontracts-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/asccontracts-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/asccontracts-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-asc-contracts-test-user"></a>Vytvoření ASC kontrakty testovacího uživatele

Práce s ASC Networks, Inc. Tým podpory (ASC) na **613.599.6178** zobrazíte uživatelů přidaných na platformě kontrakty ASC.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k ASC smluv.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit k ASC kontrakty Britta Simon, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **ASC kontrakty**.

    ![Konfigurace jednotného přihlašování](./media/asccontracts-tutorial/tutorial_asccontracts_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici smlouvy ASC na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci kontrakty ASC. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/asccontracts-tutorial/tutorial_general_01.png
[2]: ./media/asccontracts-tutorial/tutorial_general_02.png
[3]: ./media/asccontracts-tutorial/tutorial_general_03.png
[4]: ./media/asccontracts-tutorial/tutorial_general_04.png

[100]: ./media/asccontracts-tutorial/tutorial_general_100.png

[200]: ./media/asccontracts-tutorial/tutorial_general_200.png
[201]: ./media/asccontracts-tutorial/tutorial_general_201.png
[202]: ./media/asccontracts-tutorial/tutorial_general_202.png
[203]: ./media/asccontracts-tutorial/tutorial_general_203.png
