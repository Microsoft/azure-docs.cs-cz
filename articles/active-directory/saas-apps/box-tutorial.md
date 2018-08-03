---
title: 'Kurz: Integrace Azure Active Directory s polem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a pole.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3b565c8d-35e2-482a-b2f4-bf8fd7d8731f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: f5aa724e9848c9794eef093aef15b0aaed9cae97
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435756"
---
# <a name="integrate-azure-active-directory-with-box"></a>Integrace Azure Active Directory s poli

V tomto kurzu se dozvíte, jak integrovat Azure Active Directory (Azure AD) s polem.

Díky integraci služby Azure AD s poli, získáte následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k poli.
- Můžete povolit uživatelům získat přihlášení automaticky do pole (jednotné přihlašování a jednotné přihlašování) pomocí svých účtů služby Azure AD.
- Můžete spravovat své účty v jednom centrálním místě na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s polem, potřebujete následující položky:

- S předplatným služby Azure AD
- Předplatné povoleno jednotné přihlašování pole

> [!NOTE]
> Při testování kroky v tomto kurzu doporučujeme, abyste udělali *není* použijte produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. 

Scénář, který je popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání pole z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-box-from-the-gallery"></a>Přidejte pole z Galerie
Konfigurace integrace Azure AD s polem, přidejte pole z Galerie váš seznam spravovaných aplikací SaaS následujícím způsobem:

1. V [webu Azure portal](https://portal.azure.com), v levém podokně vyberte **Azure Active Directory**. 

    ![Tlačítko Azure Active Directory][1]

1. Vyberte **podnikové aplikace** > **všechny aplikace**.

    ![V okně "Podnikové aplikace"][2]
    
1. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko v horní části okna.

    !["Nové aplikace" tlačítko][3]

1. Do vyhledávacího pole zadejte **pole**vyberte **pole** v seznamu výsledků a pak vyberte **přidat**.

    ![Pole v seznamu výsledků](./media/box-tutorial/tutorial_box_search.png)
### <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s polem, podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje k identifikaci uživatele pole a jeho protějšek ve službě Azure AD. Jinými slovy musí vytvořit vztah odkazu mezi uživatele služby Azure AD a stejného uživatele v poli.

K navázání vztahu odkazu, přiřaďte jako pole *uživatelské jméno* hodnotu *uživatelské jméno* ve službě Azure AD.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s polem, dokončete stavební bloky v příštích pěti oddílů.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci pole následujícím způsobem:

1. Na webu Azure Portal v **pole** okno integrace aplikace, vyberte **jednotného přihlašování**.

    ![Odkaz "Jednotné přihlašování"][4]

1. V **jednotného přihlašování** okno v **režim jednotného přihlašování** vyberte **přihlašování na základě SAML**.
 
    ![V okně "Jednotné přihlašování"](./media/box-tutorial/tutorial_box_samlbase.png)

1. V části **pole domény a adresy URL**, postupujte takto:

    !["Pole domény a adresy URL" jednotné přihlašování – informace](./media/box-tutorial/url3.png)

    a. V **přihlašovací adresa URL** pole, zadejte adresu URL v následujícím formátu: *https://\<subdoména >. box.com*.

    b. V **identifikátor** textové pole, typ **box.net**.
     
    > [!NOTE] 
    > Předchozí hodnoty nejsou skutečný. Aktualizujte je s skutečné přihlašovací adresu URL a identifikátorem. K získání hodnot, obraťte se [pole tým podpory klienta](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire). 

1. V části **podpisový certifikát SAML**vyberte **soubor XML s metadaty**a poté uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/box-tutorial/tutorial_box_certificate.png) 

1. Vyberte **Uložit**.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/box-tutorial/tutorial_general_400.png)
    
1. Pokud chcete nakonfigurovat jednotné přihlašování pro vaši aplikaci, postupujte podle pokynů v [nastavit jednotné přihlašování na vlastní](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown).

> [!NOTE] 
> Pokud nastavení jednotného přihlašování nejde povolit pro svůj účet boxu, možná budete muset kontaktovat [pole tým podpory klienta](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire) a stažený soubor XML.

> [!TIP]
> Jak při nastavování aplikace, můžete si přečíst stručné verzi podle předchozích pokynů v [webu Azure portal](https://portal.azure.com). Po přidání aplikace ve **služby Active Directory** > **podnikové aplikace** vyberte **Single Sign-On** kartu a potom přejdete dokumentace ke službě v Embedded **konfigurace** oblast v dolní části. Další informace o funkci vložený dokumentaci najdete v tématu [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985).
>

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části můžete vytvořit testovacího uživatele Britta Simon na webu Azure Portal následujícím způsobem:

![Vytvořit testovacího uživatele Azure AD][100]

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**.

    ![Propojení služby Azure Active Directory](./media/box-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam aktuálních uživatelů, vyberte **uživatelů a skupin** > **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/box-tutorial/create_aaduser_02.png)

1. V horní části **všichni uživatelé** okně **přidat**.

    ![Tlačítko Přidat](./media/box-tutorial/create_aaduser_03.png)

    **Uživatele** otevře se okno.

1. V **uživatele** okno, postupujte takto:

    ![V okně uživatele](./media/box-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-box-test-user"></a>Vytvoření zkušebního uživatele pole

V této části můžete vytvořit testovacího uživatele Britta Simon pole. Pole podporuje just-in-time zřizování, který je ve výchozím nastavení povolené. Pokud uživatel ještě neexistuje, vytvoří se nový při pokusu o přístup k poli. Nemusíte nic dělat z k vytvoření uživatele.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolit uživatele Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k poli. Chcete-li to provést, postupujte takto:

![Přiřazení role uživatele][200]

1. Na webu Azure Portal, otevřete **aplikací** zobrazení, přejděte na **Directory** zobrazit a pak vyberte **podnikové aplikace** > **všechny aplikace**.

    !["Podnikové aplikace" a "Všechny aplikace" odkazy][201] 

1. V **aplikací** seznamu vyberte **pole**.

    ![Odkaz pole](./media/box-tutorial/tutorial_box_app.png)  

1. V levém podokně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Vyberte **přidat** a pak na **přidat přiřazení** vyberte **uživatelů a skupin**.

    ![Podokno Přidat přiřazení][203]

1. V **uživatelů a skupin** okno v **uživatelé** seznamu vyberte **Britta Simon**.

1. Vyberte **vyberte** tlačítko.

1. V **přidat přiřazení** okně **přiřadit**.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když vyberete **pole** dlaždici na přístupovém panelu, můžete otevřít přihlašovací stránku pro přihlášení k aplikaci Box.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS se službou Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace zřizování uživatelů](box-userprovisioning-tutorial.md)



<!--Image references-->

[1]: ./media/box-tutorial/tutorial_general_01.png
[2]: ./media/box-tutorial/tutorial_general_02.png
[3]: ./media/box-tutorial/tutorial_general_03.png
[4]: ./media/box-tutorial/tutorial_general_04.png

[100]: ./media/box-tutorial/tutorial_general_100.png

[200]: ./media/box-tutorial/tutorial_general_200.png
[201]: ./media/box-tutorial/tutorial_general_201.png
[202]: ./media/box-tutorial/tutorial_general_202.png
[203]: ./media/box-tutorial/tutorial_general_203.png

