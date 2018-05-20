---
title: 'Kurz: Azure Active Directory integrace s plátno pro správu vzdělávacího procesu | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a plátno pro správu vzdělávacího procesu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bfed291c-a33e-410d-b919-5b965a631d45
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: jeedes
ms.openlocfilehash: 56fe4679448fec1db67f826ff70cf8768699e507
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>Kurz: Azure Active Directory integrace s plátno pro správu vzdělávacího procesu

V tomto kurzu zjistěte, jak integrovat plátno s Azure Active Directory (Azure AD).

Integrace plátno s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup na plátno
- Můžete povolit uživatelům, aby automaticky získat přihlášení k plátno (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s plátno, potřebujete následující položky:

- Předplatné služby Azure AD
- Plátně jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání plátno z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-canvas-from-the-gallery"></a>Přidání plátno z Galerie
Při konfiguraci integrace plátno do služby Azure AD potřebujete přidat plátno z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat plátno z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **plátno**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_search.png)

5. Na panelu výsledků vyberte **plátno**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování plátno podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v plátno je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské plátno musí navázat.

V plátně přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování plátno, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele plátno](#creating-a-canvas-test-user)**  – Pokud chcete mít protějšek Britta Simon plátno propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci plátno.

**Ke konfiguraci Azure AD jednotné přihlašování plátno, proveďte následující kroky:**

1. Na portálu Azure na **plátno** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_samlbase.png)

3. Na **plátno domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<tenant-name>.instructure.com`

    b. V **identifikátor** textovému poli, zadejte hodnotu pomocí následujícího vzorce: `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory plátno klienta](https://community.canvaslms.com/community/help) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** část, zkopírujte **kryptografický OTISK** hodnota certifikátu.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_400.png)

6. Na **plátno konfigurace** klikněte na tlačítko **konfigurace plátno** otevřete **konfigurovat přihlášení** okno. Kopírování **heslo změnit adresu URL, adresa URL Sign-Out, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_configure.png) 
 
7. V okně prohlížeče jiný web Přihlaste se na váš web společnosti plátno jako správce.

8. Přejděte na **kurzy \> účty spravované \> Microsoft**.
   
    ![Plátno](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "plátno")

9. V navigačním podokně na levé straně vyberte **ověřování**a potom klikněte na **přidat nová konfigurace SAML**.
   
    ![Ověřování](./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "ověřování")

10. Na stránce aktuální integrace proveďte následující kroky:
   
    ![Aktuální integrace](./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "aktuální integrace")

    a. V **IdP Entity ID** textovému poli, vložte hodnotu **SAML Entity ID** který jste zkopírovali z portálu Azure.

    b. V **protokolu na adresu URL** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.

    c. V **adresy URL odhlašovací** textovému poli, vložte hodnotu **Sign-Out URL** který jste zkopírovali z portálu Azure.

    d. V **odkaz změnit heslo** textovému poli, vložte hodnotu **heslo změnit adresu URL** který jste zkopírovali z portálu Azure. 

    e. V **otisků prstů certifikátů** textovému poli, Vložit **kryptografický otisk** hodnota certifikát, který jste zkopírovali z portálu Azure.      
        
    f. Z **přihlášení atribut** seznamu, vyberte **NameID**.

    g. Z **identifikátor formátu** seznamu, vyberte **emailAddress**.

    h. Klikněte na tlačítko **uložit nastavení ověřování**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-canvas-lms-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-canvas-lms-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-canvas-lms-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-canvas-lms-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-canvas-test-user"></a>Vytvoření zkušebního uživatele plátno

Pokud chcete povolit uživatelům Azure AD přihlášení na plátno, musí být zřízená plátno.

V případě plátně zřizování uživatelů je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k vaší **plátno** klienta.

2. Přejděte na **kurzy \> účty spravované \> Microsoft**.
   
   ![Plátno](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "plátno")

3. Klikněte na tlačítko **uživatelé**.
   
   ![Uživatelé](./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "uživatelů")

4. Klikněte na tlačítko **přidat nového uživatele**.
   
   ![Uživatelé](./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "uživatelů")

5. Na stránce Přidat nového uživatele dialogové okno proveďte následující kroky:
   
   ![Přidat uživatele](./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "přidat uživatele")
   
   a. V **úplný název** textovému poli, zadejte jméno uživatele, jako je **BrittaSimon**.

   b. V **e-mailu** textovému poli, zadejte e-mailu uživatele jako **brittasimon@contoso.com**.

   c. V **přihlášení** textovému poli, zadejte uživatele Azure AD e-mailovou adresu jako **brittasimon@contoso.com**.

   d. Vyberte **e-mailu uživatele o vytvoření tohoto účtu**.

   e. Klikněte na tlačítko **přidat uživatele**.

>[!NOTE]
>Můžete použít všechny ostatní plátno uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované plátno zřídit AAD uživatelské účty.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu na plátno.

![Přiřadit uživatele][200] 

**Přiřadit Britta Simon na plátno, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **plátno**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici plátno na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci plátno.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_203.png

