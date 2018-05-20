---
title: 'Kurz: Azure Active Directory integrace s Zoho | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zoho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9874e1f3-ade5-42e7-a700-e08b3731236a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jeedes
ms.openlocfilehash: d332dd65d660106a9f296ea57f89379f07ced027
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Kurz: Azure Active Directory integrace s Zoho

V tomto kurzu zjistěte, jak integrovat Zoho s Azure Active Directory (Azure AD).

Integrace Zoho s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Zoho.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Zoho (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Zoho, potřebujete následující položky:

- Předplatné služby Azure AD
- Zoho jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Zoho z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-zoho-from-the-gallery"></a>Přidání Zoho z Galerie
Při konfiguraci integrace Zoho do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Zoho z galerie.

**Pokud chcete přidat Zoho z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Zoho**, vyberte **Zoho** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Zoho v seznamu výsledků](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Zoho podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Zoho je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Zoho musí navázat.

V Zoho, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Zoho, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Zoho](#create-a-zoho-test-user)**  – Pokud chcete mít protějšek Britta Simon v Zoho propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Zoho.

**Ke konfiguraci Azure AD jednotné přihlašování s Zoho, proveďte následující kroky:**

1. Na portálu Azure na **Zoho** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_samlbase.png)

3. Na **Zoho domény a adresy URL** část, proveďte následující kroky:

    ![Zoho domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<company name>.zohomail.com`

    > [!NOTE] 
    > Tato hodnota není skutečné. Aktualizujte tuto hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory Zoho klienta](https://www.zoho.com/mail/contact.html) získat tuto hodnotu. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_400.png)

6. Na **Zoho konfigurace** klikněte na tlačítko **konfigurace Zoho** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, adresy URL pro změnu hesla a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace Zoho](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti Zoho e-mailu.

8. Přejděte na **ovládací panely**.
   
    ![Ovládací panely](./media/active-directory-saas-zoho-mail-tutorial/ic789607.png "ovládací panely")

9. Klikněte **ověřování SAML** kartě.
   
    ![Ověřování SAML](./media/active-directory-saas-zoho-mail-tutorial/ic789608.png "ověřování SAML")

10. V **podrobnosti ověřování SAML** část, proveďte následující kroky:
   
    ![Podrobnosti o ověřování SAML](./media/active-directory-saas-zoho-mail-tutorial/ic789609.png "podrobnosti ověřování SAML")
   
    a. V **přihlašovací adresa URL** textovému poli, vložte **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.
   
    b. V **adresy URL odhlašovací** textovému poli, vložte **Sign-Out URL** který jste zkopírovali z portálu Azure.
   
    c. V **heslo změnit adresu URL** textovému poli, vložte **heslo změnit adresu URL** který jste zkopírovali z portálu Azure.
       
    d. Otevření kódovaného certifikátu kódování base-64 stáhli z portálu Azure v programu Poznámkový blok, zkopírujte obsah ho do schránky a vložte jej do **PublicKey** textové pole.
   
    e. Jako **algoritmus**, vyberte **RSA**.
   
    f. Klikněte na **OK**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-zoho-mail-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-zoho-mail-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-zoho-mail-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-zoho-mail-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-zoho-test-user"></a>Vytvoření zkušebního uživatele Zoho

Pokud chcete povolit uživatelům Azure AD Přihlaste se k e-mailu Zoho, musí být zřízená do Zoho e-mailu. V případě Zoho pošta zřizování je ruční úloha.

> [!NOTE]
> Můžete použít jakékoli jiné e-mailu Zoho uživatele účtu vytvoření nástroje nebo rozhraní API poskytované e-mailu Zoho zřídit AAD uživatelské účty.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>K poskytnutí uživatelského účtu, proveďte následující kroky:

1. Přihlaste se k vaší **Zoho e-mailu** společnosti lokality jako správce.

2. Přejděte na **ovládací panely \> e-mailu a dokumentů**.

3. Přejděte na **podrobné informace o uživateli \> přidat uživatele**.
   
    ![Přidat uživatele](./media/active-directory-saas-zoho-mail-tutorial/ic789611.png "přidat uživatele")

4. Na **přidat uživatele** dialogové okno, proveďte následující kroky:
   
    ![Přidat uživatele](./media/active-directory-saas-zoho-mail-tutorial/ic789612.png "přidat uživatele")
   
    a. V **křestní jméno** jako typ křestní jméno uživatele k textovému poli, **Britta**.

    b. V **příjmení** jako typ příjmení uživatele k textovému poli, **Simon**.

    c. V **ID e-mailu** jako typ e-mailu id uživatele k textovému poli, **brittasimon@contoso.com**.

    d. V **heslo** textovému poli, zadejte heslo uživatele.
   
    e. Klikněte na **OK**.  
      
    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mail s odkazem pro potvrzení účtu před stane aktivní.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Zoho.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Zoho, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Zoho**.

    ![V seznamu aplikací na Zoho odkaz](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Zoho na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Zoho.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_203.png

