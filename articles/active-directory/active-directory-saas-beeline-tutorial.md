---
title: 'Kurz: Azure Active Directory integrace s BeeLine | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a BeeLine.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 0726859d-1dac-44a0-810b-da56d89039ee
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 385866434af29a3f377003ac33c199f091a282b8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34337125"
---
# <a name="tutorial-azure-active-directory-integration-with-beeline"></a>Kurz: Azure Active Directory integrace s BeeLine

V tomto kurzu zjistěte, jak integrovat BeeLine s Azure Active Directory (Azure AD).

Integrace BeeLine s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k BeeLine
- Můžete povolit uživatelům, aby automaticky získat přihlášení k BeeLine (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s BeeLine, potřebujete následující položky:

- Předplatné služby Azure AD
- BeeLine jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání BeeLine z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-beeline-from-the-gallery"></a>Přidání BeeLine z Galerie
Při konfiguraci integrace BeeLine do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS BeeLine z galerie.

**Pokud chcete přidat BeeLine z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **BeeLine**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_search.png)

5. Na panelu výsledků vyberte **BeeLine**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s BeeLine podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v BeeLine je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v BeeLine musí navázat.

V BeeLine, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s BeeLine, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele BeeLine](#creating-a-beeline-test-user)**  – Pokud chcete mít protějšek Britta Simon v BeeLine propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci BeeLine.

**Ke konfiguraci Azure AD jednotné přihlašování s BeeLine, proveďte následující kroky:**

1. Na portálu Azure na **BeeLine** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_samlbase.png)

3. Na **BeeLine domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://projects.beeline.net/<instancename>`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | |
    |--|
    | `https://projects.beeline.net/<instancename>/SSO_External.ashx`|
    | `https://projects.beeline.net/<companyname>/SSO_External.ashx` |

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem a adresa URL odpovědi. Obraťte se na [tým podpory BeeLine](https://www.beeline.com/contact-us/) k získání těchto hodnot.
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_certificate.png) 

5. Aplikace Beeline očekává SAML kontrolní výrazy ve specifickém formátu. Spojte se s [tým podpory BeeLine](https://www.beeline.com/contact-us/) nejprve k identifikaci správné uživatelské identifikátor, který budou zmapována do aplikace. Také proveďte pokynů od [tým podpory BeeLine](https://www.beeline.com/contact-us/) o atribut, který se má použít pro toto mapování. Hodnota tohoto atributu z můžete spravovat **uživatelské atributy** aplikace. Následující snímek obrazovky ukazuje příklad pro tento. Zde jsme jsou namapovány **uživatelský identifikátor** deklarace identity s **userprincipalname** atribut, který obsahuje jedinečné ID uživatele, která budou odeslána do aplikace Beeline v každé úspěšné odpovědi SAML.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-beeline-tutorial/tutorial_attribute.png)  

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-beeline-tutorial/tutorial_general_400.png)

7. Na **BeeLine konfigurace** klikněte na tlačítko **konfigurace BeeLine** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out URL** a **SAML Entity ID** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_configure.png) 

8. Konfigurace jednotného přihlašování na **BeeLine** straně, budete muset odeslat stažené **soubor XML s metadaty** a **SAML Entity ID**, **Sign-Out URL** k [tým podpory BeeLine](https://www.beeline.com/contact-us/).

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-beeline-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-beeline-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-beeline-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-beeline-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-beeline-test-user"></a>Vytvoření zkušebního uživatele BeeLine

V této části vytvoříte volal Britta Simon v Beeline uživatele. Beeline aplikace, musí všichni uživatelé zřídit v aplikaci před provedením jednotné přihlašování. Proto práce s [tým podpory BeeLine](https://www.beeline.com/contact-us/) ke zřízení tito uživatelé do aplikace. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu BeeLine.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon BeeLine, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **BeeLine**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu. Když kliknete na dlaždici Beeline na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Beeline.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_203.png

