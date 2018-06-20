---
title: 'Kurz: Azure Active Directory integrace s Halogen softwarem | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Halogen softwarem a Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 7699eb4074d85768fa18f4739137ea8ad2b80ced
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215422"
---
# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Kurz: Azure Active Directory integrace s Halogen softwaru

V tomto kurzu zjistěte, jak integrovat Halogen softwaru s Azure Active Directory (Azure AD).

Integrace Halogen softwaru s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k softwaru Halogen
- Můžete povolit uživatelům, aby automaticky získat přihlášení k softwaru Halogen (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Halogen softwaru, potřebujete následující položky:

- Předplatné služby Azure AD
- Softwaru Halogen jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání softwaru Halogen z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-halogen-software-from-the-gallery"></a>Přidání softwaru Halogen z Galerie

Při konfiguraci integrace Halogen softwaru do služby Azure AD, musíte přidat Halogen Software z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Halogen softwaru z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Halogen softwaru**.

    ![Vytváření testovacího uživatele Azure AD](./media/halogen-software-tutorial/tutorial_halogensoftware_search.png)

5. Na panelu výsledků vyberte **Halogen softwaru**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/halogen-software-tutorial/tutorial_halogensoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Halogen Software založený na testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Halogen softwaru je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v softwaru Halogen musí navázat.

V softwaru Halogen přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Halogen softwaru, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Halogen softwaru](#creating-a-halogen-software-test-user)**  – Pokud chcete mít protějšek Britta Simon v Halogen Software, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Halogen softwaru.

**Ke konfiguraci Azure AD jednotné přihlašování s Halogen softwarem, proveďte následující kroky:**

1. Na portálu Azure na **Halogen softwaru** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/halogen-software-tutorial/tutorial_halogensoftware_samlbase.png)

3. Na **Halogen softwaru domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/halogen-software-tutorial/tutorial_halogensoftware_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://global.hgncloud.com/<companyname>`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://global.halogensoftware.com/<companyname>`, `https://global.hgncloud.com/<companyname>`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory klientský Software Halogen](https://support.halogensoftware.com/) k získání těchto hodnot. 
 


4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/halogen-software-tutorial/tutorial_halogensoftware_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/halogen-software-tutorial/tutorial_general_400.png)

6. V okně jiný prohlížeč, přihlašování k vaší **Halogen softwaru** aplikace jako správce.

7. Klikněte **možnosti** kartě. 
   
    ![Co je služba Azure AD Connect][12]

8. V levém navigačním podokně klikněte na tlačítko **konfigurace SAML**. 
   
    ![Co je služba Azure AD Connect][13]

9. Na **konfigurace SAML** proveďte následující kroky: 

    ![Co je služba Azure AD Connect][14]

     a. Jako **jedinečný identifikátor**, vyberte **NameID**.

     b. Jako **mapy jedinečný identifikátor pro**, vyberte **uživatelské jméno**.
  
     c. Chcete-li nahrát soubor stažený metadata, klikněte na tlačítko **Procházet** a vyberte soubor a potom **nahrát soubor**.
 
     d. Chcete-li otestovat konfiguraci, klikněte na tlačítko **spustit Test**. 
    
    >[!NOTE]
    >Je nutné čekat na zprávu "*SAML test je dokončen. Zavřete toto okno*". Zavřete okno otevřené. **Povolit SAML** zaškrtávací políčko je povoleno pouze v případě, test byla dokončena. 
     
     e. Vyberte **povolit SAML**.
    
     f. Klikněte na tlačítko **uložit změny**. 

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/halogen-software-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/halogen-software-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/halogen-software-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/halogen-software-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, název typu jako **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-halogen-software-test-user"></a>Vytvoření zkušebního uživatele Halogen softwaru

Cílem této části je vytvoření uživatele volal Britta Simon v Halogen softwaru.

**Vytvoření uživatele volal Britta Simon v softwaru Halogen, proveďte následující kroky:**

1. Přihlaste se k vaší **Halogen softwaru** aplikace jako správce.

2. Klikněte **uživatele Center** a pak klikněte **vytvořit uživatele**.
   
    ![Co je služba Azure AD Connect][300]  

3. Na **nového uživatele** dialogové okno stránky, proveďte následující kroky:
   
    ![Co je služba Azure AD Connect][301]

    a. V **křestní jméno** textovému poli, zadejte jméno uživatele, jako je **Britta**.
    
    b. V **příjmení** textovému poli, zadejte příjmení uživatele jako **Simon**. 

    c. V **uživatelské jméno** textovému poli, typ **Britta Simon**, uživatelské jméno jako v portálu Azure.

    d. V **heslo** textovému poli, zadejte heslo pro Britta.
    
    e. Klikněte na **Uložit**.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Halogen softwaru.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Halogen softwaru, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Halogen softwaru**.

    ![Konfigurovat jednotné přihlašování](./media/halogen-software-tutorial/tutorial_halogensoftware_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je testování konfigurace Azure AD jednotného přihlašování k použití na přístupovém panelu.

Když kliknete na dlaždici Halogen softwaru na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Halogen softwaru.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/halogen-software-tutorial/tutorial_general_01.png
[2]: ./media/halogen-software-tutorial/tutorial_general_02.png
[3]: ./media/halogen-software-tutorial/tutorial_general_03.png
[4]: ./media/halogen-software-tutorial/tutorial_general_04.png

[12]: ./media/halogen-software-tutorial/tutorial_halogen_12.png

[13]: ./media/halogen-software-tutorial/tutorial_halogen_13.png

[14]: ./media/halogen-software-tutorial/tutorial_halogen_14.png

[100]: ./media/halogen-software-tutorial/tutorial_general_100.png

[200]: ./media/halogen-software-tutorial/tutorial_general_200.png
[201]: ./media/halogen-software-tutorial/tutorial_general_201.png
[202]: ./media/halogen-software-tutorial/tutorial_general_202.png
[203]: ./media/halogen-software-tutorial/tutorial_general_203.png

[300]: ./media/halogen-software-tutorial/tutorial_halogen_300.png

[301]: ./media/halogen-software-tutorial/tutorial_halogen_301.png
