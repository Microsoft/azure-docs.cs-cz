---
title: 'Kurz: Azure Active Directory integrace s Software pro správu ochrany osobních údajů OneTrust | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Software pro správu OneTrust ochrany osobních údajů.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 71c2b6d0-3d28-4130-a2c8-1e72ab3d5814
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: jeedes
ms.openlocfilehash: 744211174440b4bb60700f6d6d71cac289f7b56e
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36230297"
---
# <a name="tutorial-azure-active-directory-integration-with-onetrust-privacy-management-software"></a>Kurz: Azure Active Directory integrace s Software pro správu OneTrust ochrany osobních údajů

V tomto kurzu zjistěte, jak integrovat Software pro správu OneTrust ochrany osobních údajů v Azure Active Directory (Azure AD).

Software pro správu ochrany osobních údajů OneTrust integraci s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k softwaru pro správu OneTrust ochrany osobních údajů.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k OneTrust Software pro správu ochrany osobních údajů (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Software pro správu OneTrust ochrany osobních údajů, potřebujete následující položky:

- Předplatné služby Azure AD
- Software pro správu ochrany osobních údajů OneTrust jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Software pro správu ochrany osobních údajů OneTrust z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-onetrust-privacy-management-software-from-the-gallery"></a>Přidání Software pro správu ochrany osobních údajů OneTrust z Galerie
Při konfiguraci integrace Software pro správu OneTrust ochrany osobních údajů do služby Azure AD potřebujete přidat Software pro správu ochrany osobních údajů OneTrust z Galerie si na seznam spravovaných aplikací SaaS.

**Přidat Software pro správu ochrany osobních údajů OneTrust z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Software pro správu ochrany osobních údajů OneTrust**, vyberte **Software pro správu ochrany osobních údajů OneTrust** z panelu výsledků klikněte **přidat** tlačítko přidáte aplikace.

    ![Software pro správu OneTrust ochrany osobních údajů v seznamu výsledků](./media/onetrust-tutorial/tutorial_onetrust_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s podle testovacího uživatele názvem "Britta Simon" Software pro správu OneTrust ochrany osobních údajů.

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Software pro správu OneTrust ochrany osobních údajů je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Software pro správu ochrany osobních údajů OneTrust musí navázat.

V OneTrust Software pro správu ochrany osobních údajů, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Software pro správu OneTrust ochrany osobních údajů, budete muset provést následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Software pro správu ochrany osobních údajů OneTrust](#create-a-onetrust-privacy-management-software-test-user)**  – Pokud chcete mít protějšek Britta Simon v Software pro správu ochrany osobních údajů OneTrust propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Software pro správu OneTrust ochrany osobních údajů.

**Chcete-li nakonfigurovat Software pro správu OneTrust ochrany osobních údajů služby Azure AD jednotné přihlašování, proveďte následující kroky:**

1. Na portálu Azure na **Software pro správu ochrany osobních údajů OneTrust** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/onetrust-tutorial/tutorial_onetrust_samlbase.png)

3. Na **doméně pro správu softwaru OneTrust ochrany osobních údajů a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Doména Software správy OneTrust ochrany osobních údajů a adresy URL jednotné přihlašování informace](./media/onetrust-tutorial/tutorial_onetrust_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL: `https://www.onetrust.com/saml2`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.onetrust.com/auth/consumerservice`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Doména Software správy OneTrust ochrany osobních údajů a adresy URL jednotné přihlašování informace](./media/onetrust-tutorial/tutorial_onetrust_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.onetrust.com/auth/login`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečná adresa URL odpovědi a adresa URL přihlašování. Obraťte se na [tým podpory klientský Software správy ochrany osobních údajů OneTrust](mailto:support@onetrust.com) k získání těchto hodnot. 

5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/onetrust-tutorial/tutorial_onetrust_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/onetrust-tutorial/tutorial_general_400.png)

7. Konfigurace jednotného přihlašování na **Software pro správu ochrany osobních údajů OneTrust** straně, budete muset odeslat stažené **soubor XML s metadaty** k [tým podpory Software pro správu ochrany osobních údajů OneTrust](mailto:support@onetrust.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/onetrust-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/onetrust-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/onetrust-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/onetrust-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-onetrust-privacy-management-software-test-user"></a>Vytvoření zkušebního uživatele Software pro správu OneTrust ochrany osobních údajů

Cílem této části je vytvoření uživatele volal Britta Simon v Software pro správu OneTrust ochrany osobních údajů. Software pro správu ochrany osobních údajů OneTrust podporuje za běhu zřizování, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k softwaru pro správu OneTrust ochrany osobních údajů, pokud ještě neexistuje.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory pro Software pro správu ochrany osobních údajů OneTrust](mailto:support@onetrust.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Software pro správu OneTrust ochrany osobních údajů.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Software pro správu OneTrust ochrany osobních údajů, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Software pro správu ochrany osobních údajů OneTrust**.

    ![Software pro správu ochrany osobních údajů OneTrust odkaz v seznamu aplikací](./media/onetrust-tutorial/tutorial_onetrust_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Software pro správu OneTrust ochrany osobních údajů na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Software pro správu OneTrust ochrany osobních údajů.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/onetrust-tutorial/tutorial_general_01.png
[2]: ./media/onetrust-tutorial/tutorial_general_02.png
[3]: ./media/onetrust-tutorial/tutorial_general_03.png
[4]: ./media/onetrust-tutorial/tutorial_general_04.png

[100]: ./media/onetrust-tutorial/tutorial_general_100.png

[200]: ./media/onetrust-tutorial/tutorial_general_200.png
[201]: ./media/onetrust-tutorial/tutorial_general_201.png
[202]: ./media/onetrust-tutorial/tutorial_general_202.png
[203]: ./media/onetrust-tutorial/tutorial_general_203.png

