---
title: 'Kurz: Azure Active Directory integrace s Panopto | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Panopto.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 89c88e23-93ce-4970-9baa-1104c4e8fe4a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 6bddeabdf608e5acb7d3bcaa390fa6289b5de3bf
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36226115"
---
# <a name="tutorial-azure-active-directory-integration-with-panopto"></a>Kurz: Azure Active Directory integrace s Panopto

V tomto kurzu zjistěte, jak integrovat Panopto s Azure Active Directory (Azure AD).

Integrace Panopto s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Panopto
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Panopto (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Panopto, potřebujete následující položky:

- Předplatné služby Azure AD
- Panopto jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Panopto z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-panopto-from-the-gallery"></a>Přidání Panopto z Galerie
Při konfiguraci integrace Panopto do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Panopto z galerie.

**Pokud chcete přidat Panopto z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Panopto**.

    ![Vytváření testovacího uživatele Azure AD](./media/panopto-tutorial/tutorial_panopto_search.png)

5. Na panelu výsledků vyberte **Panopto**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/panopto-tutorial/tutorial_panopto_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Panopto podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Panopto je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Panopto musí navázat.

V Panopto, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Panopto, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Panopto](#creating-a-panopto-test-user)**  – Pokud chcete mít protějšek Britta Simon v Panopto propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Panopto.

**Ke konfiguraci Azure AD jednotné přihlašování s Panopto, proveďte následující kroky:**

1. Na portálu Azure na **Panopto** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/panopto-tutorial/tutorial_panopto_samlbase.png)

3. Na **Panopto domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/panopto-tutorial/tutorial_panopto_url.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<tenant-name>.panopto.com`

    > [!NOTE] 
    > Tato hodnota není skutečné. Aktualizujte tuto hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory Panopto klienta](mailto:support@panopto.com‎) získat tuto hodnotu. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/panopto-tutorial/tutorial_panopto_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/panopto-tutorial/tutorial_general_400.png)

6. Na **Panopto konfigurace** klikněte na tlačítko **konfigurace Panopto** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/panopto-tutorial/tutorial_panopto_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti Panopto jako správce.

8. Na panelu nástrojů na levé straně klikněte na tlačítko **systému**a potom klikněte na **zprostředkovatelů Identity**.
   
   ![Systém](./media/panopto-tutorial/ic777670.png "systému")
9. Klikněte na tlačítko **přidejte zprostředkovatele**.
   
   ![Zprostředkovatelé identity](./media/panopto-tutorial/ic777671.png "poskytovatelů identit")
   
10. V části zprostředkovatele SAML proveďte následující kroky:
   
    ![Konfigurace SaaS](./media/panopto-tutorial/ic777672.png "konfigurace SaaS")
    
    a. Z **typ zprostředkovatele** seznamu, vyberte **SAML20**.    
    
    b. V **název Instance** textovému poli, zadejte název pro instanci.

    c. V **stručný popis** textovému poli, zadejte popisný název.
    
    d. V **kolísají adresa Url stránky** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.

    e. V **vystavitele** textovému poli, vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure.

    f. Otevření kódování base-64 kódovaného certifikátu, který jste si stáhli z portálu Azure, obsah ho zkopírujte do schránky a vložte jej do **veřejný klíč** textové pole.

11. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/panopto-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/panopto-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/panopto-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/panopto-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-panopto-test-user"></a>Vytvoření zkušebního uživatele Panopto

Neexistuje žádná položka akce můžete nakonfigurovat na Panopto zřizování uživatelů.  
Když přiřazený uživatel se pokusí přihlásit k Panopto pomocí přístupového panelu, Panopto ověří, zda uživatel existuje.  

Pokud neexistuje žádný účet k dispozici dosud, je vytvářena automaticky nástrojem Panopto.

>[!NOTE]
>Můžete použít všechny ostatní Panopto uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované Panopto ke zřízení uživatelských účtů Azure AD.
>
>

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Panopto.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Panopto, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Panopto**.

    ![Konfigurovat jednotné přihlašování](./media/panopto-tutorial/tutorial_panopto_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Panopto na přístupovém panelu, měli byste obdržet automaticky přihlašovací stránku Panopto aplikace.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/panopto-tutorial/tutorial_general_01.png
[2]: ./media/panopto-tutorial/tutorial_general_02.png
[3]: ./media/panopto-tutorial/tutorial_general_03.png
[4]: ./media/panopto-tutorial/tutorial_general_04.png

[100]: ./media/panopto-tutorial/tutorial_general_100.png

[200]: ./media/panopto-tutorial/tutorial_general_200.png
[201]: ./media/panopto-tutorial/tutorial_general_201.png
[202]: ./media/panopto-tutorial/tutorial_general_202.png
[203]: ./media/panopto-tutorial/tutorial_general_203.png

