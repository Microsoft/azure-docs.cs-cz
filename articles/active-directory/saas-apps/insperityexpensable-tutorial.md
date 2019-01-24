---
title: 'Kurz: Integrace Azure Active Directory s Insperity ExpensAble | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Insperity ExpensAble.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c579c453-580e-417d-8a5e-9b6b352795c0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: jeedes
ms.openlocfilehash: b078f459faf4da468a0b0e4ec7c6e880627e4a4d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54821174"
---
# <a name="tutorial-azure-active-directory-integration-with-insperity-expensable"></a>Kurz: Integrace Azure Active Directory s Insperity ExpensAble

V tomto kurzu se dozvíte, jak integrovat Insperity ExpensAble s Azure Active Directory (Azure AD).

Integrace s Azure AD Insperity ExpensAble poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Insperity ExpensAble
- Uživatele, aby automaticky získat přihlášení k Insperity ExpensAble (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Insperity ExpensAble, potřebujete následující položky:

- Předplatné Azure AD
- Insperity ExpensAble jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Insperity ExpensAble z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-insperity-expensable-from-the-gallery"></a>Přidání Insperity ExpensAble z Galerie
Pokud chcete nakonfigurovat integraci Insperity ExpensAble do služby Azure AD, budete muset přidat Insperity ExpensAble z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Insperity ExpensAble z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Insperity ExpensAble**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/insperityexpensable-tutorial/tutorial_insperityexpensable_search.png)

1. Na panelu výsledků vyberte **Insperity ExpensAble**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/insperityexpensable-tutorial/tutorial_insperityexpensable_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Insperity ExpensAble podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Insperity ExpensAble je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Insperity ExpensAble potřeba navázat.

V Insperity ExpensAble přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Insperity ExpensAble, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytváření Insperity ExpensAble testovacího uživatele](#creating-an-insperity-expensable-test-user)**  – Pokud chcete mít protějšek Britta Simon Insperity ExpensAble, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Insperity ExpensAble.

**Ke konfiguraci Azure AD jednotné přihlašování s Insperity ExpensAble, proveďte následující kroky:**

1. Na webu Azure Portal na **Insperity ExpensAble** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/insperityexpensable-tutorial/tutorial_insperityexpensable_samlbase.png)

1. Na **Insperity ExpensAble domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/insperityexpensable-tutorial/tutorial_insperityexpensable_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://server.expensable.com/esapp/Authenticate?companyId=<company ID>`

    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory ExpensAble klienta Insperity](http://expensable.com/support/support-overview) tuto výhodu získáte. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/insperityexpensable-tutorial/tutorial_insperityexpensable_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/insperityexpensable-tutorial/tutorial_general_400.png)

1. Na **ExpensAble konfigurace Insperity** klikněte na tlačítko **nakonfigurovat Insperity ExpensAble** otevřete **nakonfigurovat přihlašování** okno. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/insperityexpensable-tutorial/tutorial_insperityexpensable_configure.png) 

1. Ke konfiguraci jednotného přihlašování na **Insperity ExpensAble** straně, je nutné odeslat na stažený **soubor XML s metadaty**, **SAML jednotné přihlašování – adresa URL služby** a  **SAML Entity ID** k [Insperity ExpensAble tým podpory](http://expensable.com/support/support-overview). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/insperityexpensable-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/insperityexpensable-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/insperityexpensable-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/insperityexpensable-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-insperity-expensable-test-user"></a>Vytváření Insperity ExpensAble testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v Insperity ExpensAble. Spojte se prosím s [Insperity ExpensAble tým podpory](http://expensable.com/support/support-overview) přidejte uživatele Insperity ExpensAble účtu.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Insperity ExpensAble.

![Přiřadit uživatele][200] 

**Přiřadit Britta Simon Insperity ExpensAble, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Insperity ExpensAble**.

    ![Konfigurace jednotného přihlašování](./media/insperityexpensable-tutorial/tutorial_insperityexpensable_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Insperity ExpensAble na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Insperity ExpensAble.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/insperityexpensable-tutorial/tutorial_general_01.png
[2]: ./media/insperityexpensable-tutorial/tutorial_general_02.png
[3]: ./media/insperityexpensable-tutorial/tutorial_general_03.png
[4]: ./media/insperityexpensable-tutorial/tutorial_general_04.png
[100]: ./media/insperityexpensable-tutorial/tutorial_general_100.png
[200]: ./media/insperityexpensable-tutorial/tutorial_general_200.png
[201]: ./media/insperityexpensable-tutorial/tutorial_general_201.png
[202]: ./media/insperityexpensable-tutorial/tutorial_general_202.png
[203]: ./media/insperityexpensable-tutorial/tutorial_general_203.png

