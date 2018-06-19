---
title: 'Kurz: Azure Active Directory integrace s e-mailu SkyDesk | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SkyDesk e-mailu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a9d0bbcb-ddb5-473f-a4aa-028ae88ced1a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: 5ed6ff9ad3a3358d6f14d6a615609728be5e4d80
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35904064"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Kurz: Azure Active Directory integrace s SkyDesk e-mailu

V tomto kurzu zjistěte, jak integrovat SkyDesk e-mailu s Azure Active Directory (Azure AD).

Integrace SkyDesk e-mailu s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k e-mailu SkyDesk
- Můžete povolit uživatelům, aby automaticky získat přihlášeného k e-mailu SkyDesk (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SkyDesk e-mailu, potřebujete následující položky:

- Předplatné služby Azure AD
- E-mailu SkyDesk jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat a jeden měsíc zkušební [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání e-mailu SkyDesk z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-skydesk-email-from-the-gallery"></a>Přidání e-mailu SkyDesk z Galerie
Pokud chcete nakonfigurovat integraci e-mailů SkyDesk do služby Azure AD, potřebujete přidat SkyDesk e-mailu z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat SkyDesk e-mailu z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **SkyDesk e-mailu**.

    ![Vytváření testovacího uživatele Azure AD](./media/skydeskemail-tutorial/tutorial_skydeskemail_search.png)

5. Na panelu výsledků vyberte **e-mailu SkyDesk**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/skydeskemail-tutorial/tutorial_skydeskemail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat, testovací Azure AD jednotné přihlašování s e-mailu SkyDesk podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v e-mailu SkyDesk je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v e-mailu SkyDesk musí navázat.

V e-mailu SkyDesk, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SkyDesk e-mailu, musíte dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele e-mailu SkyDesk](#creating-a-skydesk-email-test-user)**  – Pokud chcete mít protějšek Britta Simon SkyDesk e-mailem, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci SkyDesk e-mailu.

**Ke konfiguraci Azure AD jednotné přihlašování s SkyDesk e-mailu, proveďte následující kroky:**

1. Na portálu Azure na **e-mailu SkyDesk** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_samlbase.png)

3. Na **SkyDesk e-mailovou doménu a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_url.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE] 
    > Hodnota není skutečné. Aktualizujte hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory SkyDesk e-mailového klienta](https://www.skydesk.sg/support/) k získání hodnoty. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/skydeskemail-tutorial/tutorial_general_400.png)

6. Na **SkyDesk e-mailové konfigurace** klikněte na tlačítko **nakonfigurovat e-mailu SkyDesk** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL a SAML jeden přihlašování služby URL** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_configure.png) 

7. K povolení přihlášení SSO v **e-mailu SkyDesk**, proveďte následující kroky:

    a. Přihlášení ke svému účtu e-mailu SkyDesk jako správce.

    b. V nabídce v horní části, klikněte na tlačítko **instalace**a vyberte **Org**. 
    
      ![Konfigurovat jednotné přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
    c. Klikněte na **domén** v levém panelu.
    
      ![Konfigurovat jednotné přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

    d. Klikněte na **přidat doménu**.
    
      ![Konfigurovat jednotné přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

    e. Zadejte název domény a potom ověření domény.
    
      ![Konfigurovat jednotné přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

    f. Klikněte na **ověřování SAML** v levém panelu.
    
      ![Konfigurovat jednotné přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

8. Na **ověřování SAML** dialogové okno stránky, proveďte následující kroky:
   
      ![Konfigurovat jednotné přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)
   
    >[!NOTE]
    >Pokud chcete používat ověřování na základě SAML, byste měli buď mít **ověřené doméně** nebo **portál adresy URL** instalační program. Na portálu můžete nastavit adresu URL s jedinečným názvem.
    > 
    > 
   
    ![Konfigurovat jednotné přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. V **přihlašovací adresa URL** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.
   
    b. V **odhlášení** textovému poli Adresa URL, vložte hodnotu **Sign-Out URL**, který jste zkopírovali z portálu Azure.

    c. **Změnit heslo URL** je volitelný, nechte pole prázdné.

    d. Klikněte na **získat klíč ze souboru** vyberte svůj certifikát stažený z portálu Azure, a pak klikněte na **otevřete** na kterou odešlete certifikát.

    e. Jako **algoritmus**, vyberte **RSA**.

    f. Klikněte na tlačítko **Ok** a uložte změny.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/skydeskemail-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/skydeskemail-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/skydeskemail-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/skydeskemail-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-skydesk-email-test-user"></a>Vytvoření zkušebního uživatele SkyDesk e-mailu

V této části vytvoříte uživatele volat Britta Simon SkyDesk e-mailem.

1. Klikněte na **přístup uživatelů** v levém panelu v e-mailu SkyDesk a pak zadejte svoje uživatelské jméno. 

    ![Konfigurovat jednotné přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

>[!NOTE] 
>Pokud potřebujete vytvořit uživatele hromadně, budete muset kontaktovat [tým podpory SkyDesk e-mailového klienta](https://www.skydesk.sg/support/).


### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k e-mailu SkyDesk.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon SkyDesk e-mailu, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **SkyDesk e-mailu**.

    ![Konfigurovat jednotné přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je testování konfigurace Azure AD jednotného přihlašování k použití na přístupovém panelu.

Když kliknete na dlaždici SkyDesk e-mailu na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci SkyDesk e-mailu.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skydeskemail-tutorial/tutorial_general_01.png
[2]: ./media/skydeskemail-tutorial/tutorial_general_02.png
[3]: ./media/skydeskemail-tutorial/tutorial_general_03.png
[4]: ./media/skydeskemail-tutorial/tutorial_general_04.png

[100]: ./media/skydeskemail-tutorial/tutorial_general_100.png

[200]: ./media/skydeskemail-tutorial/tutorial_general_200.png
[201]: ./media/skydeskemail-tutorial/tutorial_general_201.png
[202]: ./media/skydeskemail-tutorial/tutorial_general_202.png
[203]: ./media/skydeskemail-tutorial/tutorial_general_203.png

