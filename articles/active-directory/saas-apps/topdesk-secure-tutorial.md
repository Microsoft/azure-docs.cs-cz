---
title: 'Kurz: Azure Active Directory integrace s TOPdesk - zabezpečené | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TOPdesk - zabezpečené.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8e06ee33-18f9-4c05-9168-e6b162079d88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: ca393279da44ab06077ecbb46a93b8f55222c0fa
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35899382"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Kurz: Azure Active Directory integrace s TOPdesk - zabezpečení

V tomto kurzu zjistíte, jak integrovat TOPdesk - zabezpečit pomocí služby Azure Active Directory (Azure AD).

Integrace TOPdesk - zabezpečené s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k TOPdesk - zabezpečené.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k TOPdesk - zabezpečené (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s TOPdesk – zabezpečení, potřebujete následující položky:

- Předplatné služby Azure AD
- A TOPdesk - zabezpečené jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání TOPdesk – zabezpečení z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-topdesk---secure-from-the-gallery"></a>Přidání TOPdesk – zabezpečení z Galerie
Konfigurace integrace TOPdesk – zabezpečení do služby Azure AD, budete muset přidat TOPdesk – Secure z Galerie do seznamu spravovaných SaaS aplikace.

**Chcete-li přidat TOPdesk - zabezpečit z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **TOPdesk - zabezpečené**, vyberte **TOPdesk - zabezpečené** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![TOPdesk – zabezpečení v seznamu výsledků](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s TOPdesk – zabezpečení podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co příslušného uživatele v TOPdesk – zabezpečení je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v TOPdesk – zabezpečení musí být vytvořeno.

V TOPdesk - zabezpečit, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s TOPdesk - bezpečný, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření TOPdesk - zabezpečené testovacího uživatele](#create-a-topdesk---secure-test-user)**  – Pokud chcete mít protějšek Britta Simon v TOPdesk - zabezpečeného propojeného s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v vaší TOPdesk - zabezpečené aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s TOPdesk - zabezpečit, proveďte následující kroky:**

1. Na portálu Azure na **TOPdesk - zabezpečené** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_samlbase.png)

3. Na **TOPdesk – zabezpečení domény a adresy URL** část, proveďte následující kroky:

    ![Jednotné přihlašování informace TOPdesk – zabezpečení domény a adresy URL](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.topdesk.net`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    c. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.topdesk.net/tas/public/login/saml`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Adresa URL odpovědi se vysvětluje dále v kurzu. Obraťte se na [TOPdesk - tým podpory zabezpečení klienta](http://www.topdesk.com/us/support) k získání těchto hodnot. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/topdesk-secure-tutorial/tutorial_general_400.png)

6. Na **TOPdesk - zabezpečené konfigurace** klikněte na tlačítko **TOPdesk nakonfigurovat - zabezpečené** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![TOPdesk - konfiguraci zabezpečení](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_configure.png)
    
7. Přihlaste se k vaší **TOPdesk - zabezpečené** společnosti lokality jako správce.

8. V **TOPdesk** nabídky, klikněte na tlačítko **nastavení**.

    ![Nastavení](./media/topdesk-secure-tutorial/ic790598.png "nastavení")

9. Klikněte na tlačítko **nastavení přihlášení**.

    ![Nastavení přihlášení](./media/topdesk-secure-tutorial/ic790599.png "nastavení přihlášení")

10. Rozbalte **nastavení přihlášení** nabídce a pak klikněte na tlačítko **Obecné**.

    ![Obecné](./media/topdesk-secure-tutorial/ic790600.png "obecné")

11. V **zabezpečeného** části **SAML přihlášení** konfigurace části, proveďte následující kroky:

    ![Technické nastavení](./media/topdesk-secure-tutorial/ic790855.png "technické nastavení")
   
    a. Klikněte na tlačítko **Stáhnout** ke stažení souboru metadat veřejné a poté je uložit místně v počítači.
   
    b. Otevřete soubor metadat a vyhledejte **AssertionConsumerService** uzlu.
    
    ![Kontrolní výraz příjemce služby](./media/topdesk-secure-tutorial/ic790856.png "Assertion příjemce služby")
   
    c. Kopírování **AssertionConsumerService** hodnotu, vložte tuto hodnotu v textovém poli Adresa URL odpovědi v **TOPdesk – zabezpečení domény a adresy URL** části.

12. Pokud chcete vytvořit soubor s certifikátem, proveďte následující kroky:
    
    ![Certifikát](./media/topdesk-secure-tutorial/ic790606.png "certifikátu")
    
    a. Otevřete soubor metadat stažený z portálu Azure.

    b. Rozbalte **RoleDescriptor** uzlu, který má **xsi: type** z **dodáni: ApplicationServiceType**.

    c. Zkopírujte hodnotu **certifikátu x 509** uzlu.

    d. Uložit zkopírovaný **certifikátu x 509** hodnota místně na vašem počítači v souboru.

13. V **veřejné** klikněte na tlačítko **přidat**.
    
    ![Přidat](./media/topdesk-secure-tutorial/ic790607.png "přidat")

14. Na **pomocníka konfigurace SAML** dialogové okno proveďte následující kroky:
    
    ![Pomocník pro konfigurace SAML](./media/topdesk-secure-tutorial/ic790608.png "pomocníka konfigurace SAML")
    
    a. K odeslání souboru metadat stažený z portálu Azure, v části **federačních metadat**, klikněte na tlačítko **Procházet**.

    b. Nahrát soubor certifikátu, v části **certifikát (RSA)**, klikněte na tlačítko **Procházet**.

    c. Nahrát soubor logo jste získali od týmu podpory TOPdesk, v části **Logo ikonu**, klikněte na tlačítko **Procházet**.

    d. V **atribut uživatelského jména** textovému poli, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. V **zobrazovaný název** textovému poli, zadejte název pro svou konfiguraci.

    f. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/topdesk-secure-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/topdesk-secure-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/topdesk-secure-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/topdesk-secure-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-topdesk---secure-test-user"></a>Vytvoření TOPdesk - zabezpečené testovacího uživatele

Chcete-li povolit uživatelům Azure AD přihlášení do TOPdesk - bezpečný, se musí být zřízená do TOPdesk - zabezpečené.  
V případě TOPdesk - zabezpečený a zřizování je ruční úloha.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pokud chcete konfigurovat, zřizování uživatelů, proveďte následující kroky:
1. Přihlaste se k vaší **TOPdesk - zabezpečené** společnosti lokality jako správce.
2. V nabídce v horní části, klikněte na tlačítko **TOPdesk \> nový \> soubory podpory \> operátor**.
   
    ![Operátor](./media/topdesk-secure-tutorial/ic790610.png "– operátor")

3. Na **operátor New** dialogové okno, proveďte následující kroky:
   
    ![Operátor new](./media/topdesk-secure-tutorial/ic790611.png "New – operátor")
   
    a. Klikněte **Obecné** kartě.
   
    b. V **Přezdívka** textovému poli, zadejte příjmení uživatele jako **Simon**.
   
    c. Vyberte **lokality** pro účet v **umístění** části.
   
    d. V **přihlašovací jméno** textbox z **TOPdesk přihlášení** zadejte přihlašovací jméno uživatele.
   
    e. Klikněte na **Uložit**.

> [!NOTE]
> Můžete použít všechny ostatní TOPdesk – rozhraní API poskytované TOPdesk - zabezpečené zřídit AAD uživatelské účty nebo nástroje pro tvorbu účet zabezpečení uživatele.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu TOPdesk - zabezpečené.

![Přiřadit role uživatele][200] 

**Britta Simon přiřadit TOPdesk - zabezpečit, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **TOPdesk - zabezpečené**.

    ![TOPdesk - zabezpečené připojení v seznamu aplikací](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Po kliknutí na tlačítko TOPdesk - zabezpečené dlaždice na přístupovém panelu jste měli získat automaticky přihlášení k vaší TOPdesk - zabezpečené aplikace.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/topdesk-secure-tutorial/tutorial_general_01.png
[2]: ./media/topdesk-secure-tutorial/tutorial_general_02.png
[3]: ./media/topdesk-secure-tutorial/tutorial_general_03.png
[4]: ./media/topdesk-secure-tutorial/tutorial_general_04.png

[100]: ./media/topdesk-secure-tutorial/tutorial_general_100.png

[200]: ./media/topdesk-secure-tutorial/tutorial_general_200.png
[201]: ./media/topdesk-secure-tutorial/tutorial_general_201.png
[202]: ./media/topdesk-secure-tutorial/tutorial_general_202.png
[203]: ./media/topdesk-secure-tutorial/tutorial_general_203.png

