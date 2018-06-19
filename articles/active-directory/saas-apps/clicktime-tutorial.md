---
title: 'Kurz: Azure Active Directory integrace s ClickTime | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ClickTime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d437b5ab-4d71-4c13-96d0-79018cebbbd4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: jeedes
ms.openlocfilehash: 78dba504c332e20bfca913ae1d361deb633aa7fd
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35901656"
---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Kurz: Azure Active Directory integrace s ClickTime

V tomto kurzu zjistěte, jak integrovat ClickTime s Azure Active Directory (Azure AD).

Integrace ClickTime s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k ClickTime
- Můžete povolit uživatelům, aby automaticky získat přihlášení k ClickTime (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ClickTime, potřebujete následující položky:

- Předplatné služby Azure AD
- ClickTime jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání ClickTime z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-clicktime-from-the-gallery"></a>Přidání ClickTime z Galerie
Při konfiguraci integrace ClickTime do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS ClickTime z galerie.

**Pokud chcete přidat ClickTime z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **ClickTime**, vyberte **ClickTime** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![ClickTime v seznamu výsledků](./media/clicktime-tutorial/tutorial_clicktime_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s ClickTime podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v ClickTime je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v ClickTime musí navázat.

V ClickTime, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ClickTime, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele ClickTime](#create-a-clicktime-test-user)**  – Pokud chcete mít protějšek Britta Simon v ClickTime propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci ClickTime.

**Ke konfiguraci Azure AD jednotné přihlašování s ClickTime, proveďte následující kroky:**

1. Na portálu Azure na **ClickTime** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/clicktime-tutorial/tutorial_clicktime_samlbase.png)

3. Na **ClickTime domény a adresy URL** část, proveďte následující kroky:

    ![ClickTime domény a adresy URL jednotné přihlašování informace](./media/clicktime-tutorial/tutorial_clicktime_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL jako: `https://app.clicktime.com/sp/`
    
    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: 

    | |
    |--|
    | `https://app.clicktime.com/Login/` |
    | `https://app.clicktime.com/App/Login/Consume.aspx` |

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/clicktime-tutorial/tutorial_clicktime_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/clicktime-tutorial/tutorial_general_400.png)

6. Na **ClickTime konfigurace** klikněte na tlačítko **konfigurace ClickTime** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace ClickTime](./media/clicktime-tutorial/tutorial_clicktime_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti ClickTime.

8. Na panelu nástrojů v horní části klikněte na tlačítko **Předvolby**a potom klikněte na **nastavení zabezpečení**.

9. V **předvolby přihlašování** konfigurace části, proveďte následující kroky:
   
    ![Nastavení zabezpečení](./media/clicktime-tutorial/tic777280.png "nastavení zabezpečení")
   
    a.  Vyberte **povolit** přihlášení pomocí jednotné přihlašování (SSO) s **Azure AD**.
   
    b. V **koncový bod zprostředkovatele Identity** textovému poli, vložte **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.
   
    c.  Otevřete **certifikátu s kódováním base-64** stáhli z portálu Azure v **Poznámkový blok**, kopírovat obsah a vložte ji do **certifikát X.509** textové pole.
   
    d.  Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/clicktime-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.
    
    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/clicktime-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.
 
    ![Tlačítko Přidat](./media/clicktime-tutorial/create_aaduser_03.png) 

4. V **uživatele** dialogové okno pole, proveďte následující kroky:
 
    ![Dialogové okno uživatele](./media/clicktime-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-clicktime-test-user"></a>Vytvoření zkušebního uživatele ClickTime

Pokud chcete povolit uživatelům Azure AD přihlášení do ClickTime, musí být zřízená do ClickTime.  
V případě ClickTime zřizování je ruční úloha.

> [!NOTE]
> Můžete použít všechny ostatní ClickTime uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované ClickTime ke zřízení uživatelských účtů Azure AD.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**
1. Přihlaste se k vaší **ClickTime** klienta.
2. Na panelu nástrojů v horní části klikněte na tlačítko **společnosti**a potom klikněte na **osoby**.
   
    ![Lidé](./media/clicktime-tutorial/tic777282.png "osoby")
3. Klikněte na tlačítko **přidat osobu**.
   
    ![Přidat osobu](./media/clicktime-tutorial/tic777283.png "přidat osoba")
4. V části nové osobě proveďte následující kroky:
   
    ![Lidé](./media/clicktime-tutorial/tic777284.png "osoby")
   
    a.  V **celý název** textovému poli, celý název typ uživatele jako **Britta Simon**. 
  
    b.  V **e-mailová adresa** jako typ e-mailu uživatele k textovému poli, **brittasimon@contoso.com**.
       
    > [!NOTE]
    > Pokud chcete, můžete nastavit další vlastnosti nového objektu osoby.
   
    c.  Klikněte na **Uložit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu ClickTime.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon ClickTime, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **ClickTime**.

    ![Odkaz ClickTimne v seznamu aplikací](./media/clicktime-tutorial/tutorial_clicktime_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici ClickTime na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci ClickTime.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/clicktime-tutorial/tutorial_general_01.png
[2]: ./media/clicktime-tutorial/tutorial_general_02.png
[3]: ./media/clicktime-tutorial/tutorial_general_03.png
[4]: ./media/clicktime-tutorial/tutorial_general_04.png

[100]: ./media/clicktime-tutorial/tutorial_general_100.png

[200]: ./media/clicktime-tutorial/tutorial_general_200.png
[201]: ./media/clicktime-tutorial/tutorial_general_201.png
[202]: ./media/clicktime-tutorial/tutorial_general_202.png
[203]: ./media/clicktime-tutorial/tutorial_general_203.png

