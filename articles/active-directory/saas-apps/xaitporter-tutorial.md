---
title: 'Kurz: Azure Active Directory integrace s XaitPorter | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a XaitPorter.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d33c7cb7-0550-425b-882a-619a713a71b7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: jeedes
ms.openlocfilehash: ef43f7cc91467d4f9b7ab2f4cbc8c35e9b7f4586
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211789"
---
# <a name="tutorial-azure-active-directory-integration-with-xaitporter"></a>Kurz: Azure Active Directory integrace s XaitPorter

V tomto kurzu zjistěte, jak integrovat XaitPorter s Azure Active Directory (Azure AD).

Integrace XaitPorter s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k XaitPorter.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k XaitPorter (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s XaitPorter, potřebujete následující položky:

- Předplatné služby Azure AD
- XaitPorter jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání XaitPorter z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-xaitporter-from-the-gallery"></a>Přidání XaitPorter z Galerie
Při konfiguraci integrace XaitPorter do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS XaitPorter z galerie.

**Pokud chcete přidat XaitPorter z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **XaitPorter**, vyberte **XaitPorter** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![XaitPorter v seznamu výsledků](./media/xaitporter-tutorial/tutorial_xaitporter_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s XaitPorter podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v XaitPorter je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v XaitPorter musí navázat.

V XaitPorter, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s XaitPorter, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele XaitPorter](#create-a-xaitporter-test-user)**  – Pokud chcete mít protějšek Britta Simon v XaitPorter propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci XaitPorter.

**Ke konfiguraci Azure AD jednotné přihlašování s XaitPorter, proveďte následující kroky:**

1. Na portálu Azure na **XaitPorter** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/xaitporter-tutorial/tutorial_xaitporter_samlbase.png)

3. Na **XaitPorter domény a adresy URL** část, proveďte následující kroky:

    ![XaitPorter domény a adresy URL jednotné přihlašování informace](./media/xaitporter-tutorial/tutorial_xaitporter_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.xaitporter.com/saml/login`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.xaitporter.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory XaitPorter klienta](https://www.xait.com/support/) k získání těchto hodnot.
     
4. Na **SAML podpisový certifikát** části, klikněte na tlačítko Kopírovat kopírování **adresu Url aplikace federační Metadata** a vložte do poznámkového bloku. 

    ![Odkaz ke stažení certifikátu](./media/xaitporter-tutorial/tutorial_xaitporter_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/xaitporter-tutorial/tutorial_general_400.png)

6. Zadejte **IP adresu** nebo **adresu Url aplikace federační Metadata** k [SmartRecruiters tým podpory](https://www.smartrecruiters.com/about-us/contact-us/)tak, aby XaitPorter můžete zajistit, že IP adresa je dostupný z vašeho Konfigurace seznamu povolených IP adres na jejich stranu XaitPorter instance. 

7. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti XaitPorter jako správce.

8. Klikněte na **správce**.

    ![Konfigurovat jednotné přihlašování](./media/xaitporter-tutorial/user1.png)

9. Vyberte **správě jednotného přihlašování** z **nastavení systému** rozevíracího seznamu.

    ![Konfigurovat jednotné přihlašování](./media/xaitporter-tutorial/user2.png)

10. V **SPRAVOVAT jeden přihlašování** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/xaitporter-tutorial/user3.png)

    a. Vyberte **povolit jednotné přihlašování, ověřování**.

    b. V **nastavení zprostředkovatele Identity** textovému poli, vložte **adresu Url aplikace federační Metadata** který jste zkopírovali z portálu Azure a klikněte na tlačítko **načíst**.

    c. Vyberte **povolit automatické vytvoření uživatelů**.

    d. Klikněte na **OK**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/xaitporter-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/xaitporter-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/xaitporter-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/xaitporter-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-xaitporter-test-user"></a>Vytvoření zkušebního uživatele XaitPorter

V této části vytvoříte volal Britta Simon v XaitPorter uživatele. Práce s [tým podpory XaitPorter klienta](https://www.xait.com/support/) přidat uživatele do XaitPorter platformy. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu XaitPorter.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon XaitPorter, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **XaitPorter**.

    ![V seznamu aplikací na XaitPorter odkaz](./media/xaitporter-tutorial/tutorial_xaitporter_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici XaitPorter na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci XaitPorter.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/xaitporter-tutorial/tutorial_general_01.png
[2]: ./media/xaitporter-tutorial/tutorial_general_02.png
[3]: ./media/xaitporter-tutorial/tutorial_general_03.png
[4]: ./media/xaitporter-tutorial/tutorial_general_04.png

[100]: ./media/xaitporter-tutorial/tutorial_general_100.png

[200]: ./media/xaitporter-tutorial/tutorial_general_200.png
[201]: ./media/xaitporter-tutorial/tutorial_general_201.png
[202]: ./media/xaitporter-tutorial/tutorial_general_202.png
[203]: ./media/xaitporter-tutorial/tutorial_general_203.png

