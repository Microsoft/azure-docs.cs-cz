---
title: 'Kurz: Integrace Azure Active Directory s HR2day podle Merces | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a HR2day podle Merces.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 853d08c9-27b1-48d4-b8e7-3705140eb67f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/24/2017
ms.author: jeedes
ms.openlocfilehash: 327e6d94cbabd679531935ba330d3096d73f8897
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54821018"
---
# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>Kurz: Integrace Azure Active Directory s HR2day podle Merces

V tomto kurzu se dozvíte, jak integrovat HR2day podle Merces s Azure Active Directory (Azure AD).

Integrace HR2day podle Merces s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k HR2day podle Merces.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k HR2day podle Merces pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s HR2day podle Merces, potřebujete následující položky:

- Předplatné služby Azure AD.
- Podle Merces jednotného přihlašování HR2day povolené předplatné.

> [!NOTE]
> Nedoporučujeme používat produkčním prostředí pro testování kroky v tomto kurzu.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte vaše produkční prostředí, pokud to není nutné.
- Získání [měsíční bezplatné zkušební verze Azure AD](https://azure.microsoft.com/pricing/free-trial/) Pokud ještě nemáte ho.  

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář, který je popsaný tady se skládá ze dvou hlavních stavebních bloků:

1. Přidání HR2day podle Merces z galerie.
1. Konfigurace a testování Azure AD jednotného přihlašování.

## <a name="add-hr2day-by-merces-from-the-gallery"></a>Přidání HR2day podle Merces z Galerie
Konfigurace integrace HR2day podle Merces do služby Azure AD, přidejte HR2day podle Merces z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat HR2day podle Merces z galerie, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com), v levém navigačním podokně, vyberte **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte na **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko nahoře v dialogovém okně.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **HR2day podle Merces**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/hr2day-tutorial/tutorial_hr2daybymerces_search.png)

1. Na panelu výsledků vyberte **HR2day podle Merces**a pak vyberte **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/hr2day-tutorial/tutorial_hr2daybymerces_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s HR2day podle Merces podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, kdo tento uživatel protějšky v HR2day podle Merces je pro uživatele ve službě Azure AD. Jinými slovy budete muset vytvořit propojení mezi uživatele služby Azure AD a související uživatel v HR2day podle Merces.

V HR2day podle Merces přiřadit **uživatelské jméno** ve službě Azure AD **uživatelské jméno** a tím vytvoří vztah.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s HR2day podle Merces, které potřebujete k dokončení následujících stavebních bloků:

1. [Konfigurace služby Azure AD jednotného přihlašování](#configuring-azure-ad-single-sign-on): Umožní vašim uživatelům tuto funkci používat.
1. [Vytvořit testovacího uživatele Azure AD](#creating-an-azure-ad-test-user): Vyzkoušejte Azure AD jednotné přihlašování s Britta Simon.
1. [Vytvoření HR2day Merces testovací uživatel](#creating-an-hr2day-by-merces-test-user): Vytvoření protějšek Britta Simon v HR2day podle Merces, který je propojený s Azure AD reprezentace uživatele.
1. [Přiřadit uživatele Azure AD](#assigning-the-azure-ad-test-user): Povolte Britta Simon používat Azure AD jednotného přihlašování.
1. [Otestovat jednotné přihlašování](#testing-single-sign-on): Zkontrolujte, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování ve vašich HR2day Merces aplikací.

**Ke konfiguraci Azure AD jednotné přihlašování s HR2day podle Merces, proveďte následující kroky:**

1. Na webu Azure Portal na **HR2day podle Merces** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Pro povolení jednotného přihlašování, v **jednotného přihlašování** dialogu **režimu** jako **přihlašování na základě SAML**.
 
    ![Konfigurace jednotného přihlašování](./media/hr2day-tutorial/tutorial_hr2daybymerces_samlbase.png)

1. V **HR2day Merces domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/hr2day-tutorial/tutorial_hr2daybymerces_url.png)

    a. V **přihlašovací adresa URL** pole, zadejte adresu URL pomocí následujícímu vzoru: `https://<tenantname>.force.com/<instancename>`.

    b. V **identifikátor** pole, zadejte adresu URL pomocí následujícímu vzoru: `https://hr2day.force.com/<companyname>`.

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné přihlašovací adresu URL a identifikátor. Obraťte se [HR2day týmem podpory klienta Merces](mailto:servicedesk@merces.nl) k získání těchto hodnot. 
 


1. Na **podpisový certifikát SAML** vyberte **Certificate(Base64)** a poté uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/hr2day-tutorial/tutorial_hr2daybymerces_certificate.png) 

1. Tato část popisuje, jak povolit uživatelé ověřovat vůči HR2day podle Merces se svým účtem ve službě Azure AD. To udělají pomocí federace, která je založená na protokolu SAML.

    Vaše HR2day Merces aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který vyžaduje, můžete přidat mapování vlastních atributů do tokenu SAML. Následující snímek obrazovky ukazuje příklad tohoto objektu. 

    ![Konfigurace jednotného přihlašování](./media/hr2day-tutorial/tutorial_hr2day_00.png)
    
    > [!NOTE] 
    Než budete moct nakonfigurovat kontrolní výraz SAML, obraťte se [HR2day týmem podpory Merces klienta](mailto:servicedesk@merces.nl) a žádat o hodnotu atributu jedinečného identifikátoru pro vašeho tenanta. Je nutné tuto hodnotu k dokončení kroků v další části. 

1. V **jednotného přihlašování** v dialogu **atributy uživatele** nakonfigurujte atribut tokenu SAML, jak je znázorněno na následujícím obrázku. Pak proveďte následující kroky.
    
      | Název atributu    |   Hodnota atributu |  
    | ------------------- | -------------------- |    
    | ATTR_LOGINCLAIM | `join([mail],"102938475Z","@"` |
    
      a. Chcete-li otevřít **přidat atribut** dialogového okna, vyberte **přidat atribut**.

    ![Konfigurace jednotného přihlašování](./media/hr2day-tutorial/tutorial_attribute_04.png)

    ![Konfigurace jednotného přihlašování](./media/hr2day-tutorial/tutorial_attribute_05.png)

    b. V **název** zadejte **ATTR_LOGINCLAIM**.

    c. Z **hodnotu** seznamu vyberte **Join()**.

    d. Z **řetězec1** seznamu vyberte **user.mail**.

    e. Pro **řetězec2**, zadejte jedinečný identifikátor, který poskytuje HR2day týmu.

    f. V **oddělovač** zadejte **\@**.
    
    g. Vyberte **OK**.

1. Vyberte tlačítko **Uložit**.

    ![Konfigurace jednotného přihlašování](./media/hr2day-tutorial/tutorial_general_400.png)

1. V **HR2day konfigurací Merces** vyberte **konfigurace HR2day podle Merces** otevřít **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování URL**, **SAML Entity ID**, a **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka** oddílu.

    ![Konfigurace jednotného přihlašování](./media/hr2day-tutorial/tutorial_hr2daybymerces_configure.png) 

1. Pokud chcete nakonfigurovat jednotné přihlašování pro vaši aplikaci, obraťte se [HR2day týmem podpory klienta Merces](mailTo:servicedesk@merces.nl). Připojit na stažený **Certificate(Base64)** souborů k e-mailu. Také poskytnout **odhlašování URL**, **SAML Entity ID**, a **SAML jednotné přihlašování – adresa URL služby** tak, aby se dají konfigurovat pro integraci jednotného přihlašování.

    > [!NOTE]
    >Zmínění Merces týmu, že tato integrační potřebuje ID Entity k nastavení se vzorem **https://hr2day.force.com/INSTANCENAME**.

    > [!TIP]
    >Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory** > **podnikové aplikace** vyberte **Single Sign-On** kartu. Přejděte k vložená dokumentace prostřednictvím **konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace v [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **webu Azure portal**, v levém navigačním podokně, vyberte **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/hr2day-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a pak vyberte **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/hr2day-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogu **přidat** nahoře v dialogovém okně.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/hr2day-tutorial/create_aaduser_03.png) 

1. V **uživatele** dialogové okno pole, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/hr2day-tutorial/create_aaduser_04.png) 

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo**a zapište si heslo.

    d. Vyberte **Vytvořit**.
 
### <a name="create-an-hr2day-by-merces-test-user"></a>Vytvoření HR2day podle Merces testovacího uživatele

Cílem této části je pro vytvoření uživatele v HR2day Merces volá Britta Simon. Přidat uživatele účtu HR2day, pracovat [HR2day týmem podpory klienta Merces](mailto:servicedesk@merces.nl). 

> [!NOTE]
> Pokud je potřeba ručně vytvořit uživatele, obraťte se [HR2day týmem podpory klienta Merces](mailto:servicedesk@merces.nl).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k HR2day podle Merces.

![Přiřadit uživatele][200] 

**Přiřadit HR2day podle Merces Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikace, přejděte do zobrazení adresář a potom přejděte ke **podnikové aplikace**. V dalším kroku vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **HR2day podle Merces**.

    ![Konfigurace jednotného přihlašování](./media/hr2day-tutorial/tutorial_hr2daybymerces_app.png) 

1. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Vyberte **přidat** tlačítko. Potom v **přidat přiřazení** dialogu **uživatelů a skupin**.

    ![Přiřadit uživatele][203]

1. V **uživatelů a skupin** v dialogu **uživatelé** seznamu vyberte **Britta Simon**.

1. Klikněte na tlačítko **vyberte** tlačítko.

1. V **přidat přiřazení** dialogu **přiřadit**.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.  

Při výběru HR2day podle Merces dlaždici na přístupovém panelu, můžete automaticky získat přihlášení k vaší HR2day Merces aplikací.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací typu SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/hr2day-tutorial/tutorial_general_01.png
[2]: ./media/hr2day-tutorial/tutorial_general_02.png
[3]: ./media/hr2day-tutorial/tutorial_general_03.png
[4]: ./media/hr2day-tutorial/tutorial_general_04.png

[100]: ./media/hr2day-tutorial/tutorial_general_100.png

[200]: ./media/hr2day-tutorial/tutorial_general_200.png
[201]: ./media/hr2day-tutorial/tutorial_general_201.png
[202]: ./media/hr2day-tutorial/tutorial_general_202.png
[203]: ./media/hr2day-tutorial/tutorial_general_203.png

