---
title: 'Kurz: Integrace Azure Active Directory s 23 Video | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a 23 videa.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 5e73dd1d-3995-4a73-b9cf-1b2318d49cb3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: jeedes
ms.openlocfilehash: 6dc05b5ac26dfc9ef7eebf770d9f5378e8a85657
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54823976"
---
# <a name="tutorial-azure-active-directory-integration-with-23-video"></a>Kurz: Integrace Azure Active Directory s 23 videa

V tomto kurzu se dozvíte, jak integrovat 23 videa pomocí služby Azure Active Directory (Azure AD).

Integrace 23 videa pomocí služby Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k 23 videa
- Můžete povolit uživatelům, aby automaticky získat přihlášení k 23 Video (Single Sign-On) pomocí svých účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s 23 videa, potřebujete následující položky:

- Předplatné Azure AD
- 23 Video jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidávání 23 videa z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-23-video-from-the-gallery"></a>Přidávání 23 videa z Galerie
Konfigurace integrace 23 Video do služby Azure AD, budete muset přidat 23 Video z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat 23 videa z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **23 Video**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/23video-tutorial/tutorial_23video_search.png)

5. Na panelu výsledků vyberte **23 Video**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/23video-tutorial/tutorial_23video_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s 23 videa podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v 23 Video je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v 23 Video musí být vytvořeno.

V 23 Video, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s 23 Video, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření 23 testovacího uživatele Video](#creating-a-23-video-test-user)**  – Pokud chcete mít protějšek Britta Simon v 23 Video, které souvisí s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci 23 videa.

**Ke konfiguraci Azure AD jednotné přihlašování s videem 23, proveďte následující kroky:**

1. Na webu Azure Portal na **23 Video** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/23video-tutorial/tutorial_23video_samlbase.png)

3. Na **23 Video domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/23video-tutorial/tutorial_23video_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.23video.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.23video.com/saml/trust/<uniqueid>`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [23 tým podpory Video klienta](mailto:support@23company.com) k získání těchto hodnot. 
 
4. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/23video-tutorial/tutorial_23video_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/23video-tutorial/tutorial_general_400.png)

6. Na **23 konfigurace Video** klikněte na tlačítko **konfigurace Video 23** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/23video-tutorial/tutorial_23video_configure.png) 

7. Ke konfiguraci jednotného přihlašování na **23 Video** straně, je nutné odeslat na stažený **certifikát (Base64)**, **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby**k [23 tým podpory Video](mailto:support@23company.com). 


> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/23video-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/23video-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/23video-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/23video-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-23-video-test-user"></a>Vytváření 23 Video testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v 23 videa.

**Vytvořte uživatele v 23 Video jako Britta Simon, proveďte následující kroky:**

1. Přihlaste se k webu společnosti 23 Video jako správce.

2. Přejděte na **nastavení**.
 
3. V **uživatelé** klikněte na tlačítko **konfigurovat**.
   
    ![Přiřadit uživatele][400]

4. Klikněte na tlačítko **přidat nového uživatele**. 
   
    ![Přiřadit uživatele][401]

5. V **pozvat další připojení k této lokalitě** části, proveďte následující kroky:
   
    ![Přiřadit uživatele][402]

    a. V **e-mailové adresy** textového pole zadejte Britta Simon e-mailovou adresu ve službě Azure AD.  
 
    b. Klikněte na tlačítko **přidejte uživatele**.   

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup do 23 videa.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon 23 videa, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **23 Video**.

    ![Konfigurace jednotného přihlašování](./media/23video-tutorial/tutorial_23video_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je testování konfigurace jednotného přihlašování k Azure AD pomocí přístupového panelu.

Po kliknutí na 23 dlaždice videa na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci 23 videa. 

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/23video-tutorial/tutorial_general_01.png
[2]: ./media/23video-tutorial/tutorial_general_02.png
[3]: ./media/23video-tutorial/tutorial_general_03.png
[4]: ./media/23video-tutorial/tutorial_general_04.png

[100]: ./media/23video-tutorial/tutorial_general_100.png

[200]: ./media/23video-tutorial/tutorial_general_200.png
[201]: ./media/23video-tutorial/tutorial_general_201.png
[202]: ./media/23video-tutorial/tutorial_general_202.png
[203]: ./media/23video-tutorial/tutorial_general_203.png

[400]: ./media/23video-tutorial/tutorial_23video_10.png
[401]: ./media/23video-tutorial/tutorial_23video_11.png
[402]: ./media/23video-tutorial/tutorial_23video_12.png
