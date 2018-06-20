---
title: 'Kurz: Azure Active Directory integrace s Communifire | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Communifire.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: de2a164d-2115-43e7-a9ed-e54f483f4aeb
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: jeedes
ms.openlocfilehash: 30f97d203f014355daf72a0bbd474126cc225983
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36217411"
---
# <a name="tutorial-azure-active-directory-integration-with-communifire"></a>Kurz: Azure Active Directory integrace s Communifire

V tomto kurzu zjistěte, jak integrovat Communifire s Azure Active Directory (Azure AD).

Integrace Communifire s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Communifire.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Communifire (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Communifire, potřebujete následující položky:

- Předplatné služby Azure AD
- Communifire jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Communifire z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-communifire-from-the-gallery"></a>Přidání Communifire z Galerie
Při konfiguraci integrace Communifire do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Communifire z galerie.

**Pokud chcete přidat Communifire z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Communifire**, vyberte **Communifire** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Communifire v seznamu výsledků](./media/communifire-tutorial/tutorial_communifire_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Communifire podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Communifire je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Communifire musí navázat.

V Communifire, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Communifire, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Communifire](#create-a-communifire-test-user)**  – Pokud chcete mít protějšek Britta Simon v Communifire propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Communifire.

**Ke konfiguraci Azure AD jednotné přihlašování s Communifire, proveďte následující kroky:**

1. Na portálu Azure na **Communifire** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/communifire-tutorial/tutorial_communifire_samlbase.png)

3. Na **Communifire domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v režimu rozšíření IDP iniciované:

    ![Communifire domény a adresy URL jednotné přihlašování informace](./media/communifire-tutorial/tutorial_communifire_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.communifire.com`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.communifire.com/SAML/AssertionConsumerService.aspx`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Communifire domény a adresy URL jednotné přihlašování informace](./media/communifire-tutorial/tutorial_communifire_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.communifire.com/login`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [tým podpory Communifire klienta](https://my.axerosolutions.com/spaces/77/communifire-support/help/welcome) k získání těchto hodnot. 

5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/communifire-tutorial/tutorial_communifire_certificate.png) 

6.  Zkontrolujte **zobrazit upřesňující nastavení podpisového certifikátu** a vyberte **podepisování možnost** jako **odpověď přihlašování SAML a assertion**.

    ![Možnost certifikátů](./media/communifire-tutorial/tutorial_communifire_certificateoption.png) 

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/communifire-tutorial/tutorial_general_400.png)
    
8. Konfigurace jednotného přihlašování na **Communifire** straně, budete muset odeslat stažené **soubor XML s metadaty** k [tým podpory Communifire](https://my.axerosolutions.com/spaces/77/communifire-support/help/welcome). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/communifire-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/communifire-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/communifire-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/communifire-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-communifire-test-user"></a>Vytvoření zkušebního uživatele Communifire

Cílem této části je vytvoření uživatele v Communifire nazývá Britta Simon. Communifire podporuje za běhu zřizování, který je ve výchozím nastavení povolené. Nový uživatel je vytvořen po uložení Podrobnosti profilu během pokusu o přístup k Communifire, pokud ještě neexistuje.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory Communifire](https://my.axerosolutions.com/spaces/77/communifire-support/help/welcome).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Communifire.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Communifire, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Communifire**.

    ![V seznamu aplikací na Communifire odkaz](./media/communifire-tutorial/tutorial_communifire_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Communifire na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Communifire.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/communifire-tutorial/tutorial_general_01.png
[2]: ./media/communifire-tutorial/tutorial_general_02.png
[3]: ./media/communifire-tutorial/tutorial_general_03.png
[4]: ./media/communifire-tutorial/tutorial_general_04.png

[100]: ./media/communifire-tutorial/tutorial_general_100.png

[200]: ./media/communifire-tutorial/tutorial_general_200.png
[201]: ./media/communifire-tutorial/tutorial_general_201.png
[202]: ./media/communifire-tutorial/tutorial_general_202.png
[203]: ./media/communifire-tutorial/tutorial_general_203.png

