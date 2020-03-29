---
title: 'Kurz: Integrace služby Azure Active Directory s aplikací Printix | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Printix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 4aea7320-b2d5-49e0-9b63-aeaff0f6fe66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e883833f7998c073b574c892ed5c7777e01faab4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "62111447"
---
# <a name="tutorial-azure-active-directory-integration-with-printix"></a>Kurz: Integrace služby Azure Active Directory s printixem

V tomto kurzu se dozvíte, jak integrovat Printix s Azure Active Directory (Azure AD).

Integrace Printix u Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, kdo má přístup k Printix
- Uživatelům můžete povolit automatické přihlášení k Printix (jednotné přihlašování) pomocí jejich účtů Azure AD.
- Své účty můžete spravovat na jednom centrálním místě – na portálu Azure

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, podívejte se, [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Printix, budete potřebovat následující položky:

- Předplatné Azure AD
- Předplatné s povoleným jedním přihlášením Printix

> [!NOTE]
> Chcete-li otestovat kroky v tomto kurzu, nedoporučujeme používat produkční prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte produkční prostředí, pokud to není nutné.
- Pokud nemáte zkušební prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu otestujete jednotné přihlašování Azure AD v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Printix z galerie
1. Konfigurace a testování jednotného přihlašování azure ad

## <a name="adding-printix-from-the-gallery"></a>Přidání Printix z galerie
Chcete-li nakonfigurovat integraci Printix do Azure AD, musíte přidat Printix z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat printix z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.** 

    ![Active Directory][1]

1. Přejděte k **podnikovým aplikacím**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Příkaz Printix**.

    ![Vytvoření testovacího uživatele Azure AD](./media/printix-tutorial/tutorial_printix_search.png)

1. V panelu výsledky vyberte **Printix**a pak klepnutím na **tlačítko Přidat** přidejte aplikaci.

    ![Vytvoření testovacího uživatele Azure AD](./media/printix-tutorial/tutorial_printix_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování azure ad
V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí printixu na základě testovacího uživatele s názvem "Britta Simon".

Aby jednotné přihlašování fungovalo, Azure AD potřebuje vědět, co je protějšek uživatele v Printix pro uživatele ve službě Azure AD. Jinými slovy vztah propojení mezi uživatelem Azure AD a související uživatel v Printix musí být vytvořen.

V Printix přiřaďte hodnotu **uživatelského jména** ve službě Azure AD jako hodnotu **uživatelského jména** k vytvoření vztahu propojení.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby Printix, je třeba provést následující stavební bloky:

1. **[Konfigurace jednotného přihlašování Azure AD](#configuring-azure-ad-single-sign-on)** – k tomu, aby uživatelé mohli tuto funkci používat.
1. **[Vytvoření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)** – k testování jednotného přihlašování Azure AD s Brittou Simonovou.
1. **[Vytvoření test Printix uživatele](#creating-a-printix-test-user)** – mít protějšek Britta Simon v Printix, který je propojen s reprezentaci Azure AD uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)** – chcete-li britta Simon používat Azure AD jednotné přihlašování.
1. **[Testování jednotného přihlášení](#testing-single-sign-on)** - k ověření, zda konfigurace funguje.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal a nakonfigurujete jednotné přihlašování v aplikaci Printix.

**Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí služby Printix, proveďte následující kroky:**

1. Na portálu Azure klikněte na stránce Integrace aplikace **Printix** na **Jedno přihlášení**.

    ![Konfigurace jednotného přihlašování][4]

1. V **dialogovém okně Jednotné přihlašování** vyberte **Režim** jako **přihlašování na základě SAML,** abyste povolili jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/printix-tutorial/tutorial_printix_samlbase.png)

1. V části **Printix Domain and URL proveďte** následující kroky:

    ![Konfigurace jednotného přihlašování](./media/printix-tutorial/tutorial_printix_url.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.printix.net`

    > [!NOTE] 
    > Hodnota není skutečná. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Chcete-li získat hodnotu, obraťte se na [tým podpory klienta Printix.](mailto:support@printix.net) 
 
1. V části **Podpisový certifikát SAML** klikněte na **Metadata XML** a uložte soubor metadat do počítače.

    ![Konfigurace jednotného přihlašování](./media/printix-tutorial/tutorial_printix_certificate.png) 

1. Klikněte na **tlačítko Uložit.**

    ![Konfigurace jednotného přihlašování](./media/printix-tutorial/tutorial_general_400.png)

1. Přihlaste se k tenantovi Printix jako správce.

1. V nabídce v horní části klikněte na ikonu v pravém horním rohu a vyberte "**Ověřování**".
   
    ![Konfigurace jednotného přihlašování](./media/printix-tutorial/tutorial_printix_06.png)

1. Na kartě **Setup** vyberte **Povolit ověřování Azure/Office 365.**
   
    ![Konfigurace jednotného přihlašování](./media/printix-tutorial/tutorial_printix_07.png)

1. Na kartě **Azure** zadejte adresu URL metadat federace do textového pole**dokumentu metadat federace.** 

    Připojte soubor XML metadat, který jste stáhli z Azure AD do [týmu podpory Printix](mailto:support@printix.net). Pak nahrají soubor XML a poskytnou adresu URL metadat federace.
   
    ![Konfigurace jednotného přihlašování](./media/printix-tutorial/tutorial_printix_08.png)
   
1. Klikněte na tlačítko **"Testovat"** a v případě úspěšného testu klepněte na tlačítko**OK.**
   
     Po kliknutí na **testovací** tlačítko se zobrazí stránka adresáře Azure active directory. "Test byl úspěšný" zde znamená, že po zadání přihlašovacích údajů vašeho testovacího účtu Azure se zobrazí zpráva "Nastavení testováno OK". Pak klikněte na tlačítko **OK.**
   
    ![Konfigurace jednotného přihlašování](./media/printix-tutorial/tutorial_printix_09.png)

1. Klepněte na tlačítko **Uložit** na stránce**Ověřování.**


> [!TIP]
> Teď si můžete přečíst stručnou verzi těchto pokynů uvnitř [portálu Azure](https://portal.azure.com), zatímco nastavujete aplikaci!  Po přidání této aplikace z oddílu **Active Directory > Enterprise Applications** jednoduše klikněte na kartu Jednotné **přihlášení** a získejte přístup k vložené dokumentaci prostřednictvím části **Konfigurace** v dolní části. Další informace o funkci vložené dokumentace najdete zde: [Integrovaná dokumentace azure ad]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD
Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na **webu Azure Portal**klikněte v levém navigačním podokně na ikonu **Služby Azure Active Directory.**

    ![Vytvoření testovacího uživatele Azure AD](./media/printix-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **položku Uživatelé a skupiny** a klepněte na tlačítko **Všichni uživatelé**.
    
    ![Vytvoření testovacího uživatele Azure AD](./media/printix-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít dialogové **okno Uživatel,** klepněte v horní části dialogového okna na **Přidat.**
 
    ![Vytvoření testovacího uživatele Azure AD](./media/printix-tutorial/create_aaduser_03.png) 

1. Na stránce **uživatelského** dialogového okna proveďte následující kroky:
 
    ![Vytvoření testovacího uživatele Azure AD](./media/printix-tutorial/create_aaduser_04.png) 

    a. Do textového pole **Název** zadejte **příkaz BrittaSimon**.

    b. Do textového pole **Uživatelské jméno** zadejte **e-mailovou adresu** BrittaSimon.

    c. Vyberte **Zobrazit heslo** a poznamenejte si hodnotu **hesla**.

    d. Klikněte na **Vytvořit**.
 
### <a name="creating-a-printix-test-user"></a>Vytvoření testovacího uživatele Printix

Cílem této části je vytvořit uživatele s názvem Britta Simon v Printix. Printix podporuje zřizování za v čase, které je ve výchozím nastavení povoleno.

V této části pro vás není žádná položka akce. Nový uživatel je vytvořen během pokusu o přístup printix, pokud ještě neexistuje. 

> [!NOTE]
> Pokud potřebujete vytvořit uživatele ručně, musíte kontaktovat [tým podpory Printix](mailto:support@printix.net).
> 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu Printix.

![Přiřadit uživatele][200] 

**Chcete-li brittu Simon přiřadit společnosti Printix, proveďte následující kroky:**

1. Na webu Azure Portal otevřete zobrazení aplikací a přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** a klikněte na **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Printix**.

    ![Konfigurace jednotného přihlašování](./media/printix-tutorial/tutorial_printix_app.png) 

1. V nabídce vlevo klikněte na **Uživatelé a skupiny**.

    ![Přiřadit uživatele][202] 

1. Klikněte na **tlačítko Přidat.** V dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Přiřadit uživatele][203]

1. V dialogovém **okně Uživatelé a skupiny** vyberte **britta Simon** v seznamu Uživatelé.

1. V dialogovém okně **Uživatelé a skupiny** klikněte na **tlačítko Vybrat.**

1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **Přiřadit.**
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po klepnutí na dlaždici Printix na přístupovém panelu byste se měli automaticky přihlásit k aplikaci Printix.

## <a name="additional-resources"></a>Další zdroje

* [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



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

