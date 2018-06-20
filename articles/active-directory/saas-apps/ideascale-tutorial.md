---
title: 'Kurz: Azure Active Directory integrace s IdeaScale | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a IdeaScale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e16dda6b-fdf9-43cc-9bbb-a523f085a8af
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: bf42de8c1fa0e3c67a1a2bed6693b5adc0aae2d7
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225707"
---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Kurz: Azure Active Directory integrace s IdeaScale

V tomto kurzu zjistěte, jak integrovat IdeaScale s Azure Active Directory (Azure AD).

Integrace IdeaScale s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k IdeaScale
- Můžete povolit uživatelům, aby automaticky získat přihlášení k IdeaScale (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s IdeaScale, potřebujete následující položky:

- Předplatné služby Azure AD
- IdeaScale jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání IdeaScale z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-ideascale-from-the-gallery"></a>Přidání IdeaScale z Galerie
Při konfiguraci integrace IdeaScale do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS IdeaScale z galerie.

**Pokud chcete přidat IdeaScale z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **IdeaScale**.

    ![Vytváření testovacího uživatele Azure AD](./media/ideascale-tutorial/tutorial_ideascale_search.png)

5. Na panelu výsledků vyberte **IdeaScale**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/ideascale-tutorial/tutorial_ideascale_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s IdeaScale podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v IdeaScale je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v IdeaScale musí navázat.

V IdeaScale, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s IdeaScale, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření testovacího uživatele IdeaScale](#creating-an-ideascale-test-user)**  – Pokud chcete mít protějšek Britta Simon v IdeaScale propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci IdeaScale.

**Ke konfiguraci Azure AD jednotné přihlašování s IdeaScale, proveďte následující kroky:**

1. Na portálu Azure na **IdeaScale** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/ideascale-tutorial/tutorial_ideascale_samlbase.png)

3. Na **IdeaScale domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/ideascale-tutorial/tutorial_ideascale_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.ideascale.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | |
    |--|
    | `http://<companyname>.ideascale.com`  |
    | `https://<companyname>.ideascale.com` |

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory IdeaScale klienta](http://support.ideascale.com/) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/ideascale-tutorial/tutorial_ideascale_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/ideascale-tutorial/tutorial_general_400.png)

6. Na **IdeaScale konfigurace** klikněte na tlačítko **konfigurace IdeaScale** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresy URL a SAML Entity ID** z **Stručná referenční příručka části**.

    ![Konfigurovat jednotné přihlašování](./media/ideascale-tutorial/tutorial_ideascale_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti IdeaScale jako správce.

8. Přejděte na **komunity nastavení**.
   
    ![Nastavení komunity](./media/ideascale-tutorial/ic790847.png "nastavení komunity")

9. Přejděte na **zabezpečení \> jednoduchého nastavení Sign-on**.
   
    ![Jednoduchého nastavení Sign-on](./media/ideascale-tutorial/ic790848.png "jednoduchého nastavení Sign-on")

10. Jako **typu Single-Sign-on**, vyberte **SAML 2.0**.
   
    ![Jeden typ Sign-on](./media/ideascale-tutorial/ic790849.png "jeden typ Sign-on")

11. Na **nastavení jednotného Sign-on** dialogové okno, proveďte následující kroky:
   
    ![Jednoduchého nastavení Sign-on](./media/ideascale-tutorial/ic790850.png "jednoduchého nastavení Sign-on")
   
    a. V **SAML IdP Entity ID** textovému poli, vložte hodnotu **SAML Entity ID** který jste zkopírovali z portálu Azure.

    b. Kopírovat obsah souboru metadat stažený z portálu Azure a vložte ji do **SAML IdP Metadata** textové pole.

    c. V **adresy URL odhlašovací úspěch** textovému poli, vložte hodnotu **Sign-Out URL** který jste zkopírovali z portálu Azure.

    d. Klikněte na tlačítko **uložit změny**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/ideascale-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/ideascale-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/ideascale-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/ideascale-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-ideascale-test-user"></a>Vytváření testovacího uživatele IdeaScale

Pokud chcete povolit uživatelům Azure AD přihlášení do IdeaScale, se musí být zřízená v k IdeaScale. V případě IdeaScale zřizování je ruční úloha.

**Pokud chcete konfigurovat, zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **IdeaScale** společnosti lokality jako správce.

2. Přejděte na **komunity nastavení**.
   
    ![Nastavení komunity](./media/ideascale-tutorial/ic790847.png "nastavení komunity")

3. Přejděte na **základní nastavení \> člen správu**.

4. Klikněte na tlačítko **přidat člena**.
   
    ![Člen správu](./media/ideascale-tutorial/ic790852.png "člen správy")

5. V části Přidání nového člena proveďte následující kroky:
   
    ![Přidání nového člena](./media/ideascale-tutorial/ic790853.png "přidání nového člena")
   
    a. V **e-mailové adresy** textovému poli, zadejte e-mailovou adresu chcete zřídit platného účtu AAD.
   
    b. Klikněte na tlačítko **uložit změny**. 
   
    >[!NOTE]
    >Držitel účtu Azure Active Directory získá e-mail s odkazem pro potvrzení účtu před stane aktivní.
      
>[!NOTE]
>Můžete použít všechny ostatní IdeaScale uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované IdeaScale zřídit AAD uživatelské účty.
 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu IdeaScale.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon IdeaScale, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **IdeaScale**.

    ![Konfigurovat jednotné přihlašování](./media/ideascale-tutorial/tutorial_ideascale_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování


Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici IdeaScale na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci IdeaScale.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ideascale-tutorial/tutorial_general_01.png
[2]: ./media/ideascale-tutorial/tutorial_general_02.png
[3]: ./media/ideascale-tutorial/tutorial_general_03.png
[4]: ./media/ideascale-tutorial/tutorial_general_04.png

[100]: ./media/ideascale-tutorial/tutorial_general_100.png

[200]: ./media/ideascale-tutorial/tutorial_general_200.png
[201]: ./media/ideascale-tutorial/tutorial_general_201.png
[202]: ./media/ideascale-tutorial/tutorial_general_202.png
[203]: ./media/ideascale-tutorial/tutorial_general_203.png

