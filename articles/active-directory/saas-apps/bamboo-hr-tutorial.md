---
title: 'Kurz: Integrace Azure Active Directory se službou BambooHR | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a BambooHR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: 77625296797ec8ed8364e7d8bff3e5a15b4b74b5
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048034"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Kurz: Integrace Azure Active Directory se službou BambooHR

V tomto kurzu se dozvíte, jak integrovat BambooHR s Azure Active Directory (Azure AD).

BambooHR integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k BambooHR.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k BambooHR pomocí jednotného přihlašování (SSO) s jejich účty Azure AD.
- Můžete spravovat své účty v jednom centrálním místě na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s BambooHR, potřebujete následující položky:

- S předplatným služby Azure AD
- Předplatné podporující BambooHR jednotného přihlašování

> [!NOTE]
> Při testování kroky v tomto kurzu doporučujeme je velmi riskantní používat produkčním prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získáte bezplatné měsíční zkušební](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. 

Scénáře, který se popisuje v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání BambooHR z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-bamboohr-from-the-gallery"></a>Přidání BambooHR z Galerie
Pokud chcete nakonfigurovat integraci BambooHR do služby Azure AD, přidejte BambooHR z Galerie váš seznam spravovaných aplikací SaaS následujícím způsobem:

1. V [webu Azure portal](https://portal.azure.com), v levém podokně vyberte **Azure Active Directory**. 

    ![Tlačítko Azure Active Directory][1]

2. Vyberte **podnikové aplikace** > **všechny aplikace**.

    ![V podokně podnikových aplikací][2]
    
3. Chcete-li přidat aplikaci, vyberte **novou aplikaci**.

    !["Nové aplikace" tlačítko][3]

4. Do vyhledávacího pole zadejte **BambooHR**. V seznamu výsledků vyberte **BambooHR**a pak vyberte **přidat**.

    ![BambooHR v seznamu výsledků](./media/bamboo-hr-tutorial/tutorial_bamboohr_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části můžete nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s BambooHR pomocí testovacího uživatele "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co je jeho protějšek uživatel v BambooHR. Jinými slovy je potřeba vytvořit vztah odkazu mezi uživatele Azure AD a související uživatel v BambooHR.

Chcete-li v BambooHR vytvoří vztah odkazu, přiřaďte Azure AD **uživatelské jméno** hodnoty jako BambooHR **uživatelské jméno** hodnotu.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s BambooHR, dokončete stavební bloky v příštích pěti oddílů.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení jednotného přihlašování Azure AD na webu Azure Portal a konfigurace jednotného přihlašování v aplikaci BambooHR následujícím způsobem:

1. Na webu Azure Portal na **BambooHR** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. V **jednotného přihlašování** okno v **režimu** rozevíracího seznamu vyberte **přihlašování na základě SAML**.
 
    ![Okno jednotné přihlašování](./media/bamboo-hr-tutorial/tutorial_bamboohr_samlbase.png)

3. V části **BambooHR domény a adresy URL**, postupujte takto:

    ![V části BambooHR domény a adresy URL](./media/bamboo-hr-tutorial/tutorial_bamboohr_url.png)

    a. V **přihlašovací adresa URL** pole, zadejte adresu URL v následujícím formátu: `https://<company>.bamboohr.com`.

    b. V **identifikátor** zadejte hodnotu: `BambooHR-SAML`.

    > [!NOTE] 
    > **Přihlašovací adresa URL** není skutečnou hodnotu. Vaše skutečné přihlašovací adresa URL aktualizujte ji. K získání hodnoty, obraťte se [tým podpory klienta BambooHR](https://www.bamboohr.com/contact.php). 
 
4. V části **podpisový certifikát SAML**vyberte **certifikát (Base64)** a poté uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/bamboo-hr-tutorial/tutorial_bamboohr_certificate.png) 

5. Vyberte **Uložit**.

    ![Tlačítko Uložit](./media/bamboo-hr-tutorial/tutorial_general_400.png)

6. V části **BambooHR konfigurace**vyberte **nakonfigurovat BambooHR** otevřít **nakonfigurovat přihlašování** okna. V **Stručná referenční příručka** tématu, zkopírujte **SAML jednotné přihlašování – adresa URL služby** pro pozdější použití.

    ![Konfigurace BambooHR](./media/bamboo-hr-tutorial/tutorial_bamboohr_configure.png) 

7. V novém okně se přihlaste se k serveru vaší společnosti BambooHR jako správce.

8. Na domovské stránce postupujte takto:
   
    ![Na stránce BambooHR Single Sign-On](./media/bamboo-hr-tutorial/ic796691.png "jednotného přihlašování")   

    a. Vyberte **aplikace**.
   
    b. V **aplikace** vyberte **Single Sign-On**.
   
    c. Vyberte **SAML jednotného přihlašování**.

9. V **SAML jednotného přihlašování** podokno, postupujte takto:
   
    ![Podokno jednotné přihlašování SAML](./media/bamboo-hr-tutorial/IC796692.png "SAML jednotného přihlašování")
   
    a. Do **přihlašovací adresu Url pro jednotné přihlašování** pole, vložte **SAML jednotné přihlašování – adresa URL služby** , který jste zkopírovali z portálu Azure portal v kroku 6.
      
    b. V poznámkovém bloku otevřete base-64 kódovaných certifikát, který jste si stáhli z webu Azure portal, zkopírujte jeho obsah a vložte jej do **certifikát X.509** pole.
   
    c. Vyberte **Uložit**.

> [!TIP]
> Při nastavování aplikace si můžete přečíst stručné verzi těchto pokynů [webu Azure portal](https://portal.azure.com). Po přidání aplikace ze **služby Active Directory** > **podnikové aplikace** jednoduše vyberte **Single Sign-On** kartu a potom přejdete dokumentace ke službě prostřednictvím Embedded **konfigurace** oblast v dolní části. Informace najdete v tématu [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvořte testovacího uživatele Britta Simon na webu Azure Portal.

   ![Vytvořit testovacího uživatele Azure AD Britta Simon][100]

Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte takto:

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](./media/bamboo-hr-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/bamboo-hr-tutorial/create_aaduser_02.png)

3. V horní části **všichni uživatelé** vyberte **přidat**.

    ![Tlačítko Přidat](./media/bamboo-hr-tutorial/create_aaduser_03.png)

4. V **uživatele** okno, postupujte takto:

    ![V okně uživatele](./media/bamboo-hr-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-bamboohr-test-user"></a>Vytvoření zkušebního uživatele BambooHR

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k BambooHR, nastavte je ručně v BambooHR následujícím způsobem:

1. Přihlaste se k vaší **BambooHR** lokality jako správce.

2. Na panelu nástrojů v horní části vyberte **nastavení**.
   
    ![Tlačítko Nastavení](./media/bamboo-hr-tutorial/IC796694.png "nastavení")

3. Vyberte **Přehled**.

4. V levém podokně vyberte **zabezpečení** > **uživatelé**.

5. Zadejte uživatelské jméno, heslo a e-mailovou adresu platnou Azure AD účtu. Chcete nastavit.

6. Vyberte **Uložit**.
        
>[!NOTE]
>Nastavení uživatelských účtů služby Azure AD, můžete použít nástroje pro vytváření účtu uživatele BambooHR nebo rozhraní API.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

Povolení uživatele Britta Simon k udělení přístupu k BambooHR použití jednotného přihlašování k Azure.

![Přiřazení role uživatele][200] 

Přiřadit uživatele Britta Simon BambooHR, postupujte takto:

1. Na webu Azure Portal, otevřete zobrazení aplikací, přejděte do zobrazení adresáře a pak vyberte **podnikové aplikace** > **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V **podnikové aplikace** seznamu vyberte **BambooHR**.

    ![Odkaz BambooHR v seznamu podnikových aplikací](./media/bamboo-hr-tutorial/tutorial_bamboohr_app.png)  

3. V levém podokně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Vyberte **přidat** tlačítko a pak na **přidat přiřazení** vyberte **uživatelů a skupin**.

    ![Podokno Přidat přiřazení][203]

5. V **uživatelů a skupin** okno v **uživatelé** seznamu vyberte **Britta Simon**.

6. Vyberte **vyberte** tlačítko.

7. V **přidat přiřazení** okna, vyberte **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Testování konfigurace jednotného přihlašování k Azure AD s použitím na přístupovém panelu.

Když vyberete **BambooHR** dlaždici na přístupovém panelu, vám by měl získat automaticky přihlášeni k BambooHR aplikace.

Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů integrace aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bamboo-hr-tutorial/tutorial_general_01.png
[2]: ./media/bamboo-hr-tutorial/tutorial_general_02.png
[3]: ./media/bamboo-hr-tutorial/tutorial_general_03.png
[4]: ./media/bamboo-hr-tutorial/tutorial_general_04.png

[100]: ./media/bamboo-hr-tutorial/tutorial_general_100.png

[200]: ./media/bamboo-hr-tutorial/tutorial_general_200.png
[201]: ./media/bamboo-hr-tutorial/tutorial_general_201.png
[202]: ./media/bamboo-hr-tutorial/tutorial_general_202.png
[203]: ./media/bamboo-hr-tutorial/tutorial_general_203.png

