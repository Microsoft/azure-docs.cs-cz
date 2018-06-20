---
title: 'Kurz: Azure Active Directory integrace s Printix | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Printix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4aea7320-b2d5-49e0-9b63-aeaff0f6fe66
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: ba207649515cde49ad8d3df7cc5cafe1e5c14501
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225316"
---
# <a name="tutorial-azure-active-directory-integration-with-printix"></a>Kurz: Azure Active Directory integrace s Printix

V tomto kurzu zjistěte, jak integrovat Printix s Azure Active Directory (Azure AD).

Integrace Printix s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Printix
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Printix (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Printix, potřebujete následující položky:

- Předplatné služby Azure AD
- Printix jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Printix z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-printix-from-the-gallery"></a>Přidání Printix z Galerie
Při konfiguraci integrace Printix do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Printix z galerie.

**Pokud chcete přidat Printix z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Printix**.

    ![Vytváření testovacího uživatele Azure AD](./media/printix-tutorial/tutorial_printix_search.png)

5. Na panelu výsledků vyberte **Printix**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/printix-tutorial/tutorial_printix_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Printix podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Printix je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Printix musí navázat.

V Printix, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Printix, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Printix](#creating-a-printix-test-user)**  – Pokud chcete mít protějšek Britta Simon v Printix propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Printix.

**Ke konfiguraci Azure AD jednotné přihlašování s Printix, proveďte následující kroky:**

1. Na portálu Azure na **Printix** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/printix-tutorial/tutorial_printix_samlbase.png)

3. Na **Printix domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/printix-tutorial/tutorial_printix_url.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.printix.net`

    > [!NOTE] 
    > Hodnota není skutečné. Aktualizujte hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory Printix klienta](mailto:support@printix.net) k získání hodnoty. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/printix-tutorial/tutorial_printix_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/printix-tutorial/tutorial_general_400.png)

6. Přihlášení ke klientovi Printix jako správce.

7. V nabídce v horní části, klikněte na ikonu v pravém horním rohu a vyberte "**ověřování**".
   
    ![Konfigurovat jednotné přihlašování](./media/printix-tutorial/tutorial_printix_06.png)

8. Na **instalační program** vyberte **Azure nebo Office 365 povolit ověřování**
   
    ![Konfigurovat jednotné přihlašování](./media/printix-tutorial/tutorial_printix_07.png)

9. Na **Azure** kartě, zadejte adresu URL federačních metadat do textového pole z "**dokument federačních metadat**". 

    Připojte soubor metadat xml, který jste si stáhli z Azure AD [tým podpory Printix](mailto:support@printix.net). Pak nahrajte soubor xml a zadat adresu URL federačních metadat.
   
    ![Konfigurovat jednotné přihlašování](./media/printix-tutorial/tutorial_printix_08.png)
   
10. Klikněte na tlačítko "**testování**"tlačítko a klikněte na tlačítko"**OK**" tlačítko, pokud byl test úspěšný.
   
     Po kliknutí na tlačítko se zobrazí stránka služby Azure active directory **testování** tlačítko. "Test bylo úspěšné" zde znamená po zadání přihlašovacích údajů účtu Azure test, který bude pop se zobrazí zpráva "nastavení testovaný OK". Klikněte **OK** tlačítko.
   
    ![Konfigurovat jednotné přihlašování](./media/printix-tutorial/tutorial_printix_09.png)

11. Klikněte **Uložit** na tlačítko "**ověřování**" stránky.


> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/printix-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/printix-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/printix-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/printix-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-printix-test-user"></a>Vytvoření zkušebního uživatele Printix

Cílem této části je vytvoření uživatele v Printix nazývá Britta Simon. Printix podporuje za běhu zřizování, který je ve výchozím nastavení povolené.

Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Printix, pokud ještě neexistuje. 

> [!NOTE]
> Pokud potřebujete ručně vytvořit uživatele, budete muset kontaktovat [tým podpory Printix](mailto:support@printix.net).
> 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Printix.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Printix, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Printix**.

    ![Konfigurovat jednotné přihlašování](./media/printix-tutorial/tutorial_printix_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Printix na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Printix.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/printix-tutorial/tutorial_general_01.png
[2]: ./media/printix-tutorial/tutorial_general_02.png
[3]: ./media/printix-tutorial/tutorial_general_03.png
[4]: ./media/printix-tutorial/tutorial_general_04.png

[100]: ./media/printix-tutorial/tutorial_general_100.png

[200]: ./media/printix-tutorial/tutorial_general_200.png
[201]: ./media/printix-tutorial/tutorial_general_201.png
[202]: ./media/printix-tutorial/tutorial_general_202.png
[203]: ./media/printix-tutorial/tutorial_general_203.png

