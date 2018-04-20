---
title: 'Kurz: Azure Active Directory integrace se serverem Tableau | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Tableau serveru.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: 3b0390c8b95a46b2c134252532bef118ea4df52d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Kurz: Azure Active Directory integrace se serverem Tableau

V tomto kurzu zjistěte, jak integrovat Tableau serveru se službou Azure Active Directory (Azure AD).

Integrace serveru Tableau s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k serveru Tableau
- Můžete povolit uživatelům, aby automaticky získat přihlášení k serveru Tableau (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD se serverem Tableau, potřebujete následující položky:

- Předplatné služby Azure AD
- Serveru Tableau jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání serveru Tableau z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-tableau-server-from-the-gallery"></a>Přidání serveru Tableau z Galerie
Při konfiguraci integrace Tableau serveru do Azure AD, potřebujete přidat Tableau Server z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat Tableau Server z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Tableau Server**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_search.png)

5. Na panelu výsledků vyberte **Tableau Server**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Tableau serveru podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějšku Tableau serveru je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské Tableau serveru je potřeba vytvořit.

V serveru Tableau přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Tableau serveru, musíte dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele serveru Tableau](#creating-a-tableau-server-test-user)**  – Pokud chcete mít protějšek Britta Simon v Tableau Server, který je připojen k reprezentaci Azure AD uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Tableau serveru.

**Ke konfiguraci Azure AD jednotné přihlašování k serveru Tableau, proveďte následující kroky:**

1. Na portálu Azure na **Tableau Server** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_samlbase.png)

3. Na **Tableau Server domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://azure.<domain name>.link`
    
    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://azure.<domain name>.link`

    c. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://azure.<domain name>.link/wg/saml/SSO/index.html`
     
    > [!NOTE] 
    > Předchozí hodnoty nejsou skutečné hodnoty. Později aktualizujte hodnoty s skutečná adresa URL a identifikátor ze stránky konfigurace serveru Tableau. 

4. Aplikace serveru tableau očekává, že SAML kontrolní výrazy ve specifickém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z **"Atributy uživatele"** části na stránce integrace aplikace. Následující snímek obrazovky ukazuje příklad pro stejné.
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-tableauserver-tutorial/3.png)
    
5. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ---------------| --------------- |    
    | uživatelské jméno | *user.mailnickname* |

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-tableauserver-tutorial/tutorial_officespace_04.png)

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-tableauserver-tutorial/tutorial_officespace_05.png)
    
    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.
    
    d. Klikněte na tlačítko **Ok**


6. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_certificate.png) 

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_400.png)
<CS>
8. Pokud chcete získat jednotné přihlašování, které jsou nakonfigurované pro vaši aplikaci, musíte k přihlašování ke klientovi Tableau serveru jako správce.
   
   a. V konfiguraci serveru Tableau, klikněte na **SAML** kartě.
  
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_001.png) 
  
   b. Zaškrtněte políčko z **pomocí SAML pro jednotné přihlašování**.
   
   c. Adresa URL odpovědi ze serveru tableau – adresa URL, který Tableau Server uživatelé budou, jako například http://tableau_server. Pomocí http://localhost se nedoporučuje. Použijete adresu URL s koncové lomítko (například http://tableau_server/) není podporován. Kopírování **Tableau Server návratovou adresu URL** a vložte jej do služby Azure AD **přihlašovací adresa URL** textového pole v **Tableau Server domény a adresy URL** části.
   
   d. SAML entity ID – entity ID jednoznačně identifikuje instalaci serveru Tableau do rozšíření IdP. Můžete zadat vaše adresa URL serveru Tableau znovu sem, pokud chcete, ale nemusí být adresa URL serveru Tableau. Kopírování **SAML entity ID** a vložte jej do služby Azure AD **identifikátor** textového pole v **Tableau Server domény a adresy URL** části.
     
   e. Klikněte **exportovat soubor metadat** a otevře ji v aplikaci textový editor. Vyhledejte adresu URL služby příjemce kontrolní výraz s Http Post a Index 0 a zkopírujte adresu URL. Nyní vložte jej do služby Azure AD **adresa URL odpovědi** textového pole v **Tableau Server domény a adresy URL** části.
   
   f. Vyhledejte soubor federačních metadat stáhli z portálu Azure a nahrajte ji v **soubor metadat SAML Idp**.
   
   g. Klikněte **OK** tlačítka na stránce konfigurace serveru Tableau.
   
    >[!NOTE] 
    >Zákazník muset nahrát některý z certifikátů v konfiguraci jednotné přihlašování SAML Tableau serveru a bude získat ignorován v toku jednotné přihlašování.
    >Pokud jste třeba pomoci konfigurace SAML na serveru Tableau pak naleznete v tomto článku [konfigurace SAML](http://onlinehelp.tableau.com/current/server/en-us/config_saml.htm).
    >
<CE>

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-tableau-server-test-user"></a>Vytvoření zkušebního uživatele Tableau serveru

Cílem této části je vytvoření uživatele volat Britta Simon Tableau serveru. Budete muset zřídit všechny uživatele v serveru Tableau. 

Shodovat s tímto uživatelským jménem uživatele, hodnotu, která jste nakonfigurovali v Azure AD vlastní atribut **uživatelské jméno**. Pomocí správné mapování by měla fungovat integrace [konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on).

>[!NOTE]
>Pokud je potřeba ručně vytvořit uživateli, budete muset obrátit na správce serveru Tableau ve vaší organizaci.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k serveru Tableau.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit k serveru Tableau, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Tableau Server**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Tableau serveru na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Tableau serveru.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_203.png

