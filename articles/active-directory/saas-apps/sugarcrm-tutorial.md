---
title: 'Kurz: Azure Active Directory integrace s Sugar CRM | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Sugar CRM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.openlocfilehash: 72da81d3b208fce6375bcafb66caac0124699507
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35901764"
---
# <a name="tutorial-azure-active-directory-integration-with-sugar-crm"></a>Kurz: Azure Active Directory integrace s Sugar CRM

V tomto kurzu zjistěte, jak integrovat Sugar CRM s Azure Active Directory (Azure AD).

Integrace s Azure AD Sugar CRM poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Sugar CRM
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Sugar CRM (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Sugar CRM, budete potřebovat následující položky:

- Předplatné služby Azure AD
- Sugar CRM jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Sugar CRM z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-sugar-crm-from-the-gallery"></a>Přidání Sugar CRM z Galerie
Při konfiguraci integrace Sugar CRM do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Sugar CRM z galerie.

**Pokud chcete přidat Sugar CRM z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Sugar CRM**.

    ![Vytváření testovacího uživatele Azure AD](./media/sugarcrm-tutorial/tutorial_sugarcrm_search.png)

5. Na panelu výsledků vyberte **Sugar CRM**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/sugarcrm-tutorial/tutorial_sugarcrm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Sugar CRM podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Sugar CRM je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Sugar CRM je potřeba vytvořit.

V Sugar CRM přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Sugar CRM, budete muset provést následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Sugar CRM](#creating-a-sugar-crm-test-user)**  – Pokud chcete mít protějšek Britta Simon v CRM Sugar, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Sugar CRM.

**Ke konfiguraci Azure AD jednotné přihlašování s Sugar CRM, proveďte následující kroky:**

1. Na portálu Azure na **Sugar CRM** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/sugarcrm-tutorial/tutorial_sugarcrm_samlbase.png)

3. Na **Sugar CRM domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/sugarcrm-tutorial/tutorial_sugarcrm_url.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | |
    |--|
    | `https://<companyname>.sugarondemand.com` |
    | `https://<companyname>.trial.sugarcrm` |

    > [!NOTE] 
    > Hodnota není skutečné. Aktualizujte hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory klienta CRM Sugar](https://support.sugarcrm.com/) k získání hodnoty. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/sugarcrm-tutorial/tutorial_sugarcrm_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/sugarcrm-tutorial/tutorial_general_400.png)

6. Na **Sugar CRM konfigurace** klikněte na tlačítko **konfigurace CRM Sugar** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL a SAML jeden přihlašování služby URL** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/sugarcrm-tutorial/tutorial_sugarcrm_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se na váš web společnosti Sugar CRM jako správce.

8. Přejděte na **správce**.
   
    ![Správce](./media/sugarcrm-tutorial/ic795888.png "správce")

9. V **správy** klikněte na tlačítko **Správa hesel**.
   
    ![Správa](./media/sugarcrm-tutorial/ic795889.png "správy")

10. Vyberte **povolit ověřování SAML**.
   
    ![Správa](./media/sugarcrm-tutorial/ic795890.png "správy")

11. V **ověřování SAML** část, proveďte následující kroky:
   
    ![Ověřování SAML](./media/sugarcrm-tutorial/ic795891.png "ověřování SAML")  
 
    a. V **přihlašovací adresa URL** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.
  
    b. V **SLO URL** textovému poli, vložte hodnotu **Sign-Out URL**, který jste zkopírovali z portálu Azure.
  
    c. V poznámkovém bloku otevřete váš kódování base-64 kódovaného certifikátu, zkopírujte obsah ho do schránky a vložte celý certifikát do **certifikát X.509** textové pole.
  
    d. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/sugarcrm-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/sugarcrm-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/sugarcrm-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/sugarcrm-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-sugar-crm-test-user"></a>Vytvoření zkušebního uživatele Sugar CRM

Pokud chcete povolit uživatelům Azure AD přihlášení k Sugar CRM, musí být zřízená k Sugar CRM.

V případě Sugar CRM je zřizování ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k vaší **Sugar CRM** společnosti lokality jako správce.

2. Přejděte na **správce**.
   
    ![Správce](./media/sugarcrm-tutorial/ic795888.png "správce")

3. V **správy** klikněte na tlačítko **Správa uživatelů**.
   
    ![Správa](./media/sugarcrm-tutorial/ic795893.png "správy")

4. Přejděte na **uživatelé \> vytvořit nového uživatele**.
   
    ![Vytvoření nového uživatele](./media/sugarcrm-tutorial/ic795894.png "vytvořit nového uživatele")

5. Na **profil uživatele** kartu, proveďte následující kroky:
   
    ![Nový uživatel](./media/sugarcrm-tutorial/ic795895.png "nového uživatele")

    a. Typ **uživatelské jméno**, **příjmení**, a **e-mailová adresa** platný uživatele Azure Active Directory do související textových polí.
  
6. Jako **stav**, vyberte **Active**.

7. Na kartě heslo proveďte následující kroky:
   
    ![Nový uživatel](./media/sugarcrm-tutorial/ic795896.png "nového uživatele")

    a. Zadejte heslo do textového pole související.

    b. Klikněte na **Uložit**.

>[!NOTE]
>Můžete použít všechny ostatní Sugar CRM uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované Sugar CRM zřídit AAD uživatelské účty. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k Sugar CRM.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Sugar CRM, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Sugar CRM**.

    ![Konfigurovat jednotné přihlašování](./media/sugarcrm-tutorial/tutorial_sugarcrm_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Sugar CRM na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Sugar CRM.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sugarcrm-tutorial/tutorial_general_01.png
[2]: ./media/sugarcrm-tutorial/tutorial_general_02.png
[3]: ./media/sugarcrm-tutorial/tutorial_general_03.png
[4]: ./media/sugarcrm-tutorial/tutorial_general_04.png

[100]: ./media/sugarcrm-tutorial/tutorial_general_100.png

[200]: ./media/sugarcrm-tutorial/tutorial_general_200.png
[201]: ./media/sugarcrm-tutorial/tutorial_general_201.png
[202]: ./media/sugarcrm-tutorial/tutorial_general_202.png
[203]: ./media/sugarcrm-tutorial/tutorial_general_203.png

