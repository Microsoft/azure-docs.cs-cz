---
title: 'Kurz: Azure Active Directory integrace s Freshservice | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Freshservice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: a9e50b43308754dae78b0f56c19a539939d19676
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-freshservice"></a>Kurz: Azure Active Directory integrace s Freshservice

V tomto kurzu zjistěte, jak integrovat Freshservice s Azure Active Directory (Azure AD).

Integrace Freshservice s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Freshservice
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Freshservice (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Freshservice, potřebujete následující položky:

- Předplatné služby Azure AD
- Freshservice jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Freshservice z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-freshservice-from-the-gallery"></a>Přidání Freshservice z Galerie
Při konfiguraci integrace Freshservice do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Freshservice z galerie.

**Pokud chcete přidat Freshservice z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Freshservice**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_search.png)

5. Na panelu výsledků vyberte **Freshservice**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Freshservice podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Freshservice je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Freshservice musí navázat.

V Freshservice, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Freshservice, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Freshservice](#creating-a-freshservice-test-user)**  – Pokud chcete mít protějšek Britta Simon v Freshservice propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Freshservice.

**Ke konfiguraci Azure AD jednotné přihlašování s Freshservice, proveďte následující kroky:**

1. Na portálu Azure na **Freshservice** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_samlbase.png)

3. Na **Freshservice domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<democompany>.freshservice.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<democompany>.freshservice.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory Freshservice klienta](https://support.freshservice.com/) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** část, zkopírujte **kryptografický OTISK** hodnota certifikátu.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-freshservice-tutorial/tutorial_general_400.png)

6. Na **Freshservice konfigurace** klikněte na tlačítko **konfigurace Freshservice** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL a SAML jeden přihlašování služby URL** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti Freshservice jako správce.

8. V nabídce v horní části, klikněte na tlačítko **správce**.
   
    ![Správce](./media/active-directory-saas-freshservice-tutorial/ic790814.png "správce")

9. V **zákaznický portál**, klikněte na tlačítko **zabezpečení**.
   
    ![Zabezpečení](./media/active-directory-saas-freshservice-tutorial/ic790815.png "zabezpečení")

10. V **zabezpečení** část, proveďte následující kroky:
   
    ![Jednotné přihlašování](./media/active-directory-saas-freshservice-tutorial/ic790816.png "jednotné přihlašování")
   
    a. Přepínač **jednotné přihlašování**.

    b. Vyberte **jednotné přihlašování SAML**.

    c. V **SAML přihlašovací adresa URL** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.

    d. V **adresy URL odhlašovací** textovému poli, vložte hodnotu **Sign-Out URL** který jste zkopírovali z portálu Azure.

    e. V **otisků certifikátu zabezpečení** textovému poli, Vložit **kryptografický OTISK** hodnota certifikát, který jste zkopírovali z portálu Azure.

    f. Klikněte na **Uložit**.
   
> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-freshservice-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-freshservice-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-freshservice-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-freshservice-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-freshservice-test-user"></a>Vytvoření zkušebního uživatele Freshservice

Pokud chcete povolit uživatelům Azure AD přihlášení k FreshService, musí být zřízená do FreshService. V případě FreshService zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k vaší **FreshService** společnosti lokality jako správce.

2. V nabídce v horní části, klikněte na tlačítko **správce**.
   
    ![Správce](./media/active-directory-saas-freshservice-tutorial/ic790814.png "správce")

3. V **Správa uživatelů** klikněte na tlačítko **žadatelů o**.
   
    ![Žadatelů o](./media/active-directory-saas-freshservice-tutorial/ic790818.png "žadatelů o")

4. Klikněte na tlačítko **nový žadatel**.
   
    ![Nové žadatelů o](./media/active-directory-saas-freshservice-tutorial/ic790819.png "nové žadatelů o")

5. V **nový žadatel** část, proveďte následující kroky:
   
    ![Nový žadatel](./media/active-directory-saas-freshservice-tutorial/ic790820.png "nový žadatel")   

    a. Zadejte **křestní jméno** a **e-mailu** atributy platný účet služby Azure Active Directory chcete mají být zahrnuty do související textových polí.

    b. Klikněte na **Uložit**.
   
    >[!NOTE]
    >Držitel účtu Azure Active Directory získá zahrnutím odkazu pro potvrzení účtu před stane aktivní e-mailu
    >  

>[!NOTE]
>Můžete použít všechny ostatní FreshService uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované FreshService zřídit AAD uživatelské účty.
>  

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Freshservice, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Freshservice**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Freshservice na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Freshservice.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_203.png

