---
title: 'Kurz: Azure Active Directory integrace s Adobe přihlašovací | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Adobe přihlášení.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: jeedes
ms.openlocfilehash: c3b7e7178ef68475f331edf058ca0f23661af3ea
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34338869"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Kurz: Azure Active Directory integrace s Adobe přihlášení

V tomto kurzu zjistěte, jak integrovat Adobe přihlašovací službou Azure Active Directory (Azure AD).

Integrace Adobe přihlášení s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup na Adobe přihlašování
- Můžete povolit uživatelům, aby automaticky získat přihlášení k přihlášení Adobe (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD znakem Adobe, potřebujete následující položky:

- Předplatné služby Azure AD
- Přihlášení Adobe jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Adobe přihlášení z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-adobe-sign-from-the-gallery"></a>Přidání Adobe přihlášení z Galerie
Při konfiguraci Integrace Adobe registrace do služby Azure AD, potřebujete přidat přihlašovací Adobe z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat přihlašovací Adobe z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Adobe přihlašovací**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_search.png)

5. Na panelu výsledků vyberte **Adobe přihlašovací**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování znakem Adobe podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co příslušného uživatele v Adobe přihlašování je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Adobe přihlašovací musí navázat.

V Adobe přihlašovací přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Adobe přihlášení, budete muset provést následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření testovacího uživatele přihlášení Adobe](#creating-an-adobe-sign-test-user)**  – Pokud chcete mít protějšek Britta Simon v Adobe znak, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Adobe přihlášení.

**Ke konfiguraci Azure AD jednotné přihlašování znakem Adobe, proveďte následující kroky:**

1. Na portálu Azure na **Adobe přihlašovací** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_samlbase.png)

3. Na **Adobe přihlašovací domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.echosign.com/`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.echosign.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory Adobe přihlašovací klienta](https://helpx.adobe.com/in/contact/support.html) k získání těchto hodnot.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_400.png)

6. Na **Adobe přihlašovací konfigurace** klikněte na tlačítko **nakonfigurovat přihlašovací Adobe** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_configure.png)

7. Před konfigurací, budete muset kontaktovat [tým podpory Adobe přihlašovací klienta](https://helpx.adobe.com/in/contact/support.html) do seznamu povolených IP adres vaši doménu v Adobe přihlášení. Použijte následujících kroků pro přidání domény:

    a. [Tým podpory přihlašovací klienta Adobe](https://helpx.adobe.com/in/contact/support.html) vám pošle náhodně generované token. Pro vaši doménu, bude token jako: **adobe-sign ověření = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Je nutné publikovat tokenu ověření v záznamu DNS text a upozornit [tým podpory Adobe přihlašovací klienta](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Můžete očekávat, může to trvat několik dnů nebo možná nebude. Všimněte si, že zpoždění šíření DNS znamená, že hodnota publikovaná ve službě DNS, nemusí být zobrazeny pro hodinu nebo déle. Očekáváme, že váš správce IT by měl být seznámeni o tom, jak publikovat tento token v záznamu DNS text.
    
    c. Jakmile můžete upozornit [tým podpory Adobe přihlašovací klienta](https://helpx.adobe.com/in/contact/support.html) prostřednictvím lístku podpory po token je publikována, budou ověřit doménu a přidejte ke svému účtu.
    
    d. Obecné kroky, které můžete použít k publikování token v záznamu DNS-

    * Přihlaste se k účtu domény
    * Najděte stránku pro aktualizaci záznamu DNS. Tato stránka může být volána správu DNS, Správa název serveru nebo Upřesnit nastavení.
    * Vyhledání záznamů TXT pro vaši doménu.
    * Přidejte záznam TXT s hodnotou úplné tokenu poskytl Adobe
    * Uložte provedené změny.

8. V okně prohlížeče jiný web Přihlaste se na váš web společnosti Adobe přihlášení jako správce.

9. V nabídce SAML, klikněte na tlačítko **nastavení účtu**a potom klikněte na **SAML nastavení**.
   
    ![Účet](./media/active-directory-saas-adobe-echosign-tutorial/ic789520.png "účtu")

10. V **SAML nastavení** část, proveďte následující kroky:
  
    ![Nastavení SAML](./media/active-directory-saas-adobe-echosign-tutorial/ic789521.png "nastavení SAML")
   
    a. Jako **SAML režimu**, vyberte **SAML povinné**.
   
    b. Vyberte **povolit Adobe přihlašovací účet správci přihlásit pomocí svých přihlašovacích údajů přihlašovací Adobe**.
   
    c. Jako **vytvoření uživatele**, vyberte **automaticky přidat uživatele ověřeného pomocí SAML**.

    d. Vložení **SAML Entity ID**, který jste zkopírovali z portálu Azure do **Entity ID-Vystavitel URL** textové pole.
    
    e. Vložení **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure do **koncový bod adresy URL/jednotného přihlášení** textové pole.
   
    f. Vložení **Sign-Out URL**, který jste zkopírovali z portálu Azure do **koncový bod adresy URL/SLO odhlášení** textové pole.

    g. Otevřete váš stažené **Certificate(Base64)** souboru v programu Poznámkový blok, zkopírujte obsah ho do schránky a vložte jej do **IdP certifikát** textbox

    h. Klikněte na tlačítko **uložit změny**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-adobe-sign-test-user"></a>Vytváření testovacího uživatele přihlášení Adobe

Pokud chcete povolit uživatelům Azure AD přihlášení na Adobe přihlašování, musí být zřízená do Adobe přihlášení. V případě Adobe přihlašovací zřizování je ruční úloha.

>[!NOTE]
>Můžete použít jakékoli jiné Adobe přihlášení uživatele účtu vytvoření nástroje nebo rozhraní API poskytované Adobe přihlašovací zřídit AAD uživatelské účty. 

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k vaší **Adobe přihlašovací** společnosti lokality jako správce.

2. V nabídce v horní části, klikněte na tlačítko **účet**a klikněte v navigačním podokně na levé straně, **uživatelé a skupiny**a potom klikněte na **vytvořte nového uživatele**.
   
    ![Účet](./media/active-directory-saas-adobe-echosign-tutorial/ic789524.png "účtu")
   
3. V **vytvořit nového uživatele** část, proveďte následující kroky:
   
    ![Vytvoření uživatele](./media/active-directory-saas-adobe-echosign-tutorial/ic789525.png "vytvoření uživatele")
   
    a. Typ **e-mailovou adresu**, **křestní jméno**, a **příjmení** platného účtu AAD chcete mají být zahrnuty do související textových polí.
   
    b. Klikněte na tlačítko **vytvořit uživatele**.

>[!NOTE]
>Držitel účtu Azure Active Directory obdrží e-mail, který obsahuje odkaz pro potvrzení účtu před stane aktivní. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Adobe přihlášení.

![Přiřadit uživatele][200] 

**Přiřadit Britta Simon na Adobe přihlašování, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Adobe přihlašovací**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Když kliknete na dlaždici Adobe přihlašovací na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Adobe přihlášení.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_203.png
