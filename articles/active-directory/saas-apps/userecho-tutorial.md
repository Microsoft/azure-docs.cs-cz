---
title: 'Kurz: Azure Active Directory integrace s UserEcho | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a UserEcho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bedd916b-8f69-4b50-9b8d-56f4ee3bd3ed
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: f05189f70428eb3a3199870992956000dc55b954
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35903341"
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Kurz: Azure Active Directory integrace s UserEcho

V tomto kurzu zjistěte, jak integrovat UserEcho s Azure Active Directory (Azure AD).

Integrace UserEcho s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k UserEcho
- Můžete povolit uživatelům, aby automaticky získat přihlášení k UserEcho (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s UserEcho, potřebujete následující položky:

- Předplatné služby Azure AD
- UserEcho jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat a jeden měsíc zkušební: [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání UserEcho z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-userecho-from-the-gallery"></a>Přidání UserEcho z Galerie
Při konfiguraci integrace UserEcho do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS UserEcho z galerie.

**Pokud chcete přidat UserEcho z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **UserEcho**.

    ![Vytváření testovacího uživatele Azure AD](./media/userecho-tutorial/tutorial_userecho_search.png)

5. Na panelu výsledků vyberte **UserEcho**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/userecho-tutorial/tutorial_userecho_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s UserEcho podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v UserEcho je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v UserEcho musí navázat.

V UserEcho, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s UserEcho, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele UserEcho](#creating-a-userecho-test-user)**  – Pokud chcete mít protějšek Britta Simon v UserEcho propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci UserEcho.

**Ke konfiguraci Azure AD jednotné přihlašování s UserEcho, proveďte následující kroky:**

1. Na portálu Azure na **UserEcho** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/userecho-tutorial/tutorial_userecho_samlbase.png)

3. Na **UserEcho domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/userecho-tutorial/tutorial_userecho_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.userecho.com/`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory UserEcho klienta](https://feedback.userecho.com/) k získání těchto hodnot. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/userecho-tutorial/tutorial_userecho_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/userecho-tutorial/tutorial_general_400.png)

6. Na **UserEcho konfigurace** klikněte na tlačítko **konfigurace UserEcho** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL a SAML jeden přihlašování služby URL** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/userecho-tutorial/tutorial_userecho_configure.png) 

7. V jiném okně prohlížeče Přihlaste se k serveru vaší společnosti UserEcho jako správce.

8. Na panelu nástrojů v horní části na své uživatelské jméno, rozbalte nabídku a pak klikněte na **instalační program**.
   
    ![Konfigurovat jednotné přihlašování](./media/userecho-tutorial/tutorial_userecho_06.png) 

9. Klikněte na tlačítko **integrace**.
   
    ![Konfigurovat jednotné přihlašování](./media/userecho-tutorial/tutorial_userecho_07.png) 

10. Klikněte na tlačítko **webu**a potom klikněte na **jednotné přihlašování (typu SAML2)**.
   
    ![Konfigurovat jednotné přihlašování](./media/userecho-tutorial/tutorial_userecho_08.png) 

11. Na **jednotné přihlašování (SAML)** proveďte následující kroky:
   
    ![Konfigurovat jednotné přihlašování](./media/userecho-tutorial/tutorial_userecho_09.png)
    
    a. Jako **povoleno SAML**, vyberte **Ano**.
    
    b. Vložení **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure do **URL jednotné přihlašování SAML** textové pole.
    
    c. Vložení **Sign-Out URL**, který jste zkopírovali z portálu Azure do **adresy URL vzdálené logoout** textové pole.
    
    d. V poznámkovém bloku otevřete stažený certifikát, kopírovat obsah a vložte ji do **certifikát X.509** textové pole.
    
    e. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/userecho-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/userecho-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/userecho-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/userecho-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-userecho-test-user"></a>Vytvoření zkušebního uživatele UserEcho

Cílem této části je vytvoření uživatele v UserEcho nazývá Britta Simon.

**Vytvoření uživatele v UserEcho nazývá Britta Simon, proveďte následující kroky:**

1. Přihlašování k webu společnosti UserEcho jako správce.

2. Na panelu nástrojů v horní části na své uživatelské jméno, rozbalte nabídku a pak klikněte na **instalační program**.
   
    ![Konfigurovat jednotné přihlašování](./media/userecho-tutorial/tutorial_userecho_06.png)

3. Klikněte na tlačítko **uživatelé**, chcete-li rozšířit **uživatelé** části.
   
    ![Konfigurovat jednotné přihlašování](./media/userecho-tutorial/tutorial_userecho_10.png)

4. Klikněte na tlačítko **uživatelé**.
   
    ![Konfigurovat jednotné přihlašování](./media/userecho-tutorial/tutorial_userecho_11.png)

5. Klikněte na tlačítko **pozvat nového uživatele**.
   
    ![Konfigurovat jednotné přihlašování](./media/userecho-tutorial/tutorial_userecho_12.png)

6. Na **pozvat nového uživatele** dialogové okno, proveďte následující kroky:
   
    ![Konfigurovat jednotné přihlašování](./media/userecho-tutorial/tutorial_userecho_13.png)

    a. V **název** textovému poli, název typu uživatele jako Britta Simon.
    
    b.  V **e-mailu** jako typ e-mailovou adresu uživatele k textovému poli, Brittasimon@contoso.com.
    
    c. Klikněte na tlačítko **pozvat**.

Pozvánka se odesílají do Britta, který mu pomůže začít používat UserEcho umožňuje. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu UserEcho.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon UserEcho, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **UserEcho**.

    ![Konfigurovat jednotné přihlašování](./media/userecho-tutorial/tutorial_userecho_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je testování konfigurace Azure AD jednotného přihlašování k použití na přístupovém panelu.  

Když kliknete na dlaždici UserEcho na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci UserEcho.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/userecho-tutorial/tutorial_general_01.png
[2]: ./media/userecho-tutorial/tutorial_general_02.png
[3]: ./media/userecho-tutorial/tutorial_general_03.png
[4]: ./media/userecho-tutorial/tutorial_general_04.png

[100]: ./media/userecho-tutorial/tutorial_general_100.png

[200]: ./media/userecho-tutorial/tutorial_general_200.png
[201]: ./media/userecho-tutorial/tutorial_general_201.png
[202]: ./media/userecho-tutorial/tutorial_general_202.png
[203]: ./media/userecho-tutorial/tutorial_general_203.png

