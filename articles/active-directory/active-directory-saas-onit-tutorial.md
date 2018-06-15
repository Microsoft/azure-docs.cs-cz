---
title: 'Kurz: Azure Active Directory integrace s Onit | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Onit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: bc479a28-8fcd-493f-ac53-681975a5149c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jeedes
ms.openlocfilehash: 6613f31b44485e48405bc2a055f8285733d803d2
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352187"
---
# <a name="tutorial-azure-active-directory-integration-with-onit"></a>Kurz: Azure Active Directory integrace s Onit

V tomto kurzu zjistěte, jak integrovat Onit s Azure Active Directory (Azure AD).

Integrace Onit s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Onit.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Onit (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Onit, potřebujete následující položky:

- Předplatné služby Azure AD
- Onit jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Onit z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-onit-from-the-gallery"></a>Přidání Onit z Galerie
Při konfiguraci integrace Onit do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Onit z galerie.

**Pokud chcete přidat Onit z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Onit**, vyberte **Onit** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Onit v seznamu výsledků](./media/active-directory-saas-onit-tutorial/tutorial_onit_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Onit podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Onit je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Onit musí navázat.

V Onit, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Onit, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovací uživatele s Onit](#create-an-onit-test-user)**  – Pokud chcete mít protějšek Britta Simon v Onit propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  ověřit, zda funguje konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Onit.

**Ke konfiguraci Azure AD jednotné přihlašování s Onit, proveďte následující kroky:**

1. Na portálu Azure na **Onit** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-onit-tutorial/tutorial_onit_samlbase.png)

3. Na **Onit domény a adresy URL** část, proveďte následující kroky:

    ![Onit domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-onit-tutorial/tutorial_onit_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<sub-domain>.onit.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<sub-domain>.onit.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory Onit klienta](https://www.onit.com/support) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** část, zkopírujte **kryptografický OTISK** hodnota certifikátu.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-onit-tutorial/tutorial_onit_certificate.png) 

5. Aplikace Onit očekává SAML kontrolní výrazy ve specifickém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z **"Atrribute"** aplikace. Následující snímek obrazovky ukazuje příklad pro tento. 

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-onit-tutorial/tutorial_onit_attribute.png) 

6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ------------------- | -------------------- |
    | e-mail | User.Mail |
    
    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-onit-tutorial/tutorial_attribute_04.png)

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-onit-tutorial/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d. Ponechte **Namespace** prázdné.
    
    e. Klikněte na tlačítko **OK**.

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-onit-tutorial/tutorial_general_400.png)

8. Na **Onit konfigurace** klikněte na tlačítko **konfigurace Onit** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace Onit](./media/active-directory-saas-onit-tutorial/tutorial_onit_configure.png)

9. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti Onit.

10. V nabídce v horní části, klikněte na tlačítko **správy**.
   
   ![Správa](./media/active-directory-saas-onit-tutorial/IC791174.png "správy")
11. Klikněte na tlačítko **úpravy Corporation**.
   
   ![Upravit Corporation](./media/active-directory-saas-onit-tutorial/IC791175.png "Corporation úpravy")
   
12. Klikněte **zabezpečení** kartě.
    
    ![Informace o společnosti upravit](./media/active-directory-saas-onit-tutorial/IC791176.png "informace společnosti úpravy")

13. Na **zabezpečení** kartu, proveďte následující kroky:

    ![Jednotné přihlašování](./media/active-directory-saas-onit-tutorial/IC791177.png "jednotného přihlašování")

    a. Jako **strategie ověřování**, vyberte **jednotné přihlašování a heslo**.
    
    b. V **Idp cílová adresa URL** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.

    c. V **adresy URL odhlašovací Idp** textovému poli, vložte hodnotu **Sign-Out URL**, který jste zkopírovali z portálu Azure.

    d. V **Idp Cert otisk (SHA1)** textovému poli, Vložit **kryptografický otisk** hodnota certifikát, který jste zkopírovali z portálu Azure.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-onit-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-onit-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-onit-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-onit-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-onit-test-user"></a>Vytvořit uživatele s Onit testu

Pokud chcete povolit uživatelům Azure AD přihlášení do Onit, musí být zřízená do Onit.  

V případě Onit zřizování je ruční úloha.

**Pokud chcete konfigurovat, zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **Onit** společnosti lokality jako správce.
2. Klikněte na tlačítko **přidat uživatele**.
   
   ![Správa](./media/active-directory-saas-onit-tutorial/IC791180.png "správy")
3. Na **přidat uživatele** dialogové okno stránky, proveďte následující kroky:
   
   ![Přidat uživatele](./media/active-directory-saas-onit-tutorial/IC791181.png "přidat uživatele")
   
  1. Typ **název** a **e-mailovou adresu** platný Azure AD účtu chcete mají být zahrnuty do související textových polí.
  2. Klikněte na možnost **Vytvořit**.    
   
 > [!NOTE]
 > Držitel účtu Azure Active Directory obdrží e-mailu a dodržuje odkaz potvrďte svůj účet, pak se změní na aktivní.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Onit.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Onit, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Onit**.

    ![V seznamu aplikací na Onit odkaz](./media/active-directory-saas-onit-tutorial/tutorial_onit_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Onit na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Onit.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-onit-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-onit-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-onit-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-onit-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-onit-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-onit-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-onit-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-onit-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-onit-tutorial/tutorial_general_203.png

