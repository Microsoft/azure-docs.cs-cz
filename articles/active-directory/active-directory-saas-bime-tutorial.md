---
title: 'Kurz: Azure Active Directory integrace s Bime | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Bime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bdcf0729-c880-4c95-b739-0f6345b17dd8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.openlocfilehash: 53272126310b8c1ba38f5c81518073fded18b6bd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34338111"
---
# <a name="tutorial-azure-active-directory-integration-with-bime"></a>Kurz: Azure Active Directory integrace s Bime

V tomto kurzu zjistěte, jak integrovat Bime s Azure Active Directory (Azure AD).

Integrace Bime s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Bime
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Bime (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Bime, potřebujete následující položky:

- Předplatné služby Azure AD
- Bime jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat a jeden měsíc zkušební: [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Bime z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-bime-from-the-gallery"></a>Přidání Bime z Galerie
Při konfiguraci integrace Bime do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Bime z galerie.

**Pokud chcete přidat Bime z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Bime**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-bime-tutorial/tutorial_bime_search.png)

5. Na panelu výsledků vyberte **Bime**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-bime-tutorial/tutorial_bime_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Bime podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Bime je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Bime musí navázat.

V Bime, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Bime, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Bime](#creating-a-bime-test-user)**  – Pokud chcete mít protějšek Britta Simon v Bime propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Bime.

**Ke konfiguraci Azure AD jednotné přihlašování s Bime, proveďte následující kroky:**

1. Na portálu Azure na **Bime** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-bime-tutorial/tutorial_bime_samlbase.png)

3. Na **Bime domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-bime-tutorial/tutorial_bime_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<tenant-name>.Bimeapp.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<tenant-name>.Bimeapp.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory Bime klienta](https://bime.zendesk.com/hc/categories/202604307-Support-tech-notes-and-tips-) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** část, zkopírujte **kryptografický OTISK** hodnotu z certifikátu.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-bime-tutorial/tutorial_bime_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-bime-tutorial/tutorial_general_400.png)

6. Na **Bime konfigurace** klikněte na tlačítko **konfigurace Bime** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-bime-tutorial/tutorial_bime_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti Bime.

8. Na panelu nástrojů klikněte na tlačítko **správce**a potom **účet**.
   
    ![Správce](./media/active-directory-saas-bime-tutorial/ic775558.png "správce")

9. Na stránce konfigurace účtu proveďte následující kroky:
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-bime-tutorial/ic775559.png "nakonfigurovat jednotné přihlašování")
   
    a. Vyberte **ověřování povolit SAML**.

    b. V **vzdálené adresy URL pro přihlášení** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.

    c.  Vložení **kryptografický otisk** hodnotu z portálu Azure do **otisků prstů certifikátů** textové pole.       
   
    d. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-bime-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-bime-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-bime-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-bime-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-bime-test-user"></a>Vytvoření zkušebního uživatele Bime

Pokud chcete povolit uživatelům Azure AD přihlášení k Bime, musí být zřízená do Bime. V případě Bime zřizování je ruční úloha.

**Pokud chcete konfigurovat, zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **Bime** klienta.

2. Na panelu nástrojů klikněte na tlačítko **správce**a potom **uživatelé**.
   
    ![Správce](./media/active-directory-saas-bime-tutorial/ic775561.png "správce")

3. V **seznamu uživatelů**, klikněte na tlačítko **přidat nové uživatele** ("+").
   
    ![Uživatelé](./media/active-directory-saas-bime-tutorial/ic775562.png "uživatelů")

4. Na **podrobné informace o uživateli** dialogové okno proveďte následující kroky:
   
    ![Podrobné informace o uživateli](./media/active-directory-saas-bime-tutorial/ic775563.png "podrobné informace o uživateli")
   
    a. V **křestní jméno** textovému poli, zadejte jméno uživatele jako **Britta**.

    b. V **příjmení** textovému poli, zadejte příjmení uživatele jako **Simon**.
 
    c. V **e-mailu** textovému poli, zadejte e-mailu uživatele jako **brittasimon@contoso.com**.

    d. Klikněte na **Uložit**.

>[!NOTE]
>Můžete použít všechny ostatní Bime uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované Bime zřídit AAD uživatelské účty.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Bime.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Bime, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Bime**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-bime-tutorial/tutorial_bime_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Bime na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Bime.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bime-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bime-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bime-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bime-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bime-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bime-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bime-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bime-tutorial/tutorial_general_203.png

