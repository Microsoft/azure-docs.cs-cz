---
title: 'Kurz: Azure Active Directory integrace s Kintone | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Kintone.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2b947dc-e1a8-4f5f-b40e-2c5180648e4f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jeedes
ms.openlocfilehash: 3cc032a246a04dfa627f27b6661cdc540cf9e3b1
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35901626"
---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>Kurz: Azure Active Directory integrace s Kintone

V tomto kurzu zjistěte, jak integrovat Kintone s Azure Active Directory (Azure AD).

Integrace Kintone s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Kintone
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Kintone (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Kintone, potřebujete následující položky:

- Předplatné služby Azure AD
- Kintone jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Kintone z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-kintone-from-the-gallery"></a>Přidání Kintone z Galerie
Při konfiguraci integrace Kintone do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Kintone z galerie.

**Pokud chcete přidat Kintone z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Kintone**.

    ![Vytváření testovacího uživatele Azure AD](./media/kintone-tutorial/tutorial_kintone_search.png)

5. Na panelu výsledků vyberte **Kintone**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/kintone-tutorial/tutorial_kintone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Kintone podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Kintone je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Kintone musí navázat.

V Kintone, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Kintone, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Kintone](#creating-a-kintone-test-user)**  – Pokud chcete mít protějšek Britta Simon v Kintone propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Kintone.

**Ke konfiguraci Azure AD jednotné přihlašování s Kintone, proveďte následující kroky:**

1. Na portálu Azure na **Kintone** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/kintone-tutorial/tutorial_kintone_samlbase.png)

3. Na **Kintone domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/kintone-tutorial/tutorial_kintone_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.kintone.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | |
    |--|
    | `https://<companyname>.cybozu.com`|
    | `https://<companyname>.kintone.com`|

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory Kintone klienta](https://www.kintone.com/contact/) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/kintone-tutorial/tutorial_kintone_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/kintone-tutorial/tutorial_general_400.png)

6. Na **Kintone konfigurace** klikněte na tlačítko **konfigurace Kintone** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL a SAML jeden přihlašování služby URL** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/kintone-tutorial/tutorial_kintone_configure.png) 

7. V okně prohlížeče jiný web, přihlaste se k vaší **Kintone** společnosti lokality jako správce.

8. Klikněte na tlačítko **nastavení**.
   
    ![Nastavení](./media/kintone-tutorial/ic785879.png "nastavení")

9. Klikněte na tlačítko **uživatelů a správu systému**.
   
    ![Uživatelé a správu systému](./media/kintone-tutorial/ic785880.png "uživatelů a správu systému")

10. V části **systému správy \> zabezpečení** klikněte na tlačítko **přihlášení**.
   
    ![Přihlášení](./media/kintone-tutorial/ic785881.png "přihlášení")

11. Klikněte na tlačítko **ověřování povolit SAML**.
   
    ![Ověřování SAML](./media/kintone-tutorial/ic785882.png "ověřování SAML")

12. V části ověřování SAML proveďte následující kroky:
    
    ![Ověřování SAML](./media/kintone-tutorial/ic785883.png "ověřování SAML")
    
    a. V **přihlašovací adresa URL** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.
   
    b. V **adresy URL odhlašovací** textovému poli, vložte hodnotu **Sign-Out URL** který jste zkopírovali z portálu Azure.
    
    c. Klikněte na tlačítko **Procházet** nahrát stažený certifikát.
    
    d. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/kintone-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/kintone-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/kintone-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/kintone-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-kintone-test-user"></a>Vytvoření zkušebního uživatele Kintone

Pokud chcete povolit uživatelům Azure AD přihlášení k Kintone, musí být zřízená do Kintone.  
V případě Kintone zřizování je ruční úloha.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>K poskytnutí uživatelského účtu, proveďte následující kroky:

1. Přihlaste se k vaší **Kintone** společnosti lokality jako správce.

2. Klikněte na tlačítko **nastavení**.
   
    ![Nastavení](./media/kintone-tutorial/ic785879.png "nastavení")

3. Klikněte na tlačítko **uživatelů a správu systému**.
   
    ![& Systém správy uživatelského](./media/kintone-tutorial/ic785880.png "uživatele & správu systému")

4. V části **Správa uživatele**, klikněte na tlačítko **oddělení a uživatelé**.
   
    ![Oddělení a uživatelé](./media/kintone-tutorial/ic785888.png "oddělení a uživatelé")

5. Klikněte na tlačítko **nového uživatele**.
   
    ![Noví uživatelé](./media/kintone-tutorial/ic785889.png "noví uživatelé")

6. V **nového uživatele** část, proveďte následující kroky:
   
    ![Noví uživatelé](./media/kintone-tutorial/ic785890.png "noví uživatelé")
   
    a. Zadejte **zobrazovaný název**, **přihlašovací jméno**, **nové heslo**, **potvrzení hesla**, **e-mailová adresa**, a další podrobnosti o platný účet AAD chcete mají být zahrnuty do související textových polí.
 
    b. Klikněte na **Uložit**.

> [!NOTE]
> Můžete použít všechny ostatní Kintone uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované Kintone zřídit AAD uživatelské účty.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Kintone.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Kintone, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Kintone**.

    ![Konfigurovat jednotné přihlašování](./media/kintone-tutorial/tutorial_kintone_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Kintone na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Kintone.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kintone-tutorial/tutorial_general_01.png
[2]: ./media/kintone-tutorial/tutorial_general_02.png
[3]: ./media/kintone-tutorial/tutorial_general_03.png
[4]: ./media/kintone-tutorial/tutorial_general_04.png

[100]: ./media/kintone-tutorial/tutorial_general_100.png

[200]: ./media/kintone-tutorial/tutorial_general_200.png
[201]: ./media/kintone-tutorial/tutorial_general_201.png
[202]: ./media/kintone-tutorial/tutorial_general_202.png
[203]: ./media/kintone-tutorial/tutorial_general_203.png

