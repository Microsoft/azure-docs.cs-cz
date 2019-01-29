---
title: 'Kurz: Integrace Azure Active Directory pomocí portálu pro správu cloudu pro Microsoft Azure | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a portálu pro správu cloudu pro Microsoft Azure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 4ea9f47c-25ca-42b0-a878-9e7aa6f34973
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 6aa9bb8214496a5457947eab0b42a01bbe89244a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55153993"
---
# <a name="tutorial-azure-active-directory-integration-with-cloud-management-portal-for-microsoft-azure"></a>Kurz: Integrace Azure Active Directory pomocí portálu pro správu cloudu pro Microsoft Azure

V tomto kurzu se dozvíte, jak na portálu pro správu cloudu pro Microsoft Azure integrovat s Azure Active Directory (Azure AD).

Integrace portálu pro správu cloudu pro Microsoft Azure s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k portálu pro správu cloudu pro Microsoft Azure
- Uživatele, aby automaticky získat přihlášení k portálu pro správu cloudu pro Microsoft Azure (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí portálu pro správu cloudu pro Microsoft Azure, potřebujete následující položky:

- Předplatné Azure AD
- Portál pro správu cloudu pro Microsoft Azure jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání portálu pro správu cloudu pro Microsoft Azure z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-cloud-management-portal-for-microsoft-azure-from-the-gallery"></a>Přidání portálu pro správu cloudu pro Microsoft Azure z Galerie
Konfigurovat integraci portálu pro správu cloudu pro Microsoft Azure do služby Azure AD, budete muset přidat portál pro správu cloudu pro Microsoft Azure z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat portál pro správu cloudu pro Microsoft Azure z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **portálu pro správu cloudu pro Microsoft Azure**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/newsignature-tutorial/tutorial_newsignature_search.png)

1. Na panelu výsledků vyberte **portálu pro správu cloudu pro Microsoft Azure**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/newsignature-tutorial/tutorial_newsignature_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí portálu pro správu cloudu pro Microsoft Azure na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v portálu pro správu cloudu pro Microsoft Azure je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatelů v portálu pro správu cloudu pro Microsoft Azure je potřeba navázat.

V portálu pro správu cloudu pro Microsoft Azure, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí portálu pro správu cloudu pro Microsoft Azure, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření portálu pro správu cloudu pro Microsoft Azure testovacího uživatele](#creating-a-cloud-management-portal-for-microsoft-azure-test-user)**  – Pokud chcete mít protějšek Britta Simon v portálu pro správu cloudu pro Microsoft Azure, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v portálu pro správu cloudu pro aplikaci Microsoft Azure.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí portálu pro správu cloudu pro Microsoft Azure, proveďte následující kroky:**

1. Na webu Azure Portal na **portálu pro správu cloudu pro Microsoft Azure** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/newsignature-tutorial/tutorial_newsignature_samlbase.png)

1. Na **portálu pro správu cloudu Microsoft Azure domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/newsignature-tutorial/tutorial_newsignature_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujících vzorů: 
    
    | |
    |--|
    | `https://portal.newsignature.com/<instancename>` |   
    | `https://portal.igcm.com/<instancename>` |
    
    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujících vzorů: 
    
    | |
    |--|
    | `https://<subdomain>.igcm.com` |
    | `https://<subdomain>.newsignature.com` |

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujících vzorů: 
    
    | |
    |--|
    | `https://<subdomain>.igcm.com/<instancename>` |
    | `https://<subdomain>.newsignature.com` |
    | `https://<subdomain>.newsignature.com/<instancename>` |

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL, identifikátoru a adresa URL odpovědi. Kontakt [portál pro správu cloudu pro tým podpory Microsoft Azure Client](mailto:jczernuszka@newsignature.com) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/newsignature-tutorial/tutorial_newsignature_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/newsignature-tutorial/tutorial_general_400.png)

1. Na **portál pro správu cloudu pro Microsoft Azure Configuration** klikněte na tlačítko **nakonfigurovat portál pro správu Cloud Microsoft Azure** otevřete **nakonfigurovat přihlašování** okno. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/newsignature-tutorial/tutorial_newsignature_configure.png) 

1. Ke konfiguraci jednotného přihlašování na **portálu pro správu cloudu pro Microsoft Azure** straně, je nutné odeslat na stažený **certifikát**, **odhlašování URL**,  **SAML jednotné přihlašování – adresa URL služby** a **SAML Entity ID** k [portál pro správu cloudu pro tým podpory Microsoft Azure](mailto:jczernuszka@newsignature.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/newsignature-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/newsignature-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/newsignature-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/newsignature-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-cloud-management-portal-for-microsoft-azure-test-user"></a>Vytváří se portál pro správu cloudu pro Microsoft Azure testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon portálu pro správu cloudu pro Microsoft Azure. Spojte se prosím s [portál pro správu cloudu pro tým podpory Microsoft Azure](mailto:jczernuszka@newsignature.com) k přidání uživatelů v portálu pro správu cloudu pro účet Microsoft Azure.


### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k portálu pro správu cloudu pro Microsoft Azure.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon na portál pro správu cloudu pro Microsoft Azure, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **portálu pro správu cloudu pro Microsoft Azure**.

    ![Konfigurace jednotného přihlašování](./media/newsignature-tutorial/tutorial_newsignature_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.
Po kliknutí na portálu pro správu cloudu pro Microsoft Azure dlaždici na přístupovém panelu, vám by měl získat automaticky přihlášení k portálu pro správu cloudu pro aplikaci Microsoft Azure.

Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/newsignature-tutorial/tutorial_general_01.png
[2]: ./media/newsignature-tutorial/tutorial_general_02.png
[3]: ./media/newsignature-tutorial/tutorial_general_03.png
[4]: ./media/newsignature-tutorial/tutorial_general_04.png

[100]: ./media/newsignature-tutorial/tutorial_general_100.png

[200]: ./media/newsignature-tutorial/tutorial_general_200.png
[201]: ./media/newsignature-tutorial/tutorial_general_201.png
[202]: ./media/newsignature-tutorial/tutorial_general_202.png
[203]: ./media/newsignature-tutorial/tutorial_general_203.png

