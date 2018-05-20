---
title: 'Kurz: Azure Active Directory integrace s konkrétně | Microsoft Docs'
description: Naučte se konfigurovat jednotné přihlašování mezi Azure Active Directory a konkrétně.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9541d5c4-4c82-4b5b-b01a-6a3f75a2b7a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: a7f3fac69143cc4863b65604c6aac314157ba826
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Kurz: Azure Active Directory integrace s konkrétně

V tomto kurzu zjistěte, jak integrovat konkrétně s Azure Active Directory (Azure AD).

Konkrétně integraci s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, kdo má přístup k konkrétně
- Můžete povolit uživatelům získat automaticky přihlášení k konkrétně (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci Azure AD integrace s konkrétně, budete potřebovat následující položky:

- Předplatné služby Azure AD
- Konkrétně jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání konkrétně z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-namely-from-the-gallery"></a>Přidání konkrétně z Galerie
Chcete-li nakonfigurovat integraci konkrétně do služby Azure AD, přidejte konkrétně z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat a to z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **konkrétně**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-namely-tutorial/tutorial_namely_search.png)

5. Na panelu výsledků vyberte **konkrétně**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-namely-tutorial/tutorial_namely_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s, a to podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v konkrétně je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v konkrétně je potřeba vytvořit.

V konkrétně, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s konkrétně, je potřeba provést následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření konkrétně testovací uživatel](#creating-a-namely-test-user)**  – Pokud chcete mít protějšek Britta Simon v konkrétně připojený k Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v konkrétně aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s konkrétně, proveďte následující kroky:**

1. Na portálu Azure na **konkrétně** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-namely-tutorial/tutorial_namely_samlbase.png)

3. Na **konkrétně domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-namely-tutorial/tutorial_namely_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.namely.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.namely.com/saml/metadata`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory konkrétně klienta](https://www.namely.com/contact/) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-namely-tutorial/tutorial_namely_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-namely-tutorial/tutorial_general_400.png)

6. Na **konkrétně konfigurace** klikněte na tlačítko **konfigurace konkrétně** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-namely-tutorial/tutorial_namely_configure.png) 

7. V jiném okně prohlížeče, přihlaste se k vaší konkrétně společnosti lokality jako správce.

8. Na panelu nástrojů v horní části klikněte na tlačítko **společnosti**.
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-namely-tutorial/tutorial_namely_06.png) 

9. Klikněte na kartu **Nastavení**.
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-namely-tutorial/tutorial_namely_07.png) 

10. Klikněte na tlačítko **SAML**.
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-namely-tutorial/tutorial_namely_08.png) 

11. Na **SAML nastavení** proveďte následující kroky:
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-namely-tutorial/tutorial_namely_09.png)
 
    a. Klikněte na tlačítko **povolit SAML**. 

    b. V **adresa url jednotné přihlašování zprostředkovatele Identity** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.
    
    c. V poznámkovém bloku otevřete stažený certifikát, kopírovat obsah a vložte ji do **certifikát zprostředkovatele Identity** textové pole.
     
    d. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-namely-test-user"></a>Vytváření konkrétně testovací uživatel

Cílem této části je vytvoření uživatele volal Britta Simon konkrétně v.

**Vytvoření uživatele volal Britta Simon v konkrétně, proveďte následující kroky:**

1. Přihlášení k vaší konkrétně společnosti lokality jako správce.

2. Na panelu nástrojů v horní části klikněte na tlačítko **osoby**.
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-namely-tutorial/tutorial_namely_10.png) 

3. Klikněte **Directory** kartě.
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-namely-tutorial/tutorial_namely_11.png) 

4. Klikněte na tlačítko **přidat nové osobě**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-namely-tutorial/tutorial_namely_12.png)

5. Na **přidat nové osobě** dialogové okno, proveďte následující kroky:

    a. V **křestní jméno** textovému poli, typ **Britta**.

    b. V **příjmení** textovému poli, typ **Simon**.

    c. V **e-mailu** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    d. Klikněte na **Uložit**.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon chcete použít Azure jednotného přihlašování k udělení přístupu k konkrétně.

![Přiřadit uživatele][200] 

**Přiřazení Britta Simon na konkrétně, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **konkrétně**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-namely-tutorial/tutorial_namely_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je testování konfigurace Azure AD jednotného přihlašování k použití na přístupovém panelu.

Když kliknete dlaždici konkrétně na přístupovém panelu, jste měli získat automaticky přihlášení k konkrétně aplikace

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-namely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-namely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-namely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-namely-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-namely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-namely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-namely-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-namely-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-namely-tutorial/tutorial_general_203.png

