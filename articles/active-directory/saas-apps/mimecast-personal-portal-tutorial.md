---
title: 'Kurz: Azure Active Directory integrace s Mimecast osobní portál | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Mimecast osobní portál.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: jeedes
ms.openlocfilehash: a1c1f0a3f3a742c1aa5baa55db1796b3a2ae7802
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35899211"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Kurz: Azure Active Directory integrace s Mimecast osobní portálu

V tomto kurzu zjistěte, jak integrovat Mimecast osobní portálu v Azure Active Directory (Azure AD).

Integrace portálu osobní Mimecast s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k portálu osobní Mimecast.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k portálu osobní Mimecast (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD pomocí portálu osobní Mimecast, potřebujete následující položky:

- Předplatné služby Azure AD
- Portálu osobní Mimecast jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání osobní portálu Mimecast z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Přidání osobní portálu Mimecast z Galerie
Při konfiguraci integrace Mimecast osobní portálu do služby Azure AD, potřebujete přidat osobní portál Mimecast z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat osobní portál Mimecast z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **osobní portál Mimecast**, vyberte **osobní portál Mimecast** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Osobní portál Mimecast v seznamu výsledků](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Mimecast osobní portál podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějšku osobní portálu Mimecast je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské Mimecast osobní portálu musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Mimecast osobní portál, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele osobní portál Mimecast](#create-a-mimecast-personal-portal-test-user)**  – Pokud chcete mít protějšek Britta Simon v Mimecast osobní portál, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Mimecast osobní portál.

**Ke konfiguraci Azure AD jednotné přihlašování s Mimecast osobní portál, proveďte následující kroky:**

1. Na portálu Azure na **osobní portál Mimecast** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_samlbase.png)

3. Na **Mimecast osobní portál domény a adresy URL** část, proveďte následující kroky:

    ![Mimecast osobní portál domény a adresy URL jednotné přihlašování informace](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL: 

    | Oblast  |  Hodnota | 
    | --------------- | --------------- | 
    | Evropa          | `https://eu-api.mimecast.com/login/saml`|
    | Spojené státy   | `https://us-api.mimecast.com/login/saml`|
    | Jihoafrická republika    | `https://za-api.mimecast.com/login/saml`|
    | Austrálie       | `https://au-api.mimecast.com/login/saml`|
    | Příbřežních        | `https://jer-api.mimecast.com/login/saml`|

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce:

    | Oblast  |  Hodnota | 
    | --------------- | --------------- |
    | Evropa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Spojené státy   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | Jihoafrická republika    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Austrálie       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Příbřežních        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. V **adresa URL odpovědi** textovému poli, zadejte adresu URL: 

    | Oblast  |  Hodnota | 
    | --------------- | --------------- | 
    | Evropa          | `https://eu-api.mimecast.com/login/saml`|
    | Spojené státy   | `https://us-api.mimecast.com/login/saml`|
    | Jihoafrická republika    | `https://za-api.mimecast.com/login/saml`|
    | Austrálie       | `https://au-api.mimecast.com/login/saml`|
    | Příbřežních        | `https://jer-api.mimecast.com/login/saml`|
    
    > [!NOTE] 
    > Hodnota identifikátoru není skutečné. Aktualizujte hodnotu skutečným identifikátorem. Obraťte se na [tým podpory Mimecast osobní portál klienta](http://www.mimecast.com/customer-success/technical-support/) k získání hodnoty. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/mimecast-personal-portal-tutorial/tutorial_general_400.png)

6. Na **osobní konfigurace portálu Mimecast** klikněte na tlačítko **nakonfigurovat portál osobní Mimecast** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace portálu Mimecast osobní](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se k portálu osobní Mimecast jako správce.

8. Přejděte na **služby \> aplikace**.
   
    ![Aplikace](./media/mimecast-personal-portal-tutorial/ic794998.png "aplikace")

9. Klikněte na tlačítko **ověřování profily**.
   
    ![Profily ověřování](./media/mimecast-personal-portal-tutorial/ic794999.png "profily ověřování")

10. Klikněte na tlačítko **nový profil ověřování**.
   
    ![Nový profil ověřování](./media/mimecast-personal-portal-tutorial/ic795000.png "nový profil ověřování")

11. V **profil ověření** část, proveďte následující kroky:
   
    ![Profil ověření](./media/mimecast-personal-portal-tutorial/ic795001.png "profil ověření")
   
    a. V **popis** textovému poli, zadejte název pro svou konfiguraci.
   
    b. Vyberte **vynutit ověřování SAML pro portál Mimecast osobní**.
   
    c. Jako **zprostředkovatele**, vyberte **Azure Active Directory**.
   
    d. V **URL vystavitele** textovému poli, vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure.
   
    e. V **přihlašovací adresa URL** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.
   
    f. V **adresy URL odhlašovací** textovému poli, vložte hodnotu **Sign-Out URL**, který jste zkopírovali z portálu Azure.

    g. Otevřete váš **kódování base-64** kódovaného certifikátu v poznámkovém bloku stáhli z portálu Azure, zkopírujte obsah ho do schránky a vložte jej do **certifikát zprostředkovatele Identity (Metadata)** textové pole.

    h. Vyberte **povolit jednotné přihlašování na**.
   
    i. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/mimecast-personal-portal-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/mimecast-personal-portal-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/mimecast-personal-portal-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/mimecast-personal-portal-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-mimecast-personal-portal-test-user"></a>Vytvoření zkušebního uživatele Mimecast osobní portálu

Pokud chcete povolit uživatelům Azure AD přihlášení na portál osobní Mimecast, musí být zřízená Mimecast osobní portálu. V případě Mimecast osobní portál je zřizování ruční úloha.

Budete muset registraci domény, před vytvořením uživatele.

**Pokud chcete konfigurovat, zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **Mimecast osobní portál** jako správce.

2. Přejděte na **adresáře \> interní**.
   
    ![Adresáře](./media/mimecast-personal-portal-tutorial/ic795003.png "adresáře")

3. Klikněte na tlačítko **zaregistrovat nové domény**.
   
    ![Zaregistrujte novou doménu](./media/mimecast-personal-portal-tutorial/ic795004.png "zaregistrovat nové domény")

4. Po vytvoření nové domény, klikněte na tlačítko **novou adresu**.
   
    ![Nové adresy](./media/mimecast-personal-portal-tutorial/ic795005.png "novou adresu")

5. V dialogovém okně nové adresy, proveďte následující kroky platný Azure chcete zřídit účet AD:
   
    ![Uložit](./media/mimecast-personal-portal-tutorial/ic795006.png "uložit")
   
    a. V **e-mailovou adresu** textovému poli, typ **e-mailovou adresu** uživatele jako **BrittaSimon@contoso.com**.
    
    b. V **globální název** textovému poli, typ **uživatelské jméno** jako **BrittaSimon**.

    c. V **heslo**, a **Potvrdit heslo** textová pole, zadejte **heslo** uživatele.
   
    b. Klikněte na **Uložit**.

>[!NOTE]
>Další nástroje pro tvorbu účet uživatele Mimecast osobní portál nebo rozhraní API poskytovaných Mimecast osobní portálu můžete použít ke zřízení uživatelských účtů Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat tak, že udělíte přístup na portál osobní Mimecast Azure jednotné přihlašování.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Mimecast osobní portál, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Mimecast osobní portál**.

    ![Odkaz Mimecast osobní portál v seznamu aplikací](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Mimecast osobní portál na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Mimecast osobní portál.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/mimecast-personal-portal-tutorial/tutorial_general_01.png
[2]: ./media/mimecast-personal-portal-tutorial/tutorial_general_02.png
[3]: ./media/mimecast-personal-portal-tutorial/tutorial_general_03.png
[4]: ./media/mimecast-personal-portal-tutorial/tutorial_general_04.png

[100]: ./media/mimecast-personal-portal-tutorial/tutorial_general_100.png

[200]: ./media/mimecast-personal-portal-tutorial/tutorial_general_200.png
[201]: ./media/mimecast-personal-portal-tutorial/tutorial_general_201.png
[202]: ./media/mimecast-personal-portal-tutorial/tutorial_general_202.png
[203]: ./media/mimecast-personal-portal-tutorial/tutorial_general_203.png

