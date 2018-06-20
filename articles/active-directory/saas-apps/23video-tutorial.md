---
title: 'Kurz: Azure Active Directory integrace s 23 Video | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a 23 Video.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5e73dd1d-3995-4a73-b9cf-1b2318d49cb3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: jeedes
ms.openlocfilehash: 8b4b41551a1679948518846a63eee87bbd1bbfd9
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36222653"
---
# <a name="tutorial-azure-active-directory-integration-with-23-video"></a>Kurz: Azure Active Directory integrace s 23 Video

V tomto kurzu zjistěte, jak integrovat 23 Video s Azure Active Directory (Azure AD).

Integrace 23 Video s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k 23 Video
- Můžete povolit uživatelům, aby automaticky získat přihlášení k 23 Video (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s 23 Video, potřebujete následující položky:

- Předplatné služby Azure AD
- Předplatné povolené 23 Video jednotného přihlašování

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání 23 Video z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-23-video-from-the-gallery"></a>Přidání 23 Video z Galerie
Při konfiguraci integrace 23 videa do služby Azure AD, potřebujete přidat 23 Video z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat 23 Video z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **23 Video**.

    ![Vytváření testovacího uživatele Azure AD](./media/23video-tutorial/tutorial_23video_search.png)

5. Na panelu výsledků vyberte **23 Video**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/23video-tutorial/tutorial_23video_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s 23 Video podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v 23 Video je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské ve 23 Video musí navázat.

V 23 Video přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s 23 Video, budete muset provést následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele 23 Video](#creating-a-23-video-test-user)**  – Pokud chcete mít protějšek Britta Simon ve 23 Video, které je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci 23 Video.

**Ke konfiguraci Azure AD jednotné přihlašování s 23 Video, proveďte následující kroky:**

1. Na portálu Azure na **23 Video** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/23video-tutorial/tutorial_23video_samlbase.png)

3. Na **23 Video domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/23video-tutorial/tutorial_23video_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.23video.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://www.23video.com/saml/trust/<uniqueid>`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [23 tým podpory Video klienta](mailto:support@23company.com) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/23video-tutorial/tutorial_23video_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/23video-tutorial/tutorial_general_400.png)

6. Na **23 konfigurace Video** klikněte na tlačítko **konfigurace Video 23** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/23video-tutorial/tutorial_23video_configure.png) 

7. Konfigurace jednotného přihlašování na **23 Video** straně, budete muset odeslat stažené **certifikátu (Base64)**, **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** k [23 tým podpory Video](mailto:support@23company.com). 


> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/23video-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/23video-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/23video-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/23video-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-23-video-test-user"></a>Vytvoření zkušebního uživatele 23 Video

Cílem této části je vytvoření uživatele volal Britta Simon v 23 Video.

**Pokud chcete vytvořit uživateli volat Britta Simon ve 23 Video, proveďte následující kroky:**

1. Přihlásit se k webu společnosti 23 Video jako správce.

2. Přejděte na **nastavení**.
 
3. V **uživatelé** klikněte na tlačítko **konfigurace**.
   
    ![Přiřadit uživatele][400]

4. Klikněte na tlačítko **přidat nového uživatele**. 
   
    ![Přiřadit uživatele][401]

5. V **žádost o připojení k této lokalitě** část, proveďte následující kroky:
   
    ![Přiřadit uživatele][402]

    a. V **e-mailové adresy** textovému poli, zadejte Britta Simon e-mailovou adresu ve službě Azure AD.  
 
    b. Klikněte na tlačítko **přidejte uživatele**.   

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu 23 Video.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon 23 Video, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **23 Video**.

    ![Konfigurovat jednotné přihlašování](./media/23video-tutorial/tutorial_23video_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je testování konfigurace Azure AD jednotného přihlašování k použití na přístupovém panelu.

Když kliknete na dlaždici 23 Video na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci 23 Video. 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/23video-tutorial/tutorial_general_01.png
[2]: ./media/23video-tutorial/tutorial_general_02.png
[3]: ./media/23video-tutorial/tutorial_general_03.png
[4]: ./media/23video-tutorial/tutorial_general_04.png

[100]: ./media/23video-tutorial/tutorial_general_100.png

[200]: ./media/23video-tutorial/tutorial_general_200.png
[201]: ./media/23video-tutorial/tutorial_general_201.png
[202]: ./media/23video-tutorial/tutorial_general_202.png
[203]: ./media/23video-tutorial/tutorial_general_203.png

[400]: ./media/23video-tutorial/tutorial_23video_10.png
[401]: ./media/23video-tutorial/tutorial_23video_11.png
[402]: ./media/23video-tutorial/tutorial_23video_12.png
