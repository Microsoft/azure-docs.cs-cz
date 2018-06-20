---
title: 'Kurz: Azure Active Directory integrace se systémy Pega | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Pega systémy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: 005f487b46ca3894ed2d94ec032bb009c3051796
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36223378"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Kurz: Azure Active Directory integrace s Pega systémy

V tomto kurzu zjistěte, jak integrovat systémy Pega s Azure Active Directory (Azure AD).

Integrace Pega systémy s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k systémům Pega.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Pega systémy (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Pega systémy, potřebujete následující položky:

- Předplatné služby Azure AD
- Systémy Pega jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Pega systémy z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-pega-systems-from-the-gallery"></a>Přidání Pega systémy z Galerie
Pokud chcete nakonfigurovat integraci systémů Pega do služby Azure AD, potřebujete přidat systémy Pega z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat Pega systémy z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Pega systémy**, vyberte **Pega systémy** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Systémy Pega v seznamu výsledků](./media/pegasystems-tutorial/tutorial_pegasystems_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Pega systémy založené na testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v systémech Pega je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v systémech Pega musí navázat.

V systémech Pega přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Pega systémy, musíte dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele systémy Pega](#create-a-pega-systems-test-user)**  – Pokud chcete mít protějšek Britta Simon v systémech Pega, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Pega systémy.

**Ke konfiguraci Azure AD jednotné přihlašování s Pega systémy, proveďte následující kroky:**

1. Na portálu Azure na **Pega systémy** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/pegasystems-tutorial/tutorial_pegasystems_samlbase.png)

3. Na **Pega systémy domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Pega systémy domény a adresy URL jednotné přihlašování informace](./media/pegasystems-tutorial/tutorial_pegasystems_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Pega systémy domény a adresy URL jednotné přihlašování informace](./media/pegasystems-tutorial/tutorial_pegasystems_url1.png)

    V **předávání stavu** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<CUSTOMERNAME>.pegacloud.io/prweb/sso`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte s skutečné identifikátor, adresa URL odpovědi a předávání stavu adresy URL. Můžete získat hodnoty identifikátor a adresa URL odpovědi z Pega aplikace, které se vysvětluje dále v tomto kurzu. Pro stav předávání, obraťte se na [tým podpory klientské systémy Pega](https://www.pega.com/contact-us) k získání hodnoty. 

5. Aplikace Pega systémy očekává SAML kontrolní výrazy ve specifickém formátu, který vyžaduje, můžete přidat mapování vlastních atributů do vaší konfigurace atributy tokenu SAML. Tyto deklarace jsou specifické pro odběratele a závisí na váš požadavek. Následující volitelné deklarace identity jsou příklad pouze které můžete nakonfigurovat pro vaši aplikaci. Můžete spravovat hodnoty těchto atributů z "**uživatelské atributy**" části na stránce integrace aplikace. 

    ![Konfigurovat jednotné přihlašování](./media/pegasystems-tutorial/tutorial_attribute.png)

6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je vidět na předchozím obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ------------------- | -------------------- |    
    | UID | *********** |
    | CN  | *********** |
    | E-mailu | *********** |
    | přístupové skupiny | *********** |
    | Organizace | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | Pracovní skupiny | *********** |
    | Telefon | *********** |

    > [!NOTE]
    > Toto jsou konkrétní hodnoty zákazníka. Zadejte příslušné hodnoty.

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/pegasystems-tutorial/tutorial_attribute_04.png)

    ![Konfigurovat jednotné přihlašování](./media/pegasystems-tutorial/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.
    
    d. Klikněte na tlačítko **OK**.

7. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/pegasystems-tutorial/tutorial_pegasystems_certificate.png) 
8. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/pegasystems-tutorial/tutorial_general_400.png)
    
9. Konfigurace jednotného přihlašování na **Pega systémy** straně, otevřete **Pega portál** pomocí účtu správce v jiném okně prohlížeče.

10. Vyberte **vytvořit** -> **SysAdmin** -> **ověřovací služby**.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
11. Proveďte následující akce v **vytvořit službu Aauthentication** obrazovky:

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. Vyberte **SAML 2.0** z typu

    b. V **název** textovému poli, zadejte všechny název např Azure AD SSO

    c. V **krátký popis** textovému poli, zadejte všechny popis  

    d. Klikněte na **vytvořit a otevřete** 
    
12. V **informace o poskytovateli Identity (IdP)** části, klikněte na **IdP importovat metadata** a vyhledejte soubor metadat, který jste si stáhli z portálu Azure. Klikněte na tlačítko **odeslání** načíst metadata.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
13. To bude naplnění dat IdP, jak je uvedeno níže.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
14. Proveďte následující akce v **nastavení služby zprostředkovatele (SP)** části:

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. Kopírování **Entity identifikace** a vložte zpět na portálu Azure **identifikátor** textové pole.

    b.  Kopírování **Assertion příjemce Service (ACS) umístění** a vložte zpět na portálu Azure **adresa URL odpovědi** textové pole.

    c. Vyberte **zakázat žádost o podepsání**.

15. Klikněte na **Uložit**.
    
> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/pegasystems-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/pegasystems-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/pegasystems-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/pegasystems-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-pega-systems-test-user"></a>Vytvoření zkušebního uživatele Pega systémy

Cílem této části je vytvoření uživatele volal Britta Simon v Pega systémy. Spojte se s [tým podpory klientské systémy Pega](https://www.pega.com/contact-us) pro vytváření uživatelů v Pega Sysyems.


### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k systémům Pega.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Pega systémy, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Pega systémy**.

    ![V seznamu aplikací na odkaz Pega systémy](./media/pegasystems-tutorial/tutorial_pegasystems_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Pega systémy na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Pega systémy.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/pegasystems-tutorial/tutorial_general_01.png
[2]: ./media/pegasystems-tutorial/tutorial_general_02.png
[3]: ./media/pegasystems-tutorial/tutorial_general_03.png
[4]: ./media/pegasystems-tutorial/tutorial_general_04.png

[100]: ./media/pegasystems-tutorial/tutorial_general_100.png

[200]: ./media/pegasystems-tutorial/tutorial_general_200.png
[201]: ./media/pegasystems-tutorial/tutorial_general_201.png
[202]: ./media/pegasystems-tutorial/tutorial_general_202.png
[203]: ./media/pegasystems-tutorial/tutorial_general_203.png

