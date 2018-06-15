---
title: 'Kurz: Azure Active Directory integrace s Evernote | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Evernote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 6926472cad5e124ec3e8636df057044b792827c6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34342330"
---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>Kurz: Azure Active Directory integrace s Evernote

V tomto kurzu zjistěte, jak integrovat Evernote s Azure Active Directory (Azure AD).

Integrace Evernote s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Evernote.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Evernote (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Evernote, potřebujete následující položky:

- Předplatné služby Azure AD
- Evernote jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Evernote z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-evernote-from-the-gallery"></a>Přidání Evernote z Galerie
Při konfiguraci integrace Evernote do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Evernote z galerie.

**Pokud chcete přidat Evernote z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Evernote**, vyberte **Evernote** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Evernote v seznamu výsledků](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Evernote podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Evernote je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Evernote musí navázat.

V Evernote, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Evernote, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovací uživatele s Evernote](#create-an-evernote-test-user)**  – Pokud chcete mít protějšek Britta Simon v Evernote propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Evernote.

**Ke konfiguraci Azure AD jednotné přihlašování s Evernote, proveďte následující kroky:**

1. Na portálu Azure na **Evernote** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_samlbase.png)

3. Na **Evernote domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v režimu rozšíření IDP iniciované:

    ![Evernote domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_url.png)

    V **identifikátor** textovému poli, zadejte adresu URL: `https://www.evernote.com/saml2`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Evernote domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_url1.png)

    V **přihlásit na adrese URL** textovému poli, zadejte adresu URL: `https://www.evernote.com/Login.action`   

5. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-evernote-tutorial/tutorial_general_400.png)

7. Na **Evernote konfigurace** klikněte na tlačítko **konfigurace Evernote** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace Evernote](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_configure.png) 

8. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti Evernote.

9. Přejděte na **'konzoly pro správu.**

    ![Konzole pro správu](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_adminconsole.png)

10. Z **'Konzoly pro správu'**, přejděte na **, zabezpečení,** a vyberte **' jednotné přihlašování.**

    ![Nastavení jednotného přihlašování](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_sso.png)

11. Nakonfigurujte následující hodnoty:

    ![Nastavení certifikátu](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Povolit jednotné přihlašování:** je ve výchozím nastavení povolené jednotné přihlašování (klikněte na tlačítko **zakázat jednotné přihlašování** odebrat požadavek na jednotné přihlašování)

    b. Vložení **SAML jednotné přihlašování adresa URL služby** hodnotu, kterou jste zkopírovali z portálu Azure do **URL požadavku HTTP SAML** textové pole.

    c. Otevřete certifikát stažený z Azure AD v programu Poznámkový blok a zkopírujte obsah, včetně "Začít certifikát" a "END CERTIFICATE" a vložte ji do **certifikát X.509** textové pole. 

    d.Click **uložit změny**

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-evernote-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-evernote-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-evernote-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-evernote-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-evernote-test-user"></a>Vytvořit uživatele s Evernote testu

Pokud chcete povolit uživatelům Azure AD přihlášení do Evernote, musí být zřízená do Evernote.  
V případě Evernote zřizování je ruční úloha.

**Ke zřízení uživatelských účtů, proveďte následující kroky:**

1. Přihlaste se k serveru vaší společnosti Evernote jako správce.

2. Klikněte **'Konzoly pro správu'**.

    ![Konzole pro správu](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_adminconsole.png)

3. Z **'Konzoly pro správu'**, přejděte na **"Přidat uživatele,**.

    ![Přidat testUser](./media/active-directory-saas-evernote-tutorial/create_aaduser_0001.png)

4. **Přidání členů týmu** v **e-mailu** textovému poli, zadejte e-mailovou adresu uživatelského účtu a klikněte na **pozvat.**

    ![Přidat testUser](./media/active-directory-saas-evernote-tutorial/create_aaduser_0002.png)
    
5. Po odeslání pozvánky držitel účtu Azure Active Directory obdrží e-mailu pro přijetí pozvánky.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Evernote.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Evernote, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Evernote**.

    ![V seznamu aplikací na Evernote odkaz](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Evernote na přístupovém panelu, jste měli získat přihlášení k aplikaci Evernote. Se budete přihlašovat jako účet, ale pak nutné se přihlásit pomocí svého osobního účtu organizace. 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_203.png

