---
title: 'Kurz: Azure Active Directory integrace s New Relic. | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a New Relic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: jeedes
ms.openlocfilehash: fab53b33f845c277cc9b227141222c468529a75e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Kurz: Azure Active Directory integrace s New Relic.

V tomto kurzu zjistěte, jak integrovat New Relic s Azure Active Directory (Azure AD).

Integrace New Relic s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k New Relic.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k New Relic (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s New Relic, potřebujete následující položky:

- Předplatné služby Azure AD
- New Relic jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání New Relic z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-new-relic-from-the-gallery"></a>Přidání New Relic z Galerie
Při konfiguraci integrace New Relic do služby Azure AD potřebujete přidat New Relic z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat New Relic z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **New Relic**, vyberte **New Relic** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![V seznamu výsledků New Relic.](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s New Relic podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v New Relic je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah propojení mezi uživatele Azure AD a související uživatelské v New Relic.

V New Relic, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s New Relic, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele New Relic](#create-a-new-relic-test-user)**  – Pokud chcete mít protějšek Britta Simon v New Relic, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci New Relic.

**Ke konfiguraci Azure AD jednotné přihlašování s New Relic, proveďte následující kroky:**

1. Na portálu Azure na **New Relic** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_samlbase.png)

3. Na **nové domény Relic a adresy URL** část, proveďte následující kroky:

    ![Nové domény Relic a adresy URL jednotné přihlašování informace](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` -nezapomeňte nahradit vlastní ID nový účet New Relic.

    b. V **identifikátor** textovému poli, zadejte hodnotu: `rpm.newrelic.com`

4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-new-relic-tutorial/tutorial_general_400.png)

6. Na **novou konfiguraci Relic** klikněte na tlačítko **konfigurace New Relic** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL a SAML jeden přihlašování služby URL** z **Stručná referenční příručka části.**

    ![Nová konfigurace Relic](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_configure.png) 

7. V okně prohlížeče jiný web, přihlaste se k vaší **New Relic** společnosti lokality jako správce.

8. V nabídce v horní části, klikněte na tlačítko **nastavení účtu**.
   
    ![Nastavení účtu](./media/active-directory-saas-new-relic-tutorial/ic797036.png "nastavení účtu")

9. Klikněte na tlačítko **zabezpečení a ověřování** a pak klikněte **jednotné přihlašování** kartě.
   
    ![Jednotné přihlašování](./media/active-directory-saas-new-relic-tutorial/ic797037.png "jednotného přihlašování")

10. Na stránce dialogové okno SAML proveďte následující kroky:
   
    ![SAML](./media/active-directory-saas-new-relic-tutorial/ic797038.png "SAML")
   
   a. Klikněte na tlačítko **zvolit soubor** odeslání vašeho certifikátu stažené Azure Active Directory.

   b. V **adresu URL pro vzdálené přihlášení** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.
   
   c. V **cílová stránka adresy URL odhlašovací** textovému poli, vložte hodnotu **Sign-Out URL**, který jste zkopírovali z portálu Azure.

   d. Klikněte na tlačítko **uložit změny provedené v**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-new-relic-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-new-relic-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-new-relic-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-new-relic-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-new-relic-test-user"></a>Vytvoření zkušebního uživatele New Relic.

Pokud chcete povolit uživatelů Azure Active Directory k přihlášení do New Relic, musí být zřízená do New Relic. V případě New Relic zřizování je ruční úloha.

**Pokud chcete zřídit uživatelský účet New Relic, proveďte následující kroky:**

1. Přihlaste se k vaší **New Relic** společnosti lokality jako správce.

2. V nabídce v horní části, klikněte na tlačítko **nastavení účtu**.
   
    ![Nastavení účtu](./media/active-directory-saas-new-relic-tutorial/ic797040.png "nastavení účtu")

3. V **účet** podokna na levé straně klikněte na tlačítko **Souhrn**a potom klikněte na **přidat uživatele**.
   
    ![Nastavení účtu](./media/active-directory-saas-new-relic-tutorial/ic797041.png "nastavení účtu")

4. Na **aktivní uživatelé** dialogové okno, proveďte následující kroky:
   
    ![Aktivní uživatelé](./media/active-directory-saas-new-relic-tutorial/ic797042.png "aktivní uživatelé")
   
    a. V **e-mailu** textovému poli, zadejte e-mailovou adresu chcete zřídit platného uživatele Azure Active Directory.

    b. Jako **Role** vyberte **uživatele**.

    c. Klikněte na tlačítko **přidejte tohoto uživatele**.

>[!NOTE]
>Můžete použít všechny ostatní New Relic uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované New Relic zřídit AAD uživatelské účty.
> 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu New Relic.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon New Relic, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **New Relic**.

    ![V seznamu aplikací na odkaz New Relic.](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici New Relic na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci New Relic.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_203.png

