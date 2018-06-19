---
title: 'Kurz: Azure Active Directory integrace s malé vylepšení | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi malé vylepšení a Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 50f384f23d9ebcad10f3d003f44bbaa66a959426
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35912787"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Kurz: Azure Active Directory integrace s malé vylepšení

V tomto kurzu zjistěte, jak integrovat malé vylepšení s Azure Active Directory (Azure AD).

Malé vylepšení integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k vylepšení malé
- Můžete povolit uživatelům, aby automaticky získat přihlášení k malé vylepšení (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s malé vylepšení, potřebujete následující položky:

- Předplatné služby Azure AD
- Malých vylepšení jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat a jeden měsíc zkušební [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání vylepšení malé z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-small-improvements-from-the-gallery"></a>Přidání vylepšení malé z Galerie
Při konfiguraci integrace malé vylepšení do služby Azure AD, potřebujete přidat malé vylepšení z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat malé vylepšení z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **malé vylepšení**.

    ![Vytváření testovacího uživatele Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_search.png)

5. Na panelu výsledků vyberte **malé vylepšení**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s malé vylepšení podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v malých vylepšení je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v malých vylepšení musí navázat.

V malých vylepšení přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s malé vylepšení, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele malé vylepšení](#creating-a-small-improvements-test-user)**  – Pokud chcete mít protějšek Britta Simon v malých vylepšení propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci malé vylepšení.

**Ke konfiguraci Azure AD jednotné přihlašování s malé vylepšení, proveďte následující kroky:**

1. Na portálu Azure na **malé vylepšení** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/smallimprovements-tutorial/tutorial_smallimprovements_samlbase.png)

3. Na **malé vylepšení domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/smallimprovements-tutorial/tutorial_smallimprovements_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.small-improvements.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.small-improvements.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory pro malé vylepšení klienta](mailto:support@small-improvements.com) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/smallimprovements-tutorial/tutorial_smallimprovements_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/smallimprovements-tutorial/tutorial_general_400.png)

6. Na **malé konfigurace vylepšení** klikněte na tlačítko **konfigurace malých vylepšení** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/smallimprovements-tutorial/tutorial_smallimprovements_configure.png) 

7. V jiném okně prohlížeče Přihlaste se k serveru vaší společnosti malé vylepšení jako správce.

8. Na stránce hlavní řídicí panel, klikněte na tlačítko **správy** tlačítko na levé straně.
   
    ![Konfigurovat jednotné přihlašování](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

9. Klikněte **jednotné přihlašování SAML** tlačítko z **integrace** části.
   
    ![Konfigurovat jednotné přihlašování](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

10. Na stránce nastavení jednotného přihlašování proveďte následující kroky:
   
    ![Konfigurovat jednotné přihlašování](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. V **koncový bod HTTP** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.

    b. V poznámkovém bloku otevřete stažený certifikát, kopírovat obsah a vložte ji do **x509 certifikát** textové pole. 

    c. Pokud chcete mít jednotné přihlašování a přihlašovací formulář ověřování možnost k dispozici pro uživatele, zkontrolujte **povolit přístup přes heslo pro přihlášení příliš** možnost.  

    d. Zadejte odpovídající hodnotu pro název tlačítko jednotného přihlášení v **SAML výzva** textové pole.  

    e. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/smallimprovements-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/smallimprovements-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/smallimprovements-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/smallimprovements-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-small-improvements-test-user"></a>Vytvoření zkušebního uživatele malé vylepšení

Povolit uživatelům Azure AD přihlášení k vylepšení malé, musí být zřízená do malých vylepšení. V případě malé vylepšení zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlašování k webu společnosti malé vylepšení jako správce.

2. Na domovské stránce, přejděte do nabídky na levé straně, klikněte na tlačítko **správy**.

3. Klikněte **adresář uživatelského** tlačítko z části Správa uživatelů. 
   
    ![Vytváření testovacího uživatele Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

4. Klikněte na tlačítko **přidat uživatele**.

    ![Vytváření testovacího uživatele Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

5. Na **přidat uživatele** dialogové okno, proveďte následující kroky: 

    ![Vytváření testovacího uživatele Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)
    
    a. Zadejte **křestní jméno** uživatele jako **Britta**.

    b. Zadejte **příjmení** uživatele jako **Simon**.

    c. Zadejte **e-mailu** uživatele jako **brittasimon@contoso.com**. 

    d. Můžete také zadat osobní zprávu v **odeslat e-mailové oznámení** pole. Pokud nechcete, aby k odesílání oznámení, poté zrušte zaškrtnutí tohoto políčka.

    e. Klikněte na tlačítko **vytvořte uživatele**.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu malé vylepšení.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon malé vylepšení, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **malé vylepšení**.

    ![Konfigurovat jednotné přihlašování](./media/smallimprovements-tutorial/tutorial_smallimprovements_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je testování konfigurace Azure AD jednotného přihlašování k použití na přístupovém panelu.  

Když kliknete na dlaždici malé vylepšení na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci malé vylepšení.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/smallimprovements-tutorial/tutorial_general_01.png
[2]: ./media/smallimprovements-tutorial/tutorial_general_02.png
[3]: ./media/smallimprovements-tutorial/tutorial_general_03.png
[4]: ./media/smallimprovements-tutorial/tutorial_general_04.png

[100]: ./media/smallimprovements-tutorial/tutorial_general_100.png

[200]: ./media/smallimprovements-tutorial/tutorial_general_200.png
[201]: ./media/smallimprovements-tutorial/tutorial_general_201.png
[202]: ./media/smallimprovements-tutorial/tutorial_general_202.png
[203]: ./media/smallimprovements-tutorial/tutorial_general_203.png

