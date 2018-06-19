---
title: 'Kurz: Azure Active Directory integrace s Sprinklr | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Sprinklr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: 7037c509eeb4b7adf1232ebd29b7f7034237f22b
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35901760"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Kurz: Azure Active Directory integrace s Sprinklr

V tomto kurzu zjistěte, jak integrovat Sprinklr s Azure Active Directory (Azure AD).

Integrace Sprinklr s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Sprinklr
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Sprinklr (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Sprinklr, potřebujete následující položky:

- Předplatné služby Azure AD
- Sprinklr jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Sprinklr z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-sprinklr-from-the-gallery"></a>Přidání Sprinklr z Galerie
Při konfiguraci integrace Sprinklr do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Sprinklr z galerie.

**Pokud chcete přidat Sprinklr z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Sprinklr**.

    ![Vytváření testovacího uživatele Azure AD](./media/sprinklr-tutorial/tutorial_sprinklr_search.png)

5. Na panelu výsledků vyberte **Sprinklr**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/sprinklr-tutorial/tutorial_sprinklr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Sprinklr podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Sprinklr je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Sprinklr musí navázat.

V Sprinklr, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Sprinklr, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Sprinklr](#creating-a-sprinklr-test-user)**  – Pokud chcete mít protějšek Britta Simon v Sprinklr propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Sprinklr.

**Ke konfiguraci Azure AD jednotné přihlašování s Sprinklr, proveďte následující kroky:**

1. Na portálu Azure na **Sprinklr** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/sprinklr-tutorial/tutorial_sprinklr_samlbase.png)

3. Na **Sprinklr domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/sprinklr-tutorial/tutorial_sprinklr_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.sprinklr.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.sprinklr.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Aktualizujte hodnotu s skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory Sprinklr klienta](https://www.sprinklr.com/contact-us/) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/sprinklr-tutorial/tutorial_sprinklr_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/sprinklr-tutorial/tutorial_general_400.png)

6. Na **Sprinklr konfigurace** klikněte na tlačítko **konfigurace Sprinklr** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

7. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti Sprinklr jako správce.

8. Přejděte na **správy \> nastavení**.
   
    ![Správa](./media/sprinklr-tutorial/ic782907.png "správy")

9. Přejděte na **spravovat partnera \> jednotné přihlašování** na v levém podokně.
   
    ![Správa partnera](./media/sprinklr-tutorial/ic782908.png "spravovat partnera")

10. Klikněte na tlačítko **+ jednotné přihlašování, doplňky**.
   
    ![Jednotné přihlašování](./media/sprinklr-tutorial/ic782909.png "jednotného přihlašování")

11. Na **jednotného přihlašování** proveďte následující kroky:
   
    ![Jednotné přihlašování](./media/sprinklr-tutorial/ic782910.png "jednotného přihlašování")

    a. V **název** textovému poli, zadejte název pro svou konfiguraci (například: *WAADSSOTest*).

    b. Vyberte **povoleno**.

    c. Vyberte **používat nový certifikát jednotného přihlašování k**.
             
    e. V poznámkovém bloku otevřete váš kódování base-64 kódovaného certifikátu, zkopírujte obsah ho do schránky a vložte jej do **certifikát zprostředkovatele Identity** textové pole.

    f. Vložení **SAML Entity ID** hodnotu, která jste zkopírovali z portálu Azure do **Entity Id** textové pole.

    g. Vložení **SAML jeden přihlašování adresa URL služby** hodnotu, která jste zkopírovali z portálu Azure do **adresu URL pro přihlášení zprostředkovatele Identity** textové pole.

    h. Vložení **Sign-Out URL** hodnotu, která jste zkopírovali z portálu Azure do **adresa URL odhlašovací zprostředkovatele Identity** textové pole.
     
    i. Jako **typ ID uživatele SAML**, vyberte **kontrolní výraz obsahuje uživatele "uživatelské jméno s sprinklr.com**.

    j. Jako **umístění ID uživatele SAML**, vyberte **ID uživatele je v elementu identifikátor název subjektu příkaz**.

    k. Klikněte na **Uložit**.
       
    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/sprinklr-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/sprinklr-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/sprinklr-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/sprinklr-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-sprinklr-test-user"></a>Vytvoření zkušebního uživatele Sprinklr

1. Přihlaste se k serveru vaší společnosti Sprinklr jako správce.

2. Přejděte na **správy \> nastavení**.
   
    ![Správa](./media/sprinklr-tutorial/ic782907.png "správy")

3. Přejděte na **spravovat klienta \> uživatelé** v levém podokně.
   
    ![Nastavení](./media/sprinklr-tutorial/ic782914.png "nastavení")

4. Klikněte na tlačítko **přidat uživatele**.
   
    ![Nastavení](./media/sprinklr-tutorial/ic782915.png "nastavení")

5. Na **upravit uživatele** dialogové okno, proveďte následující kroky:
   
    ![Úprava uživatele](./media/sprinklr-tutorial/ic782916.png "úpravy uživatele") 

    a. V **e-mailu**, **křestní jméno** a **příjmení** textová pole, zadejte informace o účtu uživatele Azure AD, chcete zřídit.

    b. Vyberte **heslo zakázáno**.

    c. Vyberte **jazyk**.

    d. Vyberte **typ uživatele**.

    e. Klikněte na tlačítko **aktualizace**.
   
     >[!IMPORTANT]
     >**Heslo zakázáno** musí být vybrán tak, aby uživateli přihlásit se pomocí zprostředkovatele Identity. 
     
6. Přejděte na **Role**a poté proveďte následující kroky:
   
    ![Partner role](./media/sprinklr-tutorial/ic782917.png "Partner role")

    a. Z **globální** seznamu, vyberte **všechny\_oprávnění**.  

    b. Klikněte na tlačítko **aktualizace**.

>[!NOTE]
>Můžete použít všechny ostatní Sprinklr uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované Sprinklr ke zřízení uživatelských účtů Azure AD. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Sprinklr.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Sprinklr, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Sprinklr**.

    ![Konfigurovat jednotné přihlašování](./media/sprinklr-tutorial/tutorial_sprinklr_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Sprinklr na přístupovém panelu, měli byste obdržet automaticky přihlášení k aplikaci Sprinklr naleznete další informace o přístupový Panel [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sprinklr-tutorial/tutorial_general_01.png
[2]: ./media/sprinklr-tutorial/tutorial_general_02.png
[3]: ./media/sprinklr-tutorial/tutorial_general_03.png
[4]: ./media/sprinklr-tutorial/tutorial_general_04.png

[100]: ./media/sprinklr-tutorial/tutorial_general_100.png

[200]: ./media/sprinklr-tutorial/tutorial_general_200.png
[201]: ./media/sprinklr-tutorial/tutorial_general_201.png
[202]: ./media/sprinklr-tutorial/tutorial_general_202.png
[203]: ./media/sprinklr-tutorial/tutorial_general_203.png

