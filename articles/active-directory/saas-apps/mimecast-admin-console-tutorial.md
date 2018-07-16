---
title: 'Kurz: Integrace Azure Active Directory pomocí konzoly pro správu Mimecast | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Mimecast konzoly pro správu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: jeedes
ms.openlocfilehash: 27c9606357d9599fa56e4045606f8d9046722e7f
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041717"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Kurz: Integrace Azure Active Directory pomocí konzoly pro správu Mimecast

V tomto kurzu se dozvíte, jak integrovat Mimecast konzoly pro správu Azure Active Directory (Azure AD).

Integrace konzoly pro správu Mimecast s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup ke konzole pro správu Mimecast.
- Uživatele, aby automaticky získat přihlášení k Mimecast konzoly pro správu (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí konzoly pro správu Mimecast, potřebujete následující položky:

- S předplatným služby Azure AD
- Konzoly pro správu Mimecast jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Mimecast konzoly pro správu z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>Přidání Mimecast konzoly pro správu z Galerie
Konfigurace integrace Mimecast konzoly pro správu do služby Azure AD, budete muset přidat Mimecast konzoly pro správu z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Mimecast konzoly pro správu z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **konzoly pro správu Mimecast**vyberte **konzoly pro správu Mimecast** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Mimecast konzoly pro správu v seznamu výsledků](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí konzoly pro správu Mimecast podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v konzole pro správu Mimecast je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v konzole pro správu Mimecast potřeba navázat.

V konzole pro správu Mimecast, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Mimecast konzoly pro správu, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele konzoly pro správu Mimecast](#create-a-mimecast-admin-console-test-user)**  – Pokud chcete mít protějšek Britta Simon Mimecast konzoly pro správu, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Mimecast konzoly pro správu.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí konzoly pro správu Mimecast, proveďte následující kroky:**

1. Na webu Azure Portal na **konzoly pro správu Mimecast** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_samlbase.png)

3. Na **Mimecast správce konzoly domény a adresy URL** části, proveďte následující kroky:

    ![Mimecast správce konzoly domény a adresy URL jednotného přihlašování – informace](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL:
    | |
    | -- |
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|

    > [!NOTE] 
    > Přihlášení na adrese URL se liší podle oblastí.

4. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/mimecast-admin-console-tutorial/tutorial_general_400.png)

6. Na **konfigurace konzoly Správce Mimecast** klikněte na tlačítko **konfigurace konzoly pro správu Mimecast** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace konzoly Správce Mimecast](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_configure.png) 

7. V okně jiný webový prohlížeč Přihlaste se ke konzole správce Mimecast jako správce.

8. Přejděte na **služby \> aplikace**.

    ![Služby](./media/mimecast-admin-console-tutorial/ic794998.png "služby")

9. Klikněte na tlačítko **ověřování profilů**.

    ![Ověřování profilů](./media/mimecast-admin-console-tutorial/ic794999.png "ověřování profilů")
    
10. Klikněte na tlačítko **nový profil ověřování**.

    ![Nové profily ověřování](./media/mimecast-admin-console-tutorial/ic795000.png "nové profily ověřování")

11. V **ověřování profilu** části, proveďte následující kroky:

    ![Profil ověření](./media/mimecast-admin-console-tutorial/ic795015.png "profilu ověření systému")
    
    a. V **popis** textového pole zadejte název pro vaši konfiguraci.
    
    b. Vyberte **vynutit ověřování SAML pro konzolu Správce Mimecast**.
    
    c. Jako **poskytovatele**vyberte **Azure Active Directory**.
    
    d. Vložit **SAML Entity ID**, který jste zkopírovali z portálu Azure portal do **URL vystavitele** textového pole.
    
    e. Vložit **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal do **přihlašovací adresa URL** textového pole.

    f. Vložit **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal do **odhlašovací adresa URL** textového pole.
    
    >[!NOTE]
    >Hodnota adresy URL pro přihlášení a hodnota odhlašovací adresa URL jsou pro konzolu Správce Mimecast stejné.
    
    g. Otevřít certifikát base-64 stáhnout z webu Azure portal v programu Poznámkový blok, odeberte první řádek ("*--*") a poslední řádek ("*--*"), zkopírujte ho do zbývající obsah vaší schránky a vložte ho do **certifikát zprostředkovatele Identity (Metadata)** textového pole.
    
    h. Vyberte **povolit jednotné přihlašování na**.
    
    i. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985) 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/mimecast-admin-console-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/mimecast-admin-console-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/mimecast-admin-console-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/mimecast-admin-console-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-mimecast-admin-console-test-user"></a>Vytvoření zkušebního uživatele konzoly pro správu Mimecast

Chcete-li povolit uživatele Azure AD pro přihlášení ke konzole pro správu Mimecast, musí být poskytnuty do konzoly pro správu Mimecast. V případě Mimecast konzoly pro správu zřizování se ruční úlohy.

* Budete muset zaregistrovat k doméně, než budete moct vytvořit uživatele.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **konzoly pro správu Mimecast** jako správce.
2. Přejděte na **adresáře \> interní**.
   
   ![Adresáře](./media/mimecast-admin-console-tutorial/ic795003.png "adresáře")
3. Klikněte na tlačítko **zaregistrovat novou doménu**.
   
   ![Zaregistrujte novou doménu](./media/mimecast-admin-console-tutorial/ic795004.png "zaregistrovat nové domény")
4. Po vytvoření nové domény, klikněte na tlačítko **novou adresu**.
   
   ![Nová adresa](./media/mimecast-admin-console-tutorial/ic795005.png "novou adresu")
5. V dialogovém okně Nový adresy proveďte následující kroky:
   
   ![Uložit](./media/mimecast-admin-console-tutorial/ic795006.png "uložit")
   
   a. Typ **e-mailovou adresu**, **globální název**, **heslo**, a **potvrzení hesla** účtu atributů platné Azure AD, který jste chcete zřízení související textových polí.

   b. Klikněte na **Uložit**.

>[!NOTE]
>Další nástroje pro tvorbu účtu uživatele konzoly pro správu Mimecast nebo rozhraní API poskytovaných Mimecast konzoly pro správu můžete použít ke zřízení uživatelských účtů služby Azure AD. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu ke konzole správce Mimecast.

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Mimecast konzoly pro správu, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **konzoly pro správu Mimecast**.

    ![Na konzole pro správu Mimecast odkaz v seznamu aplikací](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Mimecast konzoly pro správu na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci konzoly pro správu Mimecast.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/mimecast-admin-console-tutorial/tutorial_general_01.png
[2]: ./media/mimecast-admin-console-tutorial/tutorial_general_02.png
[3]: ./media/mimecast-admin-console-tutorial/tutorial_general_03.png
[4]: ./media/mimecast-admin-console-tutorial/tutorial_general_04.png

[100]: ./media/mimecast-admin-console-tutorial/tutorial_general_100.png

[200]: ./media/mimecast-admin-console-tutorial/tutorial_general_200.png
[201]: ./media/mimecast-admin-console-tutorial/tutorial_general_201.png
[202]: ./media/mimecast-admin-console-tutorial/tutorial_general_202.png
[203]: ./media/mimecast-admin-console-tutorial/tutorial_general_203.png

