---
title: 'Kurz: Azure Active Directory integrace s EBSCO | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a EBSCO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeedes
ms.openlocfilehash: 79ef1adf2989ed2982c9625cfa7e6421461b908f
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35899148"
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>Kurz: Azure Active Directory integrace s EBSCO

V tomto kurzu zjistěte, jak integrovat EBSCO s Azure Active Directory (Azure AD).

Integrace EBSCO s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k EBSCO.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k EBSCO (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s EBSCO, potřebujete následující položky:

- Předplatné služby Azure AD
- EBSCO jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání EBSCO z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-ebsco-from-the-gallery"></a>Přidání EBSCO z Galerie
Při konfiguraci integrace EBSCO do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS EBSCO z galerie.

**Pokud chcete přidat EBSCO z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **EBSCO**, vyberte **EBSCO** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![EBSCO v seznamu výsledků](./media/ebsco-tutorial/tutorial_ebsco_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s EBSCO podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v EBSCO je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v EBSCO musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s EBSCO, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovací uživatele s EBSCO](#create-an-ebsco-test-user)**  – můžete automatizovat EBSCOhost zřizování nebo přizpůsobení uživatelem. EBSCO podporuje pouze za běhu zřizování uživatelů.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci EBSCO.

**Ke konfiguraci Azure AD jednotné přihlašování s EBSCO, proveďte následující kroky:**

1. Na portálu Azure na **EBSCO** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/ebsco-tutorial/tutorial_ebsco_samlbase.png)

3. Na **EBSCO domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![EBSCO domény a adresy URL jednotné přihlašování informace](./media/ebsco-tutorial/tutorial_ebsco_url.png)

    V **identifikátor** textovému poli, zadejte adresu URL: `pingsso.ebscohost.com`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![EBSCO domény a adresy URL jednotné přihlašování informace](./media/ebsco-tutorial/tutorial_ebsco_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`
     
    > [!NOTE] 
    > Hodnota přihlašovací adresa URL není skutečné. Aktualizujte hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory EBSCO klienta](mailto:sso@ebsco.com) k získání hodnoty. 

    o **jedinečný prvky:**  

    o **Custid** = zadejte jedinečné ID EBSCO zákazníka 

    o **profil** = klientů můžete přizpůsobit propojení, které uživatele nasměrují na konkrétní profilu (v závislosti na tom, co se zakoupit od EBSCO). Můžou zadat konkrétní profil ID. Hlavní ID jsou eds (služba zjišťování EBSCO) a ehost (EBSOCOhost databáze). Jsou uvedeny pokyny pro stejné [zde](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

5. Aplikace EBSCO očekává SAML kontrolní výrazy ve specifickém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z "**uživatelské atributy**" části na stránce integrace aplikace. Následující snímek obrazovky ukazuje příklad pro tento.
    
    ![Konfigurovat jednotné přihlašování](./media/ebsco-tutorial/tutorial_ebsco_attribute.png)

    > [!Note]
    > **Název** atribut je povinný a je namapována na **uživatelský identifikátor** v EBSCO aplikaci. Ve výchozím nastavení to je přidáván, takže nemusíte to přidat ručně.
    
6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ---------------| --------------- |    
    | FirstName   | user.givenname |
    | Příjmení   | user.surname |
    | Email   | User.Mail |

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/ebsco-tutorial/tutorial_officespace_04.png)

    ![Konfigurovat jednotné přihlašování](./media/ebsco-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.
    
    d. Klikněte na tlačítko **Ok**

7. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/ebsco-tutorial/tutorial_ebsco_certificate.png) 

8. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/ebsco-tutorial/tutorial_general_400.png)
    
9. Konfigurace jednotného přihlašování na **EBSCO** straně, budete muset odeslat stažené **soubor XML s metadaty** k [tým podpory EBSCO](mailto:sso@ebsco.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/ebsco-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/ebsco-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/ebsco-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/ebsco-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-ebsco-test-user"></a>Vytvořit uživatele s EBSCO testu

V případě EBSCO je automatické zřizování uživatelů.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

Azure AD předá EBSCO aplikaci požadovaná data. Zřizování uživatelů na EBSCO může být automatické nebo vyžadovat jednorázové formuláře. To závisí na tom, zda má klient spoustu existující účty EBSCOhost s osobní nastavení uložit. Stejné můžete popsané s [tým podpory EBSCO](mailto:sso@ebsco.com) během implementace. V obou případech klient nemá k vytváření účtů EBSCOhost před testování.

   >[!Note]
   >Je možné automatizovat EBSCOhost zřizování nebo přizpůsobení uživatelem. Obraťte se na [tým podpory EBSCO](mailto:sso@ebsco.com) o těsně za běhu zřizování uživatelů. 
 
### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu EBSCO.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon EBSCO, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **EBSCO**.

    ![V seznamu aplikací na EBSCO odkaz](./media/ebsco-tutorial/tutorial_ebsco_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

1. Když kliknete na dlaždici EBSCO na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci EBSCO.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

2. Jakmile se přihlásíte k aplikaci, klikněte na **přihlásit** tlačítko v pravém horním rohu.

    ![Přihlášení EBSCO v seznamu aplikací](./media/ebsco-tutorial/tutorial_ebsco_signin.png)
 
3. Obdržíte jednorázové výzvu k spárujte institucionální/SAML přihlášení se **existujícího účtu MyEBSCOhost propojit účtu instituce, která teď** nebo **vytvořit nový účet MyEBSCOhost a propojovat je se vaše účet instituce**. Účet se používá pro přizpůsobení na EBSCOhost aplikaci. Vyberte možnost **vytvořit nový účet** a zobrazí se, že formulář pro přizpůsobení je předem byla dokončena s hodnoty z odpověď saml, jak je vidět na tomto snímku obrazovky. Klikněte na tlačítko **'Pokračovat'** uložte tento výběr.
    
     ![EBSCO uživatele v seznamu aplikací](./media/ebsco-tutorial/tutorial_ebsco_user.png)

4. Po dokončení výše uvedených nastavení vymažte soubory cookie nebo mezipaměti a přihlaste se znovu. Nebudete muset ručně přihlášení znovu a uloží individuální nastavení

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ebsco-tutorial/tutorial_general_01.png
[2]: ./media/ebsco-tutorial/tutorial_general_02.png
[3]: ./media/ebsco-tutorial/tutorial_general_03.png
[4]: ./media/ebsco-tutorial/tutorial_general_04.png

[100]: ./media/ebsco-tutorial/tutorial_general_100.png

[200]: ./media/ebsco-tutorial/tutorial_general_200.png
[201]: ./media/ebsco-tutorial/tutorial_general_201.png
[202]: ./media/ebsco-tutorial/tutorial_general_202.png
[203]: ./media/ebsco-tutorial/tutorial_general_203.png

