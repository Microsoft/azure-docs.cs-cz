---
title: 'Kurz: Azure Active Directory integrace s 10 000 ft plány | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a 10 000 ft plány.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b60c955e-8fa3-4872-a897-c4e81fd7beac
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.openlocfilehash: cc6b1036d98aca62360ed8a935d2d1719c7f4069
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36230348"
---
# <a name="tutorial-azure-active-directory-integration-with-10000ft-plans"></a>Kurz: Azure Active Directory integrace s 10 000 ft plány

V tomto kurzu zjistíte integrace 10 000 ft plánů služby Azure Active Directory (Azure AD).

Integrace 10 000 ft plány s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup do 10 000 ft plánů
- Můžete povolit uživatelům, aby automaticky získat přihlášeného do 10 000 ft plánů (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s 10 000 ft plány, potřebujete následující položky:

- Předplatné služby Azure AD
- 10 000 ft plány jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat a jeden měsíc zkušební [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání 10 000 ft plány z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-10000ft-plans-from-the-gallery"></a>Přidání 10 000 ft plány z Galerie
Při konfiguraci integrace 10 000 ft plány do služby Azure AD potřebujete přidat 10 000 ft plány z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat 10 000 ft plány z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **10 000 ft plány**.

    ![Vytváření testovacího uživatele Azure AD](./media/10000ftplans-tutorial/tutorial_10,000ftplans_search.png)

5. Na panelu výsledků vyberte **10 000 ft plány**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/10000ftplans-tutorial/tutorial_10,000ftplans_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s 10 000 ft plány podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v plánech úrovně 10 000 ft je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v 10 000 ft plány musí navázat.

V plánech 10 000 ft přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s 10 000 ft plány, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření 10 000 ft testovací plány uživatel](#creating-a-10000ft-plans-test-user)**  – Pokud chcete mít protějšek Britta Simon v 10 000 ft plány propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci 10 000 ft plány.

**Ke konfiguraci Azure AD jednotné přihlašování s 10 000 ft plány, proveďte následující kroky:**

1. Na portálu Azure na **10 000 ft plány** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/10000ftplans-tutorial/tutorial_10,000ftplans_samlbase.png)

3. Na **10 000 ft plány domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/10000ftplans-tutorial/tutorial_10,000ftplans_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL: `https://app.10000ft.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL: `https://app.10000ft.com/saml/metadata`

    > [!NOTE] 
    > Hodnota **identifikátor** se liší, pokud máte vlastní doménu. Obraťte se na [tým podpory plány 10 000 ft](https://www.10000ft.com/plans/support) získat tuto hodnotu. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Raw)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/10000ftplans-tutorial/tutorial_10,000ftplans_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/10000ftplans-tutorial/tutorial_general_400.png)

6. Na **10 000 ft plány konfigurace** klikněte na tlačítko **10 000 ft plány nakonfigurovat** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/10000ftplans-tutorial/tutorial_10,000ftplans_configure.png) 

7. Konfigurace jednotného přihlašování na **10 000 ft plány** straně, budete muset odeslat stažené **Certificate(Raw), adresa URL Sign-Out, SAML Entity ID a SAML jeden přihlašování adresa URL služby** k [tým podpory plány 10 000 ft](https://www.10000ft.com/plans/support).

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/10000ftplans-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/10000ftplans-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/10000ftplans-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/10000ftplans-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-10000ft-plans-test-user"></a>Vytváření 10 000 ft plány testovací uživatel

Cílem této části je vytvoření uživatele názvem Britta Simon v plánech úrovně odolnosti proti selhání 10 000. 10 000 ft plány podporuje za běhu zřizování, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k 10 000 ft plány, pokud ještě neexistuje. 

> [!NOTE]
> Pokud potřebujete ručně vytvořit uživatele, budete muset kontaktovat [tým podpory plány 10 000 ft](https://www.10000ft.com/plans/support).

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu do 10 000 ft plánů.

![Přiřadit uživatele][200] 

**Přiřadit Britta Simon do 10 000 ft plánů, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **10 000 ft plány**.

    ![Konfigurovat jednotné přihlašování](./media/10000ftplans-tutorial/tutorial_10,000ftplans_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.  
Když kliknete na dlaždici 10 000 ft plány na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci 10 000 ft plány.
 
## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/10000ftplans-tutorial/tutorial_general_01.png
[2]: ./media/10000ftplans-tutorial/tutorial_general_02.png
[3]: ./media/10000ftplans-tutorial/tutorial_general_03.png
[4]: ./media/10000ftplans-tutorial/tutorial_general_04.png

[100]: ./media/10000ftplans-tutorial/tutorial_general_100.png

[200]: ./media/10000ftplans-tutorial/tutorial_general_200.png
[201]: ./media/10000ftplans-tutorial/tutorial_general_201.png
[202]: ./media/10000ftplans-tutorial/tutorial_general_202.png
[203]: ./media/10000ftplans-tutorial/tutorial_general_203.png

