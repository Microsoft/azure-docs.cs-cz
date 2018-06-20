---
title: 'Kurz: Azure Active Directory integrace s BambooHR | Microsoft Docs'
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
ms.openlocfilehash: d31d8d92590f665b847bb92370967e88ef4590ff
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210699"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Kurz: Azure Active Directory integrace s BambooHR

V tomto kurzu zjistěte, jak integrovat BambooHR s Azure Active Directory (Azure AD).

Integrace BambooHR s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k BambooHR.
- Můžete povolit uživatelům automaticky získat přihlášení k BambooHR pomocí jednotného přihlašování (SSO) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě, portálu Azure.

Další informace o integraci aplikací SaaS v Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s BambooHR, potřebujete následující položky:

- Předplatné služby Azure AD
- Předplatné povolené BambooHR jednotného přihlašování

> [!NOTE]
> Při testování kroky v tomto kurzu, doporučujeme, abyste nepoužívali provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získání bezplatné zkušební jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. 

Scénář, který tento kurz popisuje se skládá ze dvou hlavních stavebních bloků:

1. Přidání BambooHR z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-bamboohr-from-the-gallery"></a>Přidat BambooHR z Galerie
Při konfiguraci integrace BambooHR do služby Azure AD přidáte BambooHR z Galerie váš seznam spravovaných aplikací SaaS následujícím způsobem:

1. V [portál Azure](https://portal.azure.com), v levém podokně vyberte **Azure Active Directory**. 

    ![Tlačítko Azure Active Directory][1]

2. Vyberte **podnikové aplikace, které** > **všechny aplikace**.

    ![V podokně podnikových aplikací][2]
    
3. Chcete-li přidat aplikaci, vyberte **novou aplikaci**.

    !["Nová aplikace" tlačítko][3]

4. Do vyhledávacího pole zadejte **BambooHR**. V seznamu výsledků vyberte **BambooHR**a potom vyberte **přidat**.

    ![BambooHR v seznamu výsledků](./media/bamboo-hr-tutorial/tutorial_bamboohr_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD přihlášení SSO se BambooHR pomocí testovacího uživatele "Britta Simon."

Pro jednotné přihlašování pro práci je potřeba vědět, co je uživatel jeho protějškem v BambooHR Azure AD. Jinými slovy je potřeba vytvořit vztah propojení mezi uživatele Azure AD a související uživatelské v BambooHR.

Chcete-li vytvořit vztah odkaz v BambooHR, přiřaďte Azure AD **uživatelské jméno** hodnotu jako BambooHR **uživatelské jméno** hodnotu.

Nakonfigurovat a otestovat Azure AD přihlášení SSO se BambooHR, dokončete stavebních bloků v následujících pět částech.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části Povolení jednotného přihlašování Azure AD na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci BambooHR následujícím způsobem:

1. Na portálu Azure na **BambooHR** stránky integrace aplikací, vyberte **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. V **jednotného přihlašování** okno v **režimu** rozevíracího seznamu vyberte **na základě SAML přihlašování**.
 
    ![Okno přihlášení](./media/bamboo-hr-tutorial/tutorial_bamboohr_samlbase.png)

3. V části **BambooHR domény a adresy URL**, postupujte takto:

    ![V části BambooHR domény a adresy URL](./media/bamboo-hr-tutorial/tutorial_bamboohr_url.png)

    a. V **přihlásit na adrese URL** pole, zadejte adresu URL v následujícím formátu: `https://<company>.bamboohr.com`.

    b. V **identifikátor** zadejte hodnotu: `BambooHR-SAML`.

    > [!NOTE] 
    > **Přihlásit na adrese URL** hodnota není skutečné. Aktualizujte jej s skutečná adresa URL přihlašování. Chcete-li získat hodnotu, obraťte se [tým podpory klienta BambooHR](https://www.bamboohr.com/contact.php). 
 
4. V části **SAML podpisový certifikát**, vyberte **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/bamboo-hr-tutorial/tutorial_bamboohr_certificate.png) 

5. Vyberte **Uložit**.

    ![Tlačítko Uložit](./media/bamboo-hr-tutorial/tutorial_general_400.png)

6. V části **BambooHR konfigurace**, vyberte **konfigurace BambooHR** otevřete **konfigurovat přihlášení** okno. V **Stručná referenční příručka** část, zkopírujte **SAML jeden přihlašování adresa URL služby** pro pozdější použití.

    ![Konfigurace BambooHR](./media/bamboo-hr-tutorial/tutorial_bamboohr_configure.png) 

7. V novém okně Přihlaste se k serveru vaší společnosti BambooHR jako správce.

8. Na domovské stránce postupujte takto:
   
    ![Stránka BambooHR Single Sign-On](./media/bamboo-hr-tutorial/ic796691.png "jednotné přihlašování")   

    a. Vyberte **aplikace**.
   
    b. V **aplikace** podokně, vyberte **jednotné přihlašování**.
   
    c. Vyberte **SAML Single Sign-On**.

9. V **SAML Single Sign-On** podokně, postupujte takto:
   
    ![V podokně SAML Single Sign-On](./media/bamboo-hr-tutorial/IC796692.png "SAML jednotné přihlašování")
   
    a. Do **adresu Url pro přihlášení SSO** pole, vložte **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure v kroku 6.
      
    b. V poznámkovém bloku otevřete kódování base-64 kódovaného certifikátu, který jste stáhli z portálu Azure, zkopírujte její obsah a vložte ji do **certifikát X.509** pole.
   
    c. Vyberte **Uložit**.

> [!TIP]
> Při nastavování aplikaci, můžete si přečíst stručným verzi tyto pokyny v [portál Azure](https://portal.azure.com). Po přidání aplikace z **služby Active Directory** > **podnikové aplikace, které** jednoduše vyberte **jednotné přihlašování** kartě a potom přejdete vložené dokumentace prostřednictvím **konfigurace** v dolní části. Informace najdete v tématu [Azure AD vložených dokumentaci]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele volat Britta Simon na portálu Azure.

   ![Vytvoření zkušebního uživatele Azure AD Britta Simon][100]

Vytvoření zkušebního uživatele ve službě Azure AD, postupujte takto:

1. Na portálu Azure, v levém podokně, vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](./media/bamboo-hr-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom vyberte **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/bamboo-hr-tutorial/create_aaduser_02.png)

3. V horní části **všichni uživatelé** podokně, vyberte **přidat**.

    ![Tlačítko Přidat](./media/bamboo-hr-tutorial/create_aaduser_03.png)

4. V **uživatele** okno, postupujte takto:

    ![Okno uživatele](./media/bamboo-hr-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-bamboohr-test-user"></a>Vytvoření zkušebního uživatele BambooHR

Pokud chcete povolit uživatelům Azure AD přihlášení k BambooHR, vytvořit ručně v BambooHR následujícím způsobem:

1. Přihlaste se k vaší **BambooHR** lokality jako správce.

2. Na panelu nástrojů v horní části vyberte **nastavení**.
   
    ![Tlačítko Nastavení](./media/bamboo-hr-tutorial/IC796694.png "nastavení")

3. Vyberte **Přehled**.

4. V levém podokně vyberte **zabezpečení** > **uživatelé**.

5. Zadejte uživatelské jméno, heslo a e-mailovou adresu, platný Azure AD účtu. Chcete nastavit.

6. Vyberte **Uložit**.
        
>[!NOTE]
>Pokud chcete nastavit uživatelské účty Azure AD, můžete použít rozhraní API nebo nástroje pro vytváření účtů BambooHR uživatele.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

Umožní uživateli Britta Simon chcete použít Azure jednotného přihlašování k udělení přístupu BambooHR.

![Přiřadit role uživatele][200] 

Pokud chcete přiřadit uživatele Britta Simon BambooHR, postupujte takto:

1. Na portálu Azure otevřete zobrazení aplikace, přejděte do zobrazení adresáře a pak vyberte **podnikové aplikace, které** > **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V **podnikové aplikace, které** seznamu, vyberte **BambooHR**.

    ![Odkaz BambooHR v seznamu podnikových aplikací](./media/bamboo-hr-tutorial/tutorial_bamboohr_app.png)  

3. V levém podokně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Vyberte **přidat** tlačítko a pak na **přidat přiřazení** podokně, vyberte **uživatelů a skupin**.

    ![V podokně Přidat přiřazení][203]

5. V **uživatelů a skupin** okno v **uživatelé** seznamu, vyberte **Britta Simon**.

6. Vyberte **vyberte** tlačítko.

7. V **přidat přiřazení** vyberte **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Otestujte konfiguraci Azure AD jednotného přihlašování pomocí přístupového panelu.

Když vyberete **BambooHR** dlaždici na přístupovém panelu budete by měl získat automaticky přihlášení k aplikaci BambooHR.

Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS v Azure Active Directory](tutorial-list.md)
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

