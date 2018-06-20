---
title: 'Kurz: Azure Active Directory integrace s Jobbadmin | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Jobbadmin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c5208b0d-66a3-49ed-9aad-70d21f54aee0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: jeedes
ms.openlocfilehash: 85c8f58fecf2a652db73a44650d26a3c9faf33e2
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36223089"
---
# <a name="tutorial-azure-active-directory-integration-with-jobbadmin"></a>Kurz: Azure Active Directory integrace s Jobbadmin

V tomto kurzu zjistěte, jak integrovat Jobbadmin s Azure Active Directory (Azure AD).

Integrace Jobbadmin s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Jobbadmin
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Jobbadmin (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Jobbadmin, potřebujete následující položky:

- Předplatné služby Azure AD
- Jobbadmin jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Jobbadmin z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-jobbadmin-from-the-gallery"></a>Přidání Jobbadmin z Galerie
Při konfiguraci integrace Jobbadmin do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Jobbadmin z galerie.

**Pokud chcete přidat Jobbadmin z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Jobbadmin**.

    ![Vytváření testovacího uživatele Azure AD](./media/jobbadmin-tutorial/tutorial_jobbadmin_search.png)

5. Na panelu výsledků vyberte **Jobbadmin**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/jobbadmin-tutorial/tutorial_jobbadmin_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Jobbadmin podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Jobbadmin je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Jobbadmin musí navázat.

V Jobbadmin, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Jobbadmin, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Jobbadmin](#creating-a-jobbadmin-test-user)**  – Pokud chcete mít protějšek Britta Simon v Jobbadmin propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Jobbadmin.

**Ke konfiguraci Azure AD jednotné přihlašování s Jobbadmin, proveďte následující kroky:**

1. Na portálu Azure na **Jobbadmin** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/jobbadmin-tutorial/tutorial_jobbadmin_samlbase.png)

3. Na **Jobbadmin domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/jobbadmin-tutorial/tutorial_jobbadmin_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<instancename>.jobbnorge.no/auth/saml2/login.ashx`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<instancename>.jobnorge.no`

    c. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<instancename>.jobbnorge.no/auth/saml2/login.ashx`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory Jobbadmin klienta](https://www.jobbnorge.no/om-oss/kontakt-oss) k získání těchto hodnot. 
 


4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/jobbadmin-tutorial/tutorial_jobbadmin_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/jobbadmin-tutorial/tutorial_general_400.png)

6. Konfigurace jednotného přihlašování na **Jobbadmin** straně, budete muset odeslat stažené **soubor XML s metadaty** k [tým podpory Jobbadmin](https://www.jobbnorge.no/om-oss/kontakt-oss). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/jobbadmin-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/jobbadmin-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/jobbadmin-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/jobbadmin-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-jobbadmin-test-user"></a>Vytvoření zkušebního uživatele Jobbadmin

Pokud chcete povolit uživatelům Azure AD přihlášení k Jobbadmin, musí být zřízená do Jobbadmin.
 
Obraťte se na [tým podpory Jobbadmin](https://www.jobbnorge.no/om-oss/kontakt-oss) získat uživatele, přidání na jejich straně.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Jobbadmin.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Jobbadmin, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Jobbadmin**.

    ![Konfigurovat jednotné přihlašování](./media/jobbadmin-tutorial/tutorial_jobbadmin_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Jobbadmin na přístupovém panelu, měli byste obdržet přihlašovací stránku Jobbadmin aplikace.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/jobbadmin-tutorial/tutorial_general_01.png
[2]: ./media/jobbadmin-tutorial/tutorial_general_02.png
[3]: ./media/jobbadmin-tutorial/tutorial_general_03.png
[4]: ./media/jobbadmin-tutorial/tutorial_general_04.png

[100]: ./media/jobbadmin-tutorial/tutorial_general_100.png

[200]: ./media/jobbadmin-tutorial/tutorial_general_200.png
[201]: ./media/jobbadmin-tutorial/tutorial_general_201.png
[202]: ./media/jobbadmin-tutorial/tutorial_general_202.png
[203]: ./media/jobbadmin-tutorial/tutorial_general_203.png

