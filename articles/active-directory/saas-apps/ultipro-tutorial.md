---
title: 'Kurz: Azure Active Directory integrace s UltiPro | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a UltiPro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: afc0f2b9-2eac-47ec-af04-65ed0fb0ca5a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: 3641eb3b38782772fb8641b1ddb5bb1230b2ec48
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36217117"
---
# <a name="tutorial-azure-active-directory-integration-with-ultipro"></a>Kurz: Azure Active Directory integrace s UltiPro

V tomto kurzu zjistěte, jak integrovat UltiPro s Azure Active Directory (Azure AD).

Integrace UltiPro s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k UltiPro
- Můžete povolit uživatelům, aby automaticky získat přihlášení k UltiPro (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s UltiPro, potřebujete následující položky:

- Předplatné služby Azure AD
- UltiPro jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání UltiPro z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-ultipro-from-the-gallery"></a>Přidání UltiPro z Galerie
Při konfiguraci integrace UltiPro do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS UltiPro z galerie.

**Pokud chcete přidat UltiPro z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **UltiPro**.

    ![Vytváření testovacího uživatele Azure AD](./media/ultipro-tutorial/tutorial_ultipro_search.png)

5. Na panelu výsledků vyberte **UltiPro**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/ultipro-tutorial/tutorial_ultipro_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s UltiPro podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v UltiPro je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v UltiPro musí navázat.

V UltiPro, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s UltiPro, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele UltiPro](#creating-a-ultipro-test-user)**  – Pokud chcete mít protějšek Britta Simon v UltiPro propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci UltiPro.

**Ke konfiguraci Azure AD jednotné přihlašování s UltiPro, proveďte následující kroky:**

1. Na portálu Azure na **UltiPro** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/ultipro-tutorial/tutorial_ultipro_samlbase.png)

3. Na **UltiPro domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/ultipro-tutorial/tutorial_ultipro_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | |
    |--|
    | `https://<companyname>.ultipro.com/`|
    | `https://<companyname>.ultiproworkplace.com?cpi=AZUREADISSSUERURL`|
    | ` https://<companyname>.ultipro.ca`|
    
    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | |
    |--|
    | `https://<companyname>.ultipro.com/adfs/services/trust`|
    | `https://<companyname>.ultiproworkplace.com/adfs/services/trust`|
    | `https://<companyname>.ultipro.ca/adfs/services/trust`|
    
    c. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | |
    |--|
    | `https://<companyname>.ultipro.com/<instancename>`|
    | `https://<companyname>.ultiproworkplace.com/<instancename>`|
    | `https://<companyname>.ultipro.ca/<instancename>`|
    
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [tým podpory UltiPro klienta](https://www.ultimatesoftware.com/ContactUs) k získání těchto hodnot. 

5. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/ultipro-tutorial/tutorial_ultipro_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/ultipro-tutorial/tutorial_general_400.png)
    
7. Na **UltiPro konfigurace** klikněte na tlačítko **konfigurace UltiPro** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/ultipro-tutorial/tutorial_ultipro_configure.png) 

8. Konfigurace jednotného přihlašování na **UltiPro** straně, budete muset odeslat stažené **Certificate(Base64), adresa URL Sign-Out, SAML Entity ID a SAML jeden přihlašování adresa URL služby** k [UltiPro podpory tým](https://www.ultimatesoftware.com/ContactUs).

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/ultipro-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/ultipro-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/ultipro-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/ultipro-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-ultipro-test-user"></a>Vytvoření zkušebního uživatele UltiPro

V této části vytvoříte volal Britta Simon v UltiPro uživatele. Práce s [tým podpory UltiPro](https://www.ultimatesoftware.com/ContactUs) přidat uživatele do UltiPro platformy. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu UltiPro.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon UltiPro, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **UltiPro**.

    ![Konfigurovat jednotné přihlašování](./media/ultipro-tutorial/tutorial_ultipro_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.  
Když kliknete na dlaždici UltiPro na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci UltiPro.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/ultipro-tutorial/tutorial_general_01.png
[2]: ./media/ultipro-tutorial/tutorial_general_02.png
[3]: ./media/ultipro-tutorial/tutorial_general_03.png
[4]: ./media/ultipro-tutorial/tutorial_general_04.png

[100]: ./media/ultipro-tutorial/tutorial_general_100.png

[200]: ./media/ultipro-tutorial/tutorial_general_200.png
[201]: ./media/ultipro-tutorial/tutorial_general_201.png
[202]: ./media/ultipro-tutorial/tutorial_general_202.png
[203]: ./media/ultipro-tutorial/tutorial_general_203.png

