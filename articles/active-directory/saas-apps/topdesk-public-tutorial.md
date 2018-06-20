---
title: 'Kurz: Azure Active Directory integrace s TOPdesk - veřejné | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TOPdesk - veřejné.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: cb4c530f8fd58d95b75c23aae165bd826a698bb0
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211481"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Kurz: Azure Active Directory integrace s TOPdesk - veřejné

V tomto kurzu zjistěte, jak integrovat TOPdesk - veřejné službou Azure Active Directory (Azure AD).

Integrace TOPdesk - veřejné s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k TOPdesk - veřejné.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k TOPdesk - veřejné (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s TOPdesk - veřejné, potřebujete následující položky:

- Předplatné služby Azure AD
- TOPdesk - veřejné jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání TOPdesk - veřejné z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-topdesk---public-from-the-gallery"></a>Přidání TOPdesk - veřejné z Galerie
Konfigurace integrace TOPdesk - veřejné do služby Azure AD, potřebujete přidat TOPdesk - veřejné z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat TOPdesk - veřejné z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **TOPdesk - veřejné**, vyberte **TOPdesk - veřejné** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![TOPdesk - veřejné v seznamu výsledků](./media/topdesk-public-tutorial/tutorial_topdesk-public_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s TOPdesk – veřejné podle testovacího uživatele názvem "Britta Simon".

Pro jednotné přihlašování pro práci Azure AD je potřeba vědět, jaké příslušného uživatele v TOPdesk – veřejné je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v TOPdesk - veřejné musí navázat.

V TOPdesk - veřejným přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s TOPdesk - veřejné, je potřeba provést následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření TOPdesk - veřejné testovacího uživatele](#create-a-topdesk---public-test-user)**  – Pokud chcete mít protějšek Britta Simon v TOPdesk - veřejné propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v vaší TOPdesk - veřejné aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s TOPdesk - veřejné, proveďte následující kroky:**

1. Na portálu Azure na **TOPdesk - veřejné** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/topdesk-public-tutorial/tutorial_topdesk-public_samlbase.png)

3. Na **TOPdesk - veřejnou doménu a adresy URL** část, proveďte následující kroky:

    ![Jednotné přihlašování informace TOPdesk - veřejnou doménu a adresy URL](./media/topdesk-public-tutorial/tutorial_topdesk-public_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.topdesk.net`
    
    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.topdesk.net/tas/public/login/verify`

    c. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.topdesk.net/tas/public/login/saml`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Adresa URL odpovědi je explaned později v kurzu. Obraťte se na [TOPdesk - tým podpory veřejné klienta](https://help.topdesk.com/saas/enterprise/user/) k získání těchto hodnot.  

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/topdesk-public-tutorial/tutorial_topdesk-public_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/topdesk-public-tutorial/tutorial_general_400.png)
    
6. Na **TOPdesk - veřejné konfigurace** klikněte na tlačítko **konfigurace TOPdesk - veřejné** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![TOPdesk - veřejné konfigurace](./media/topdesk-public-tutorial/tutorial_topdesk-public_configure.png) 

7. Přihlaste se k vaší **TOPdesk - veřejné** společnosti lokality jako správce.

8. V **TOPdesk** nabídky, klikněte na tlačítko **nastavení**.
   
    ![Nastavení](./media/topdesk-public-tutorial/ic790598.png "nastavení")

9. Klikněte na tlačítko **nastavení přihlášení**.
   
    ![Nastavení přihlášení](./media/topdesk-public-tutorial/ic790599.png "nastavení přihlášení")

10. Rozbalte **nastavení přihlášení** nabídce a pak klikněte na tlačítko **Obecné**.
   
    ![Obecné](./media/topdesk-public-tutorial/ic790600.png "obecné")

11. V **veřejné** části **SAML přihlášení** konfigurace části, proveďte následující kroky:
   
    ![Technické nastavení](./media/topdesk-public-tutorial/ic790601.png "technické nastavení")
   
    a. Klikněte na tlačítko **Stáhnout** ke stažení souboru metadat veřejné a poté je uložit místně v počítači.
   
    b. Otevřete soubor stažený metadat a vyhledejte **AssertionConsumerService** uzlu.

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Kopírování **AssertionConsumerService** hodnotu, vložte tuto hodnotu v **adresa URL odpovědi** textového pole v **TOPdesk - veřejnou doménu a adresy URL** části.      
   
12. Pokud chcete vytvořit soubor s certifikátem, proveďte následující kroky:
    
    ![Certifikát](./media/topdesk-public-tutorial/ic790606.png "certifikátu")
    
    a. Otevřete soubor metadat stažený z portálu Azure.
    
    b. Rozbalte **RoleDescriptor** uzlu, který má **xsi: type** z **dodáni: ApplicationServiceType**.
    
    c. Zkopírujte hodnotu **certifikátu x 509** uzlu.
    
    d. Uložit zkopírovaný **certifikátu x 509** hodnota místně na vašem počítači v souboru.

13. V **veřejné** klikněte na tlačítko **přidat**.
    
    ![Přihlašování SAML](./media/topdesk-public-tutorial/ic790625.png "SAML přihlášení")

14. Na **pomocníka konfigurace SAML** dialogové okno proveďte následující kroky:
    
    ![Pomocník pro konfigurace SAML](./media/topdesk-public-tutorial/ic790608.png "pomocníka konfigurace SAML")
    
    a. K odeslání souboru metadat stažený z portálu Azure, v části **federačních metadat**, klikněte na tlačítko **Procházet**.

    b. Nahrát soubor certifikátu, v části **certifikát (RSA)**, klikněte na tlačítko **Procházet**.

    c. Nahrát soubor logo jste získali od týmu podpory TOPdesk, v části **Logo ikonu**, klikněte na tlačítko **Procházet**.

    d. V **atribut uživatelského jména** textovému poli, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. V **zobrazovaný název** textovému poli, zadejte název pro svou konfiguraci.

    f. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/topdesk-public-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/topdesk-public-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/topdesk-public-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/topdesk-public-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-topdesk---public-test-user"></a>Vytvoření TOPdesk - veřejné testovacího uživatele

Chcete-li povolit uživatelům Azure AD přihlášení do TOPdesk - veřejné, musí být zřízená do TOPdesk - veřejné.  
V případě TOPdesk - veřejné, zřizování je ruční úloha.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pokud chcete konfigurovat, zřizování uživatelů, proveďte následující kroky:
1. Přihlaste se k vaší **TOPdesk - veřejné** společnosti lokality jako správce.

2. V nabídce v horní části, klikněte na tlačítko **TOPdesk \> nový \> soubory podpory \> osoba**.
   
    ![Osoba](./media/topdesk-public-tutorial/ic790628.png "osoba")

3. V dialogovém okně nové osobě proveďte následující kroky:
   
    ![Nové osobě](./media/topdesk-public-tutorial/ic790629.png "nové osobě")
   
    a. Klikněte na kartu Obecné.

    b. V **Přezdívka** textovému poli, zadejte příjmení uživatele jako Simon
 
    c. Vyberte **lokality** pro účet.
 
    d. Klikněte na **Uložit**.

> [!NOTE]
> Můžete použít všechny ostatní TOPdesk - zadáním hodnoty nástroje pro tvorbu veřejné uživatelského účtu nebo rozhraní API poskytované TOPdesk - veřejné ke zřízení uživatelských účtů Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu TOPdesk - veřejné.

![Přiřadit role uživatele][200] 

**Britta Simon přiřadit TOPdesk - veřejné, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **TOPdesk - veřejné**.

    ![TOPdesk - veřejné odkaz v seznamu aplikací](./media/topdesk-public-tutorial/tutorial_topdesk-public_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Při kliknutí TOPdesk – veřejné dlaždici na přístupovém panelu, jste měli získat automaticky přihlášení k vaší TOPdesk - veřejné aplikace.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/topdesk-public-tutorial/tutorial_general_01.png
[2]: ./media/topdesk-public-tutorial/tutorial_general_02.png
[3]: ./media/topdesk-public-tutorial/tutorial_general_03.png
[4]: ./media/topdesk-public-tutorial/tutorial_general_04.png

[100]: ./media/topdesk-public-tutorial/tutorial_general_100.png

[200]: ./media/topdesk-public-tutorial/tutorial_general_200.png
[201]: ./media/topdesk-public-tutorial/tutorial_general_201.png
[202]: ./media/topdesk-public-tutorial/tutorial_general_202.png
[203]: ./media/topdesk-public-tutorial/tutorial_general_203.png

