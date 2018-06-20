---
title: 'Kurz: Azure Active Directory integrace s Datahug | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Datahug.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5c0dc1ea-7ff4-4554-b60b-0f2fa9f5abaa
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2017
ms.author: jeedes
ms.openlocfilehash: e8295d7cba023697e803bce2d8a321a943761f2a
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36222902"
---
# <a name="tutorial-azure-active-directory-integration-with-datahug"></a>Kurz: Azure Active Directory integrace s Datahug

V tomto kurzu zjistěte, jak integrovat Datahug s Azure Active Directory (Azure AD).

Integrace Datahug s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Datahug
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Datahug (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, přečtěte si téma. [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Datahug, potřebujete následující položky:

- Předplatné služby Azure AD
- Datahug jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Datahug z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-datahug-from-the-gallery"></a>Přidání Datahug z Galerie
Při konfiguraci integrace Datahug do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Datahug z galerie.

**Pokud chcete přidat Datahug z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Datahug**.

    ![Vytváření testovacího uživatele Azure AD](./media/datahug-tutorial/tutorial_datahug_search.png)

5. Na panelu výsledků vyberte **Datahug**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/datahug-tutorial/tutorial_datahug_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Datahug podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Datahug je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Datahug musí navázat.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Datahug.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Datahug, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Datahug](#creating-a-datahug-test-user)**  – Pokud chcete mít protějšek Britta Simon v Datahug propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Datahug.

**Ke konfiguraci Azure AD jednotné přihlašování s Datahug, proveďte následující kroky:**

1. Na portálu Azure na **Datahug** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/datahug-tutorial/tutorial_datahug_samlbase.png)

3. Na **Datahug domény a adresy URL** část, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Konfigurovat jednotné přihlašování](./media/datahug-tutorial/tutorial_datahug_ur1.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://apps.datahug.com/identity/<uniqueID>`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://apps.datahug.com/identity/<uniqueID>/acs`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL**. Pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Konfigurovat jednotné přihlašování](./media/datahug-tutorial/tutorial_datahug_url2.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL jako: `https://apps.datahug.com/`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou reálné. Tyto hodnoty aktualizujte se skutečným identifikátorem a adresa URL odpovědi. Zde, doporučujeme vám použít jedinečnou hodnotu řetězce v identifikátor a adresa URL odpovědi. Obraťte se na [tým podpory Datahug klienta](http://datahug.com/about/contact-us/) k získání těchto hodnot. 

5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/datahug-tutorial/tutorial_datahug_certificate.png) 

6.  Zkontrolujte **"Zobrazit upřesňující nastavení podpisový certifikát"** a proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/datahug-tutorial/tutorial_datahug_cert.png)

    a. V **podepisování možnost**, vyberte **kontrolního výrazu SAML přihlašovací**.
    
    b. V **podpisového algoritmu**, vyberte **SHA1**.
 
7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/datahug-tutorial/tutorial_general_400.png)
    
8. Na **Datahug konfigurace** klikněte na tlačítko **konfigurace Datahug** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID** a **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/datahug-tutorial/tutorial_datahug_configure.png) 

9. Konfigurace jednotného přihlašování na **Datahug** straně, budete muset odeslat stažené **soubor XML s metadaty**, **SAML Entity ID** a **SAML jeden přihlašování adresa URL služby**  k [Datahug podporu](http://datahug.com/about/contact-us/). Nastavené tuto aplikaci tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/datahug-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/datahug-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/datahug-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/datahug-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-datahug-test-user"></a>Vytvoření zkušebního uživatele Datahug

Pokud chcete povolit uživatelům Azure AD přihlášení k Datahug, musí být zřízená do Datahug.  
Při zřizování Datahug, je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k serveru vaší společnosti Datahug jako správce.

2. Najeďte myší **ozubené kolo** v pravém horním a klikněte na **nastavení**
   
   ![Můžete přidat zaměstnance](./media/datahug-tutorial/1.png)

3. Zvolte **osoby** a klikněte na tlačítko **přidat uživatele** karta

    ![Můžete přidat zaměstnance](./media/datahug-tutorial/2.png)

4. Zadejte e-mailu osoby, kterou chcete vytvořit účet pro a klikněte na tlačítko **přidat**.

    ![Můžete přidat zaměstnance](./media/datahug-tutorial/3.png)

    > [!NOTE] 
    > Můžete odeslat e-mailu registrace uživatele tak, že vyberete **odeslání Uvítacího e-mailu** zaškrtávací políčko.  
    > Při vytváření účtu služby Salesforce Neodesílat Uvítacího e-mailu.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Datahug.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Datahug, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Datahug**.

    ![Konfigurovat jednotné přihlašování](./media/datahug-tutorial/tutorial_datahug_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.
Když kliknete na dlaždici Datahug na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Datahug. Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/datahug-tutorial/tutorial_general_01.png
[2]: ./media/datahug-tutorial/tutorial_general_02.png
[3]: ./media/datahug-tutorial/tutorial_general_03.png
[4]: ./media/datahug-tutorial/tutorial_general_04.png

[100]: ./media/datahug-tutorial/tutorial_general_100.png

[200]: ./media/datahug-tutorial/tutorial_general_200.png
[201]: ./media/datahug-tutorial/tutorial_general_201.png
[202]: ./media/datahug-tutorial/tutorial_general_202.png
[203]: ./media/datahug-tutorial/tutorial_general_203.png

