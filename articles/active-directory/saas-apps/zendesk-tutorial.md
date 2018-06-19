---
title: 'Kurz: Azure Active Directory integrace s Zendesk | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a služby Zendesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 1f1f61fa416a5fc57fbf76f7ae401818aa6e97db
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35903279"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Kurz: Azure Active Directory integrace s Zendesk

V tomto kurzu zjistěte, jak integrovat Zendesk s Azure Active Directory (Azure AD).

Integrace služby Zendesk s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k této služby.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Zendesk (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Zendesk, potřebujete následující položky:

- Předplatné služby Azure AD
- Této služby jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Zendesk z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-zendesk-from-the-gallery"></a>Přidání Zendesk z Galerie
Při konfiguraci integrace služby Zendesk do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Zendesk z galerie.

**Pokud chcete přidat Zendesk z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Zendesk**, vyberte **Zendesk** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Zendesk v seznamu výsledků](./media/zendesk-tutorial/tutorial_zendesk_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části konfiguraci a testování Azure AD jednotné přihlašování pomocí služby Zendesk podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co příslušného uživatele v této služby je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatele v této služby je nutné stanovit.

V této služby, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Zendesk, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Zendesk](#create-a-zendesk-test-user)**  – Pokud chcete mít protějšek Britta Simon v Zendesku propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci služby Zendesk.

**Ke konfiguraci Azure AD jednotné přihlašování s Zendesk, proveďte následující kroky:**

1. Na portálu Azure na **Zendesk** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/zendesk-tutorial/tutorial_zendesk_samlbase.png)

3. Na **Zendesk domény a adresy URL** část, proveďte následující kroky:

    ![Zendesk domény a adresy URL jednotné přihlašování informace](./media/zendesk-tutorial/tutorial_zendesk_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.zendesk.com`

    b. V **identifikátor** textovému poli, zadejte hodnotu pomocí následujícího vzorce: `<subdomain>.zendesk.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory služby Zendesk klienta](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) k získání těchto hodnot.

4. Na **SAML podpisový certifikát** část, zkopírujte **kryptografický OTISK** hodnota certifikátu.

    ![Odkaz ke stažení certifikátu](./media/zendesk-tutorial/tutorial_zendesk_certificate.png)

5. Zendesk očekává SAML kontrolní výrazy ve specifickém formátu. Neexistují žádné povinné atributy SAML, ale můžete volitelně přidat atribut z **uživatelské atributy** části podle následujících kroků: 

     ![Konfigurovat jednotné přihlašování](./media/zendesk-tutorial/tutorial_zendesk_attributes1.png)

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování přidat](./media/zendesk-tutorial/tutorial_attribute_04.png)

    ![Konfigurovat jednotné přihlašování addattb](./media/zendesk-tutorial/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.
    
    d. Klikněte na tlačítko **OK**.

    > [!NOTE]
    > Atributy rozšíření je použít k přidání atributů, které nejsou ve službě Azure AD ve výchozím nastavení. Klikněte na tlačítko [atributy uživatele, které lze nastavit v SAML](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) získat úplný seznam SAML atributy, které **Zendesk** přijímá.

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/zendesk-tutorial/tutorial_general_400.png)

7. Na **Zendesk konfigurace** klikněte na tlačítko **konfigurace Zendesk** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL a SAML jeden přihlašování služby URL** z **Stručná referenční příručka části.**

    ![Konfigurace služby Zendesk](./media/zendesk-tutorial/tutorial_zendesk_configure.png) 

8. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti Zendesk.

9. Klikněte na tlačítko **správce**.

10. V levém navigačním podokně klikněte na tlačítko **nastavení**a potom klikněte na **zabezpečení**.

11. Na **zabezpečení** proveďte následující kroky: 

     ![Zabezpečení](./media/zendesk-tutorial/ic773089.png "zabezpečení")

    ![Jednotné přihlašování](./media/zendesk-tutorial/ic773090.png "jednotného přihlašování")

     a. Klikněte **správce & agenty** kartě.

     b. Vyberte **jednotné přihlašování (SSO) a SAML**a potom vyberte **SAML**.

     c. V **URL jednotné přihlašování SAML** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure. 

     d. V **vzdálené adresy URL odhlašovací** textovému poli, vložte hodnotu **Sign-Out URL** který jste zkopírovali z portálu Azure.

     e. V **otisků prstů certifikátů** textovému poli, Vložit **kryptografický otisk** hodnota certifikát, který jste zkopírovali z portálu Azure.

     f. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/zendesk-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/zendesk-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/zendesk-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/zendesk-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-zendesk-test-user"></a>Vytvoření zkušebního uživatele Zendesk

Cílem této části je vytvoření uživatele volal Britta Simon v této služby. Zendesk podporuje automatické uživatele zřizování, který je ve výchozím nastavení povolené. Můžete najít další podrobnosti o [sem](zendesk-provisioning-tutorial.md) na tom, jak nakonfigurovat uživatele automatické zřizování.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

> [!NOTE]
> **Koncový uživatel** účty jsou zřízené automaticky při přihlášení. **Agent** a **správce** účty muset ručně zřídit v **Zendesk** před přihlášením.

1. Přihlaste se k vaší **Zendesk** klienta.

2. Vyberte **seznamu zákazníků** kartě.

3. Vyberte **uživatele** a klikněte na **přidat**.

    ![Přidat uživatele](./media/zendesk-tutorial/ic773632.png "přidat uživatele")
4. Typ **název** a **e-mailu** stávajícího účtu Azure AD určené ke zřízení a potom klikněte na **Uložit**.

    ![Nový uživatel](./media/zendesk-tutorial/ic773633.png "nového uživatele")

> [!NOTE]
> Můžete použít všechny ostatní Zendesk uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované Zendesk zřídit AAD uživatelské účty.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k této služby.

![Přiřadit role uživatele][200]

**Pokud chcete přiřadit Britta Simon Zendesk, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **Zendesk**.

    ![V seznamu aplikací na Zendesk odkaz](./media/zendesk-tutorial/tutorial_zendesk_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici služby Zendesk na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci služby Zendesk.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfiguraci zřizování uživatelů](zendesk-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
[4]: ./media/zendesk-tutorial/tutorial_general_04.png

[100]: ./media/zendesk-tutorial/tutorial_general_100.png

[200]: ./media/zendesk-tutorial/tutorial_general_200.png
[201]: ./media/zendesk-tutorial/tutorial_general_201.png
[202]: ./media/zendesk-tutorial/tutorial_general_202.png
[203]: ./media/zendesk-tutorial/tutorial_general_203.png
