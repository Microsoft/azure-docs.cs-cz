---
title: 'Kurz: Integrace Azure Active Directory s Aha! | Dokumenty Microsoft'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Aha!.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: ad955d3d-896a-41bb-800d-68e8cb5ff48d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: d46467947116da6f6e5439a54e0315f216961819
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813215"
---
# <a name="tutorial-azure-active-directory-integration-with-aha"></a>Kurz: Integrace Azure Active Directory s Aha!

V tomto kurzu se dozvíte, jak integrovat Aha! s Azure Active Directory (Azure AD).

Integrace Aha! s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Aha!
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Aha! (Jednotné přihlašování) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Aha!, potřebujete následující položky:

- Předplatné Azure AD
- Aha! jednotné přihlašování v předplatném povolené

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Aha! z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-aha-from-the-gallery"></a>Přidání Aha! z Galerie
Konfigurace integrace Aha! do služby Azure AD budete muset přidat Aha! z Galerie tak, aby váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Aha! v galerii proveďte následující kroky:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Aha!**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/aha-tutorial/tutorial_aha_search.png)

5. Na panelu výsledků vyberte **Aha!** a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/aha-tutorial/tutorial_aha_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Aha! na základě testovací uživatele nazývá "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, potřebuje vědět, co protějšek uživatel v Aha! je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele služby Azure AD a související uživatelské v Aha! je potřeba navázat.

V Aha!, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Aha!, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření Aha! testovací uživatele](#creating-an-aha-test-user)**  – Pokud chcete mít protějšek Britta Simon Aha! připojený k Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování ve vašich Aha! aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s Aha!, proveďte následující kroky:**

1. Na webu Azure Portal na **Aha!** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/aha-tutorial/tutorial_aha_samlbase.png)

3. Na **Aha! Domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/aha-tutorial/tutorial_aha_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.aha.io/session/new`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.aha.io`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [Aha! Tým podpory klienta](https://www.aha.io/company/contact) k získání těchto hodnot. 
 
4. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/aha-tutorial/tutorial_aha_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/aha-tutorial/tutorial_general_400.png)

6. V okně jiné webové prohlížeče Přihlaste se k vaší Aha! web společnosti jako správce.

7. V nabídce v horní části klikněte na tlačítko **nastavení**.

    ![Nastavení](./media/aha-tutorial/IC798950.png "nastavení")

8. Klikněte na tlačítko **účet**.
   
    ![Profil](./media/aha-tutorial/IC798951.png "profilu")

9. Klikněte na tlačítko **zabezpečení a jednotné přihlašování**.
   
    ![Zabezpečení a jednotné přihlašování](./media/aha-tutorial/IC798952.png "zabezpečení a jednotné přihlašování")

10. V **Single Sign-On** části jako **zprostředkovatele Identity**vyberte **SAML2.0**.
   
    ![Zabezpečení a jednotné přihlašování](./media/aha-tutorial/IC798953.png "zabezpečení a jednotné přihlašování")

11. Na **Single Sign-On** konfigurační stránce, proveďte následující kroky:
    
    ![Jednotné přihlašování](./media/aha-tutorial/IC798954.png "jednotného přihlašování")
    
       a. V **název** textového pole zadejte název pro vaši konfiguraci.

       b. Pro **konfigurace pomocí**vyberte **soubor metadat**.
   
       c. Nahrát soubor stažený metadat, klikněte na tlačítko **Procházet**.
   
       d. Klikněte na tlačítko **aktualizace**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/aha-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/aha-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/aha-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/aha-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-aha-test-user"></a>Vytvoření Aha! testovací uživatel

Azure AD uživatelům se přihlásit k Aha!, musí být poskytnuty do Aha!.  

V případě Aha!, zřizování se automatická úloha. Neexistuje žádná položka akce za vás.

Uživatelé se automaticky vytvoří v případě potřeby během první jednotné přihlašování pokus.

>[!NOTE]
>Můžete použít jiné Aha! Nástroje pro tvorbu uživatelského účtu nebo rozhraní API poskytovaných Aha! ke zřízení účtů služby AAD uživatele.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Aha!.

![Přiřadit uživatele][200] 

**Přiřadit Britta Simon Aha!, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Aha!**.

    ![Konfigurace jednotného přihlašování](./media/aha-tutorial/tutorial_aha_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Pokud chcete otestovat jednotné přihlašování – nastavení, otevřete na přístupovém panelu. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/aha-tutorial/tutorial_general_01.png
[2]: ./media/aha-tutorial/tutorial_general_02.png
[3]: ./media/aha-tutorial/tutorial_general_03.png
[4]: ./media/aha-tutorial/tutorial_general_04.png

[100]: ./media/aha-tutorial/tutorial_general_100.png

[200]: ./media/aha-tutorial/tutorial_general_200.png
[201]: ./media/aha-tutorial/tutorial_general_201.png
[202]: ./media/aha-tutorial/tutorial_general_202.png
[203]: ./media/aha-tutorial/tutorial_general_203.png

