---
title: 'Kurz: Azure Active Directory integrace s most | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a most.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 3dbb6499-80c1-4d00-a0b4-e0ad5522cf0f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 1b92773aa3a8dd098e3dffe34f59e6750e3ad23c
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210821"
---
# <a name="tutorial-azure-active-directory-integration-with-bridge"></a>Kurz: Azure Active Directory integrace s most

V tomto kurzu zjistěte, jak integrovat most s Azure Active Directory (Azure AD).

Integrace s Azure AD most? víc informací nabízí následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k most
- Můžete povolit uživatelům, aby automaticky získat přihlášení k most (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s most, potřebujete následující položky:

- Předplatné služby Azure AD
- Most jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání most z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-bridge-from-the-gallery"></a>Přidání most z Galerie
Při konfiguraci integrace most do služby Azure AD potřebujete přidat most z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat most z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **most**.

    ![Vytváření testovacího uživatele Azure AD](./media/bridge-tutorial/tutorial_bridge_search.png)

5. Na panelu výsledků vyberte **most**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/bridge-tutorial/tutorial_bridge_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s most podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v most je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v most musí navázat.

V mostu, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s most, musíte dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele most](#creating-a-bridge-test-user)**  – Pokud chcete mít protějšek Britta Simon v mostu, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci most.

**Ke konfiguraci Azure AD jednotné přihlašování s most, proveďte následující kroky:**

1. Na portálu Azure na **most** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/bridge-tutorial/tutorial_bridge_samlbase.png)

3. Na **most domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/bridge-tutorial/tutorial_bridge_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<company name>.bridgeapp.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<company name>.bridgeapp.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory most klienta](https://community.bridgeapp.com/community/help) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Raw)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/bridge-tutorial/tutorial_bridge_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/bridge-tutorial/tutorial_general_400.png)

6. Na **konfigurace mostu** klikněte na tlačítko **konfigurace mostu** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/bridge-tutorial/tutorial_bridge_configure.png) 

7. Konfigurace jednotného přihlašování na **most** straně, budete muset odeslat stažené **Certificate(Raw)** a **SAML Entity ID, SAML jeden přihlašování adresa URL služby a adresa URL Sign-Out** k [tým podpory most](https://community.bridgeapp.com/community/help). 

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/bridge-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/bridge-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/bridge-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/bridge-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-bridge-test-user"></a>Vytvoření zkušebního uživatele most

V této části vytvoříte uživatele volal Britta Simon v most. Práce s [tým podpory most klienta](https://community.bridgeapp.com/community/help) vytvoření uživatele v platformu. Můžete zvýšit lístku podpory s most z <a href="https://community.bridgeapp.com/community/help">sem</a> přidat uživatele do mostu platformy.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu most.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon most, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **most**.

    ![Konfigurovat jednotné přihlašování](./media/bridge-tutorial/tutorial_bridge_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete otestovat vaši konfiguraci Azure AD jednotného přihlašování k použití na přístupovém panelu.

Když kliknete na dlaždici mostu na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci most.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/bridge-tutorial/tutorial_general_01.png
[2]: ./media/bridge-tutorial/tutorial_general_02.png
[3]: ./media/bridge-tutorial/tutorial_general_03.png
[4]: ./media/bridge-tutorial/tutorial_general_04.png

[100]: ./media/bridge-tutorial/tutorial_general_100.png

[200]: ./media/bridge-tutorial/tutorial_general_200.png
[201]: ./media/bridge-tutorial/tutorial_general_201.png
[202]: ./media/bridge-tutorial/tutorial_general_202.png
[203]: ./media/bridge-tutorial/tutorial_general_203.png

