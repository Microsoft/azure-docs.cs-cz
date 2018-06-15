---
title: 'Kurz: Azure Active Directory integrace s Samanage | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Samanage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: e96cbbadb3d36cdc022052acb5a9a42da35db263
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352663"
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Kurz: Azure Active Directory integrace s Samanage

V tomto kurzu zjistěte, jak integrovat Samanage s Azure Active Directory (Azure AD).

Integrace Samanage s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Samanage
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Samanage (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Samanage, potřebujete následující položky:

- Předplatné služby Azure AD
- Samanage jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Samanage z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-samanage-from-the-gallery"></a>Přidání Samanage z Galerie
Při konfiguraci integrace Samanage do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Samanage z galerie.

**Pokud chcete přidat Samanage z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Samanage**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_search.png)

5. Na panelu výsledků vyberte **Samanage**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Samanage podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Samanage je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Samanage musí navázat.

V Samanage, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Samanage, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Samanage](#creating-a-samanage-test-user)**  – Pokud chcete mít protějšek Britta Simon v Samanage propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Samanage.

**Ke konfiguraci Azure AD jednotné přihlašování s Samanage, proveďte následující kroky:**

1. Na portálu Azure na **Samanage** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_samlbase.png)

3. Na **Samanage domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor, který je vysvětlen později v tomto kurzu. Obraťte se na podrobnosti [tým podpory Samanage klienta](https://www.samanage.com/support).    
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-samanage-tutorial/tutorial_general_400.png)

6. Na **Samanage konfigurace** klikněte na tlačítko **konfigurace Samanage** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresy URL a SAML Entity ID** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti Samanage.

8. Klikněte na tlačítko **řídicí panel** a vyberte **instalační program** v levém navigačním podokně.
   
    ![Řídicí panel](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "řídicí panel")

9. Klikněte na tlačítko **jednotného přihlašování**.
   
    ![Jednotné přihlašování](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_002.png "jednotného přihlašování")

10. Přejděte na **přihlášení pomocí SAML** část, proveďte následující kroky:
   
    ![Přihlášení pomocí SAML](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_003.png "přihlášení pomocí SAML")
 
    a. Klikněte na tlačítko **povolit jednotné přihlašování s SAML**.  
 
    b. V **adresa URL poskytovatele Identity** textovému poli, vložte hodnotu **SAML Entity ID** který jste zkopírovali z portálu Azure.    
 
    c. Potvrďte **přihlašovací adresa URL** odpovídá **přihlašovací adresa URL** z **Samanage domény a adresy URL** části na portálu Azure.
 
    d. V **adresy URL odhlašovací** textovému poli, zadejte hodnotu **Sign-Out URL** který jste zkopírovali z portálu Azure.
 
    e. V **SAML vystavitele** textové pole, zadejte id URI aplikace nastavte u poskytovatele identit.
 
    f. Otevření kódovaného certifikátu kódování base-64 stáhli z portálu Azure v programu Poznámkový blok, zkopírujte obsah ho do schránky a vložte jej do **vložit vašeho poskytovatele identit x.509 certifikátu níže** textové pole.
 
    g. Klikněte na tlačítko **vytváření uživatelů, pokud neexistují v Samanage**.
 
    h. Klikněte na tlačítko **aktualizace**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
 
### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-samanage-test-user"></a>Vytvoření zkušebního uživatele Samanage

Pokud chcete povolit uživatelům Azure AD přihlášení k Samanage, musí být zřízená do Samanage.  
V případě Samanage zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k serveru vaší společnosti Samanage jako správce.

2. Klikněte na tlačítko **řídicí panel** a vyberte **instalační program** v levém navigačním podokně.
   
    ![Instalační program](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "instalační program")

3. Klikněte **uživatelé** karta
   
    ![Uživatelé](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_006.png "uživatelů")

4. Klikněte na tlačítko **nového uživatele**.
   
    ![Nový uživatel](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_007.png "nového uživatele")

5. Typ **název** a **e-mailovou adresu** účtu Azure Active Directory, které chcete zřídit a klikněte na tlačítko **vytvořit uživateli**.
   
    ![Vytvoření uživatele](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_008.png "vytvoření uživatele")
   
   >[!NOTE]
   >Držitel účtu Azure Active Directory bude dostávat e-mailu a postupujte podle odkaz potvrďte svůj účet, pak se změní na aktivní. Můžete použít všechny ostatní Samanage uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované Samanage zřídit služby Azure Active Directory uživatelské účty.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Samanage.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Samanage, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Samanage**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Samanage na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Samanage.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_203.png

