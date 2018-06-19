---
title: 'Kurz: Azure Active Directory integrace s KnowBe4 zabezpečení sledování školení | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a KnowBe4 zabezpečení sledování školení.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: b80d2212-cc5f-4adb-836c-570640810c39
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: jeedes
ms.openlocfilehash: d02ee99ec425a5a4cec75f460cfaa4698655a368
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35913480"
---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>Kurz: Azure Active Directory integrace s KnowBe4 zabezpečení sledování školení

V tomto kurzu zjistěte, jak integrovat KnowBe4 zabezpečení sledování školení s Azure Active Directory (Azure AD).

Integrace KnowBe4 zabezpečení sledování školení s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k KnowBe4 zabezpečení sledování školení.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k KnowBe4 bezpečnostního školení sledování (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD se školením sledování KnowBe4 zabezpečení, potřebujete následující položky:

- Předplatné služby Azure AD
- Školení sledování zabezpečení KnowBe4 jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání KnowBe4 zabezpečení sledování školení z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-knowbe4-security-awareness-training-from-the-gallery"></a>Přidání KnowBe4 zabezpečení sledování školení z Galerie
Při konfiguraci integrace KnowBe4 zabezpečení sledování školení do služby Azure AD, potřebujete přidat školení sledování zabezpečení KnowBe4 z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat KnowBe4 zabezpečení sledování školení z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **KnowBe4 zabezpečení sledování školení**, vyberte **KnowBe4 zabezpečení sledování školení** z panelu výsledků klikněte **přidat** tlačítko přidáte aplikace.

    ![KnowBe4 bezpečnostního školení sledování v seznamu výsledků](./media/knowbe4-tutorial/tutorial_knowbe4_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s KnowBe4 zabezpečení sledování školení podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co příslušného uživatele v školení sledování KnowBe4 zabezpečení je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v KnowBe4 zabezpečení sledování školení musí navázat.

V KnowBe4 zabezpečení sledování školení, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování se školením sledování KnowBe4 zabezpečení, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele školení sledování zabezpečení KnowBe4](#create-a-knowbe4-security-awareness-training-test-user)**  – Pokud chcete mít protějšek Britta Simon v KnowBe4 zabezpečení sledování školení propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci KnowBe4 zabezpečení sledování školení.

**Ke konfiguraci Azure AD jednotné přihlašování se školením sledování zabezpečení KnowBe4, proveďte následující kroky:**

1. Na portálu Azure na **KnowBe4 zabezpečení sledování školení** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/knowbe4-tutorial/tutorial_knowbe4_samlbase.png)

3. Na **KnowBe4 zabezpečení sledování školení domény a adresy URL** část, proveďte následující kroky:

    ![KnowBe4 zabezpečení sledování školení domény a adresy URL jednotné přihlašování informace](./media/knowbe4-tutorial/tutorial_knowbe4_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.KnowBe4.com/auth/saml/<instancename>`

    > [!NOTE] 
    > Přihlášení hodnota adresy URL není skutečné. Aktualizujte tuto hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory KnowBe4 zabezpečení sledování školení klienta](mailto:support@KnowBe4.com) získat tuto hodnotu. 

    b. V **identifikátor** textovému poli, zadejte hodnotu řetězce: `KnowBe4`

    > [!NOTE]
    >Toto je malá a velká písmena

4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Raw)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/knowbe4-tutorial/tutorial_knowbe4_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/knowbe4-tutorial/tutorial_general_400.png)

6. Na **konfigurace školení sledování zabezpečení KnowBe4** klikněte na tlačítko **konfigurace KnowBe4 zabezpečení sledování školení** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace školení sledování KnowBe4 zabezpečení](./media/knowbe4-tutorial/tutorial_knowbe4_configure.png) 

7. Konfigurace jednotného přihlašování na **KnowBe4 zabezpečení sledování školení** straně, budete muset odeslat stažené **certifikátu (Raw)**, **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** k [tým podpory KnowBe4 zabezpečení sledování školení klienta](mailto:support@KnowBe4.com).

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/knowbe4-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/knowbe4-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/knowbe4-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/knowbe4-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-knowbe4-security-awareness-training-test-user"></a>Vytvoření zkušebního uživatele KnowBe4 zabezpečení sledování školení

Cílem této části je vytvoření uživatele volal Britta Simon v KnowBe4 zabezpečení sledování školení. Školení sledování zabezpečení KnowBe4 podporuje za běhu zřizování, který je ve výchozím nastavení povolené.

Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k KnowBe4 zabezpečení sledování školení, pokud ještě neexistuje. 

>[!NOTE]
>Pokud potřebujete ručně vytvořit uživatele, budete muset kontaktovat [tým podpory školení sledování zabezpečení KnowBe4](mailto:support@KnowBe4.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu KnowBe4 zabezpečení sledování školení.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon KnowBe4 zabezpečení sledování školení, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **KnowBe4 zabezpečení sledování školení**.

    ![Odkaz KnowBe4 zabezpečení sledování školení v seznamu aplikací](./media/knowbe4-tutorial/tutorial_knowbe4_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.
  
Když kliknete na dlaždici KnowBe4 zabezpečení sledování školení na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci KnowBe4 zabezpečení sledování školení. 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/knowbe4-tutorial/tutorial_general_01.png
[2]: ./media/knowbe4-tutorial/tutorial_general_02.png
[3]: ./media/knowbe4-tutorial/tutorial_general_03.png
[4]: ./media/knowbe4-tutorial/tutorial_general_04.png

[100]: ./media/knowbe4-tutorial/tutorial_general_100.png

[200]: ./media/knowbe4-tutorial/tutorial_general_200.png
[201]: ./media/knowbe4-tutorial/tutorial_general_201.png
[202]: ./media/knowbe4-tutorial/tutorial_general_202.png
[203]: ./media/knowbe4-tutorial/tutorial_general_203.png

