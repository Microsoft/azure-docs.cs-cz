---
title: 'Kurz: Azure Active Directory integrace s Aha! | Dokumenty Microsoft'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Aha!.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ad955d3d-896a-41bb-800d-68e8cb5ff48d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: d143b04e424336bda8b38f4458448230277ac837
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34347835"
---
# <a name="tutorial-azure-active-directory-integration-with-aha"></a>Kurz: Azure Active Directory integrace s Aha!

V tomto kurzu zjistíte, jak integrovat Aha! s Azure Active Directory (Azure AD).

Integrace Aha! s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Aha!
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Aha! (Jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Aha!, potřebujete následující položky:

- Předplatné služby Azure AD
- Aha! jednotné přihlašování v předplatném povolené

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Aha! z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-aha-from-the-gallery"></a>Přidání Aha! z Galerie
Konfigurace integrace Aha! do služby Azure AD je nutné přidat Aha! z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat Aha! z Galerie proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Aha!**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-aha-tutorial/tutorial_aha_search.png)

5. Na panelu výsledků vyberte **Aha!** a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-aha-tutorial/tutorial_aha_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Aha! na základě testovací uživatele, nazývá "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, jaké příslušného uživatele v Aha! je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Aha! musí být vytvořeno.

V Aha!, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Aha!, je potřeba provést následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření Aha! testovací uživatel](#creating-an-aha-test-user)**  – Pokud chcete mít protějšek Britta Simon v Aha! připojený k Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v vaší Aha! Aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s Aha!, proveďte následující kroky:**

1. Na portálu Azure na **Aha!** Stránka integrace aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-aha-tutorial/tutorial_aha_samlbase.png)

3. Na **Aha! Domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-aha-tutorial/tutorial_aha_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.aha.io/session/new`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.aha.io`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [Aha! Tým podpory klienta](https://www.aha.io/company/contact) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-aha-tutorial/tutorial_aha_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-aha-tutorial/tutorial_general_400.png)

6. V okně prohlížeče jiný web Přihlaste se k vaší Aha! web společnosti jako správce.

7. V nabídce v horní části, klikněte na tlačítko **nastavení**.

    ![Nastavení](./media/active-directory-saas-aha-tutorial/IC798950.png "nastavení")

8. Klikněte na tlačítko **účet**.
   
    ![Profil](./media/active-directory-saas-aha-tutorial/IC798951.png "profilu")

9. Klikněte na tlačítko **zabezpečení a jednotné přihlašování**.
   
    ![Zabezpečení a jednotné přihlašování](./media/active-directory-saas-aha-tutorial/IC798952.png "zabezpečení a jednotné přihlašování")

10. V **jednotné přihlašování** části jako **zprostředkovatele Identity**, vyberte **SAML2.0**.
   
    ![Zabezpečení a jednotné přihlašování](./media/active-directory-saas-aha-tutorial/IC798953.png "zabezpečení a jednotné přihlašování")

11. Na **jednotné přihlašování** konfigurace proveďte následující kroky:
    
    ![Jednotné přihlašování](./media/active-directory-saas-aha-tutorial/IC798954.png "jednotného přihlašování")
    
       a. V **název** textovému poli, zadejte název pro svou konfiguraci.

       b. Pro **nakonfigurovat**, vyberte **soubor metadat**.
   
       c. Chcete-li nahrát soubor stažený metadata, klikněte na tlačítko **Procházet**.
   
       d. Klikněte na tlačítko **aktualizace**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-aha-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-aha-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-aha-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-aha-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-aha-test-user"></a>Vytvoření Aha! testovací uživatel

Povolit uživatelům Azure AD přihlášení do Aha!, musí být zřízená do Aha!.  

V případě Aha!, zřizování je automatizaci úloh. Neexistuje žádná položka akce za vás.

Uživatelé jsou automaticky vytvářeny v případě potřeby při prvním jeden přihlašování pokusu.

>[!NOTE]
>Můžete použít jiné Aha! Nástroje pro tvorbu účet uživatele nebo rozhraní API poskytovaných Aha! Chcete-li zřídit AAD uživatelské účty.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Aha!.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit Aha!, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Aha!**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-aha-tutorial/tutorial_aha_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Pokud chcete testovat vaše nastavení jednotného přihlašování, otevřete Panel přístupu. Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-aha-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-aha-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-aha-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-aha-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-aha-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-aha-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-aha-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-aha-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-aha-tutorial/tutorial_general_203.png

