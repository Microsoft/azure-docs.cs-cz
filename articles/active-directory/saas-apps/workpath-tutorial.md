---
title: 'Kurz: Azure Active Directory integrace s Workpath | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Workpath.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 320b0daf-14be-4813-b59b-25a6a5070690
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: 925d4b0d8c116887c14d22f821500273daefba40
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36218590"
---
# <a name="tutorial-azure-active-directory-integration-with-workpath"></a>Kurz: Azure Active Directory integrace s Workpath

V tomto kurzu zjistěte, jak integrovat Workpath s Azure Active Directory (Azure AD).

Integrace Workpath s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Workpath
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Workpath (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Workpath, potřebujete následující položky:

- Předplatné služby Azure AD
- Workpath jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Workpath z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-workpath-from-the-gallery"></a>Přidání Workpath z Galerie
Při konfiguraci integrace Workpath do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Workpath z galerie.

**Pokud chcete přidat Workpath z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Workpath**.

    ![Vytváření testovacího uživatele Azure AD](./media/workpath-tutorial/tutorial_workpath_search.png)

5. Na panelu výsledků vyberte **Workpath**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/workpath-tutorial/tutorial_workpath_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Workpath podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Workpath je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Workpath musí navázat.

V Workpath, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Workpath, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Workpath](#creating-a-workpath-test-user)**  – Pokud chcete mít protějšek Britta Simon v Workpath propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Workpath.

**Ke konfiguraci Azure AD jednotné přihlašování s Workpath, proveďte následující kroky:**

1. Na portálu Azure na **Workpath** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/workpath-tutorial/tutorial_workpath_samlbase.png)

3. Na **Workpath domény a adresy URL** část, pokud chcete nakonfigurovat aplikace **IDP** initiated režimu proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/workpath-tutorial/tutorial_workpath_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://api.workpath.com/v1/saml/metadata/<instancename>`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://api.workpath.com/v1/saml/assert/<instancename>`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL**. Pokud chcete nakonfigurovat aplikace **SP** iniciované režimu, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/workpath-tutorial/tutorial_workpath_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.workpath.com/ `

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečná adresa URL přihlašování, identifikátor a adresa URL odpovědi. Obraťte se na [tým podpory Workpath](https://help.workpath.com) k získání těchto hodnot.

5. Aplikace Workpath očekává SAML kontrolní výrazy ve specifickém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z "**uživatelské atributy**" části na stránce integrace aplikace. Následující snímek obrazovky ukazuje příklad pro tuto konfiguraci. 

    ![Konfigurovat jednotné přihlašování](./media/workpath-tutorial/tutorial_workpath_attributes.png)
    
6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ------------------- | -------------------- |    
    | křestní_jméno | user.givenname |
    | Příjmení | user.surname |
    
    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/workpath-tutorial/tutorial_attribute_04.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    ![Konfigurovat jednotné přihlašování](./media/workpath-tutorial/tutorial_attribute_05.png)

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d. Ponechte **Namespace** textové pole prázdné.
    
    e. Klikněte na tlačítko **OK**.
    

7. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/workpath-tutorial/tutorial_workpath_certificate.png) 

8. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/workpath-tutorial/tutorial_general_400.png)

9. Na **Workpath konfigurace** klikněte na tlačítko **konfigurace Workpath** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/workpath-tutorial/tutorial_workpath_configure.png) 

10. Konfigurace jednotného přihlašování na **Workpath** straně, budete muset odeslat stažené **soubor XML s metadaty**, **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** k [tým podpory Workpath](https://help.workpath.com). 

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/workpath-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/workpath-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/workpath-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/workpath-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-workpath-test-user"></a>Vytvoření zkušebního uživatele Workpath

Workpath podporuje pouze v době zřizování uživatelů. Po ověření uživatelé jsou automaticky vytvořené v aplikaci. 


### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Workpath.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Workpath, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Workpath**.

    ![Konfigurovat jednotné přihlašování](./media/workpath-tutorial/tutorial_workpath_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Workpath na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Workpath.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/workpath-tutorial/tutorial_general_01.png
[2]: ./media/workpath-tutorial/tutorial_general_02.png
[3]: ./media/workpath-tutorial/tutorial_general_03.png
[4]: ./media/workpath-tutorial/tutorial_general_04.png

[100]: ./media/workpath-tutorial/tutorial_general_100.png

[200]: ./media/workpath-tutorial/tutorial_general_200.png
[201]: ./media/workpath-tutorial/tutorial_general_201.png
[202]: ./media/workpath-tutorial/tutorial_general_202.png
[203]: ./media/workpath-tutorial/tutorial_general_203.png

