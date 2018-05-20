---
title: 'Kurz: Azure Active Directory integrace s Igloo softwarem | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Igloo softwarem a Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 17ccb7fdb765462b8753be26ca823fd68160af34
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Kurz: Azure Active Directory integrace s Igloo softwaru

V tomto kurzu zjistěte, jak integrovat Igloo softwaru s Azure Active Directory (Azure AD).

Integrace Igloo softwaru s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k softwaru Igloo
- Můžete povolit uživatelům, aby automaticky získat přihlášení k softwaru Igloo (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Igloo softwaru, potřebujete následující položky:

- Předplatné služby Azure AD
- Softwaru Igloo jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání softwaru Igloo z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-igloo-software-from-the-gallery"></a>Přidání softwaru Igloo z Galerie
Při konfiguraci integrace Igloo softwaru do služby Azure AD, musíte přidat Igloo Software z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Igloo softwaru z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Igloo softwaru**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_search.png)

5. Na panelu výsledků vyberte **Igloo softwaru**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Igloo Software založený na testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Igloo softwaru je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Igloo softwaru je nutné stanovit.

V softwaru Igloo přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Igloo softwaru, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření testovacího uživatele softwaru Igloo](#creating-an-igloo-software-test-user)**  – Pokud chcete mít protějšek Britta Simon v Igloo Software, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Igloo softwaru.

**Ke konfiguraci Azure AD jednotné přihlašování s Igloo softwarem, proveďte následující kroky:**

1. Na portálu Azure na **Igloo softwaru** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_samlbase.png)

3. Na **Igloo softwaru domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_url.png)
    
    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<company name>.igloocommmunities.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<company name>.igloocommmunities.com/saml.digest`

    c. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [tým podpory klientský Software Igloo](https://www.igloosoftware.com/services/support) k získání těchto hodnot. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-igloo-software-tutorial/tutorial_general_400.png)
    
6. Na **Igloo softwarové konfigurace** klikněte na tlačítko **konfigurace softwaru Igloo** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL a SAML jeden přihlašování služby URL** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se na váš web společnosti Igloo softwaru jako správce.

8. Přejděte na **ovládací panely**.
   
     ![Ovládací panely](./media/active-directory-saas-igloo-software-tutorial/ic799949.png "ovládací panely")

9. V **členství** , klikněte na **přihlášení v nastavení**.
   
    ![Přihlaste se nastavení](./media/active-directory-saas-igloo-software-tutorial/ic783968.png "přihlášení nastavení")

10. V části Konfigurace SAML, klikněte na tlačítko **konfigurace ověřování SAML**.
   
    ![Konfigurace SAML](./media/active-directory-saas-igloo-software-tutorial/ic783969.png "konfigurace SAML")
   
11. V **obecné konfigurace** část, proveďte následující kroky:
   
    ![Obecná konfigurace](./media/active-directory-saas-igloo-software-tutorial/ic783970.png "obecné konfigurace")

    a. V **název připojení** textovému poli, napište vlastní název pro svou konfiguraci.
   
    b. V **IdP přihlašovací adresa URL** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.
   
    c. V **adresy URL odhlašovací IdP** textovému poli, vložte hodnotu **Sign-Out URL** který jste zkopírovali z portálu Azure.
    
    d. Vyberte **odhlášení odpovědi a typ požadavku HTTP** jako **POST**.
   
    e. Otevřete váš **kódování base-64** kódovaného certifikátu v poznámkovém bloku stáhli z portálu Azure, zkopírujte obsah ho do schránky a vložte jej do **veřejný certifikát** textové pole.
    
12. V **odpovědi a konfiguraci ověřování**, proveďte následující kroky:
    
    ![Odpověď a konfiguraci ověřování](./media/active-directory-saas-igloo-software-tutorial/IC783971.png "odpovědi a konfiguraci ověřování")
  
      a. Jako **zprostředkovatele Identity**, vyberte **Microsoft ADFS**.
      
      b. Jako **typ identifikátoru**, vyberte **e-mailovou adresu**. 

      c. V **atribut e-mailu** textovému poli, typ **emailaddress**.

      d. V **křestní jméno atribut** textovému poli, typ **givenname**.

      e. V **poslední atribut Name** textovému poli, typ **Přezdívka**.

13. Proveďte následující kroky k dokončení konfigurace:
    
    ![Vytvoření uživatele na přihlašovací](./media/active-directory-saas-igloo-software-tutorial/IC783972.png "vytvoření uživatele na přihlášení") 

     a. Jako **vytvoření uživatele na přihlašovací**, vyberte **vytvořte nového uživatele ve vaší lokalitě při přihlášení**.

     b. Jako **přihlášení nastavení**, vyberte **SAML použijte tlačítko na obrazovce "Přihlásit"**.

     c. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-igloo-software-test-user"></a>Vytváření testovacího uživatele Igloo softwaru

Neexistuje žádná položka akce můžete nakonfigurovat software Igloo zřizování uživatelů.  

Když přiřazený uživatel se pokusí přihlásit k Igloo Software pomocí přístupového panelu, Igloo softwaru ověří, zda uživatel existuje.  Pokud neexistuje žádný účet k dispozici dosud, se automaticky vytvoří Igloo softwarem.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Igloo softwaru.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Igloo softwaru, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Igloo softwaru**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Igloo softwaru na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Igloo softwaru.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_203.png

