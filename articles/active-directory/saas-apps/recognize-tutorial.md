---
title: 'Kurz: Azure Active Directory integrace s rozpoznávat | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a rozpoznávat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: f49fe131d29ab6d635fd79b56f3159d76a6e220a
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35899080"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Kurz: Azure Active Directory integrace s rozpoznávat

V tomto kurzu zjistěte, jak integrovat rozpoznávat s Azure Active Directory (Azure AD).

Integrace rozpoznávat s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k rozpoznání
- Můžete povolit uživatelům, aby automaticky získat přihlášení k rozpoznání (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s rozpoznávat, potřebujete následující položky:

- Předplatné služby Azure AD
- Rozpoznávat jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat a jeden měsíc zkušební: [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání rozpoznávat z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-recognize-from-the-gallery"></a>Přidání rozpoznávat z Galerie
Při konfiguraci integrace rozpoznávat do služby Azure AD potřebujete přidat rozpoznávat z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat rozpoznávat z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **rozpoznávat**.

    ![Vytváření testovacího uživatele Azure AD](./media/recognize-tutorial/tutorial_recognize_search.png)

5. Na panelu výsledků vyberte **rozpoznávat**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/recognize-tutorial/tutorial_recognize_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s rozpoznávat podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v rozpoznávat je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v rozpoznávat musí navázat.

V rozpoznávat, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s rozpoznávat, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele rozpoznávat](#creating-a-recognize-test-user)**  – Pokud chcete mít protějšek Britta Simon v rozpoznávat propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci rozpoznávat.

**Ke konfiguraci Azure AD jednotné přihlašování s rozpoznávat, proveďte následující kroky:**

1. Na portálu Azure na **rozpoznávat** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/recognize-tutorial/tutorial_recognize_samlbase.png)

3. Na **rozpoznat domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/recognize-tutorial/tutorial_recognize_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://recognizeapp.com/<your-domain>/saml/sso`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://recognizeapp.com/<your-domain>`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory rozpoznat klienta](mailto:support@recognizeapp.com) získat přihlašovací adresa URL a získáte hodnota identifikátoru po otevření této adresy URL služby zprostředkovatele metadat z části Nastavení jednotného přihlašování, která je vysvětlená později v tomto kurzu. . 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/recognize-tutorial/tutorial_recognize_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/recognize-tutorial/tutorial_general_400.png)

6. Na **rozpoznat konfigurace** klikněte na tlačítko **konfigurace rozpoznat** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/recognize-tutorial/tutorial_recognize_configure.png) 

7. V okně prohlížeče jiných webových přihlašování ke klientovi rozpoznávat jako správce.

8. V pravém horním rohu klikněte na tlačítko **nabídky**. Přejděte na **společnosti správce**.
   
    ![Konfigurace straně jediné přihlášení na aplikaci](./media/recognize-tutorial/tutorial_recognize_000.png)

9. V levém navigačním podokně klikněte na tlačítko **nastavení**.
   
    ![Konfigurace straně jediné přihlášení na aplikaci](./media/recognize-tutorial/tutorial_recognize_001.png)

10. Proveďte následující kroky na **nastavení jednotného přihlašování k** části.
   
    ![Konfigurace straně jediné přihlášení na aplikaci](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Jako **povolit jednotné přihlašování**, vyberte **ON**.

    b. V **IDP Entity ID** textovému poli, vložte hodnotu **SAML Entity ID** který jste zkopírovali z portálu Azure.
    
    c. V **jednotné přihlašování cílová adresa url** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.
    
    d. V **Slo cílová adresa url** textovému poli, vložte hodnotu **Sign-Out URL** který jste zkopírovali z portálu Azure. 
    
    e. Otevřete váš stažené **certifikátu (Base64)** souboru v programu Poznámkový blok, zkopírujte obsah ho do schránky a vložte jej do **certifikát** textové pole.
    
    f. Klikněte **uložit nastavení** tlačítko. 

11. Vedle položky **nastavení jednotného přihlašování k** část, zkopírujte adresu URL v části **adresu url služby zprostředkovatele metadat**.
   
    ![Konfigurace straně jediné přihlášení na aplikaci](./media/recognize-tutorial/tutorial_recognize_003.png)

12. Otevřete **adresu URL metadat odkaz** pod prázdnou prohlížeč o stažení dokument metadat. Pak zkopírujte EntityDescriptor value(entityID) ze souboru a vložte jej do **identifikátor** textového pole v **rozpoznat domény a adresy URL části** na portálu Azure.
    
    ![Konfigurace straně jediné přihlášení na aplikaci](./media/recognize-tutorial/tutorial_recognize_004.png)

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/recognize-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/recognize-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/recognize-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/recognize-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-recognize-test-user"></a>Vytvoření zkušebního uživatele rozpoznávat

Pokud chcete povolit uživatelům Azure AD přihlášení do rozpoznávat, musí být zřízená do rozpoznávat. V případě rozpoznávat zřizování je ruční úloha.

Tato aplikace nepodporuje SCIM zřizování, ale má synchronizaci alternativní uživatele, která zřizuje uživatele. 

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k serveru vaší společnosti rozpoznávat jako správce.

2. V pravém horním rohu klikněte na tlačítko **nabídky**. Přejděte na **společnosti správce**.

3. V levém navigačním podokně klikněte na tlačítko **nastavení**.

4. Proveďte následující kroky na **synchronizace uživatele** části.
   
   ![Nový uživatel](./media/recognize-tutorial/tutorial_recognize_005.png "nového uživatele")
   
   a. Jako **povolená synchronizace**, vyberte **ON**.
   
   b. Jako **zprostředkovatele synchronizace zvolte**, vyberte **Microsoft nebo Office 365**.
   
   c. Klikněte na tlačítko **spustíte synchronizaci uživatelů**.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k rozpoznání.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon rozpoznávat, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **rozpoznávat**.

    ![Konfigurovat jednotné přihlašování](./media/recognize-tutorial/tutorial_recognize_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici rozpoznávat na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci rozpoznávat. Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/recognize-tutorial/tutorial_general_01.png
[2]: ./media/recognize-tutorial/tutorial_general_02.png
[3]: ./media/recognize-tutorial/tutorial_general_03.png
[4]: ./media/recognize-tutorial/tutorial_general_04.png

[100]: ./media/recognize-tutorial/tutorial_general_100.png

[200]: ./media/recognize-tutorial/tutorial_general_200.png
[201]: ./media/recognize-tutorial/tutorial_general_201.png
[202]: ./media/recognize-tutorial/tutorial_general_202.png
[203]: ./media/recognize-tutorial/tutorial_general_203.png

