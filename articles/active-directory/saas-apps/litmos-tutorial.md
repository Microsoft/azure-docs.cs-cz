---
title: 'Kurz: Azure Active Directory integrace s Litmos | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Litmos.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: jeedes
ms.assetid: cfaae4bb-e8e5-41d1-ac88-8cc369653036
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 786040054875d5e90b558ca1684d0ce657205cff
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36219253"
---
# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>Kurz: Azure Active Directory integrace s Litmos

V tomto kurzu zjistěte, jak integrovat Litmos s Azure Active Directory (Azure AD).

Integrace Litmos s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Litmos.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Litmos (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Litmos, potřebujete následující položky:

- Předplatné služby Azure AD
- Litmos jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Litmos z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-litmos-from-the-gallery"></a>Přidání Litmos z Galerie
Při konfiguraci integrace Litmos do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Litmos z galerie.

**Pokud chcete přidat Litmos z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Litmos**, vyberte **Litmos** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Litmos v seznamu výsledků](./media/litmos-tutorial/tutorial_litmos_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Litmos podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Litmos je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Litmos musí navázat.

V Litmos, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Litmos, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Litmos](#create-a-litmos-test-user)**  – Pokud chcete mít protějšek Britta Simon v Litmos propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Litmos.

**Ke konfiguraci Azure AD jednotné přihlašování s Litmos, proveďte následující kroky:**

1. Na portálu Azure na **Litmos** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/litmos-tutorial/tutorial_litmos_samlbase.png)

3. Na **Litmos domény a adresy URL** část, proveďte následující kroky:

    ![Litmos domény a adresy URL jednotné přihlašování informace](./media/litmos-tutorial/tutorial_litmos_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.litmos.com/account/Login`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.litmos.com/integration/samllogin`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizovat se skutečným identifikátorem a adresa URL odpovědi, které jsou vysvětlené později v kurzu nebo kontaktujte [tým podpory Litmos](https://www.litmos.com/contact-us/) k získání těchto hodnot.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/litmos-tutorial/tutorial_litmos_certificate.png)

5. Jako součást konfigurace, budete muset přizpůsobit **atributy tokenu SAML** pro vaši aplikaci Litmos.

    ![Atribut části](./media/litmos-tutorial/tutorial_attribute.png)
           
    | Název atributu   | Hodnota atributu |   
    | ---------------  | ----------------|
    | FirstName |user.givenname |
    | Příjmení  |user.surname |
    | Email |User.Mail |

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Přidat atribut](./media/litmos-tutorial/tutorial_attribute_04.png)

    ![Přidání atributu Dailog](./media/litmos-tutorial/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.
    
    d. Klikněte na tlačítko **OK**.     

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/litmos-tutorial/tutorial_general_400.png)

7. V okně jiný prohlížeč přihlašování k webu společnosti Litmos jako správce.

8. V navigačním panelu na levé straně klikněte na **účty**.
   
    ![Oddílu účtů na straně aplikace][22] 

9. Klikněte **integrace** kartě.
   
    ![Karta integrace][23] 

10. Na **integrace** kartě, přejděte dolů k položce **3. stran integrace**a potom klikněte na **SAML 2.0** kartě.
   
    ![SAML 2.0 části][24] 

11. Zkopírujte hodnotu v části **je koncový bod SAML pro litmos:** a vložte ji do **adresa URL odpovědi** textového pole v **Litmos domény a adresy URL** části na portálu Azure. 
   
    ![Koncový bod SAML][26] 

12. Ve vašem **Litmos** aplikace, proveďte následující kroky:
    
     ![Litmos aplikace][25] 
     
     a. Klikněte na tlačítko **povolit SAML**.
    
     b. V poznámkovém bloku otevřete váš kódování base-64 kódovaného certifikátu, zkopírujte obsah ho do schránky a vložte jej do **certifikát X.509 SAML** textové pole.
     
     c. Klikněte na tlačítko **uložit změny**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/litmos-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/litmos-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/litmos-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/litmos-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-litmos-test-user"></a>Vytvoření zkušebního uživatele Litmos

Cílem této části je vytvoření uživatele v Litmos nazývá Britta Simon.  
Litmos aplikace podporuje pouze za běhu zřizování. To znamená, uživatelský účet se automaticky vytvoří v případě potřeby při pokusu o přístup k aplikaci pomocí přístupového panelu.

**Vytvoření uživatele v Litmos nazývá Britta Simon, proveďte následující kroky:**

1. V okně jiný prohlížeč přihlašování k webu společnosti Litmos jako správce.

2. V navigačním panelu na levé straně klikněte na **účty**.
   
    ![Oddílu účtů na straně aplikace][22] 

3. Klikněte **integrace** kartě.
   
    ![Karta integrace][23] 

4. Na **integrace** kartě, přejděte dolů k položce **3. stran integrace**a potom klikněte na **SAML 2.0** kartě.
   
    ![SAML 2.0][24] 
    
5. Vyberte **generovat uživatelů**
   
    ![Generovat uživatelů][27] 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Litmos.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Litmos, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Litmos**.

    ![V seznamu aplikací na Litmos odkaz](./media/litmos-tutorial/tutorial_litmos_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.  

Když kliknete na dlaždici Litmos na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Litmos. 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/litmos-tutorial/tutorial_general_01.png
[2]: ./media/litmos-tutorial/tutorial_general_02.png
[3]: ./media/litmos-tutorial/tutorial_general_03.png
[4]: ./media/litmos-tutorial/tutorial_general_04.png
[21]: ./media/litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/litmos-tutorial/tutorial_litmos_66.png

[100]: ./media/litmos-tutorial/tutorial_general_100.png

[200]: ./media/litmos-tutorial/tutorial_general_200.png
[201]: ./media/litmos-tutorial/tutorial_general_201.png
[202]: ./media/litmos-tutorial/tutorial_general_202.png
[203]: ./media/litmos-tutorial/tutorial_general_203.png

