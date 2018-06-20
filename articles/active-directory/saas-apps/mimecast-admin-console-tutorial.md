---
title: 'Kurz: Integrace Azure Active Directory pomocí konzoly pro správu Mimecast | Microsoft Docs'
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
ms.openlocfilehash: ef80f7fab21e2b43d9b6b8da80d10d4d47a63dd3
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36214946"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Kurz: Azure Active Directory integrace s Mimecast konzoly pro správu

V tomto kurzu zjistíte integrace Mimecast konzoly pro správu služby Azure Active Directory (Azure AD).

Integrace konzoly pro správu Mimecast s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup ke konzole pro správu Mimecast.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Mimecast konzoly pro správu (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD pomocí konzoly pro správu Mimecast, potřebujete následující položky:

- Předplatné služby Azure AD
- Konzole pro správu Mimecast jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Mimecast konzoly pro správu z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>Přidání Mimecast konzoly pro správu z Galerie
Při konfiguraci integrace Mimecast konzoly pro správu do služby Azure AD potřebujete přidat Mimecast konzoly pro správu z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Mimecast konzoly pro správu z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **konzoly pro správu Mimecast**, vyberte **konzoly pro správu Mimecast** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Mimecast konzoly pro správu v seznamu výsledků](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části konfiguraci a testování Azure AD jednotné přihlašování pomocí konzoly pro správu Mimecast podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v konzole pro správu Mimecast je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v konzole pro správu Mimecast musí navázat.

V konzole pro správu Mimecast přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Mimecast konzoly pro správu, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele konzoly pro správu Mimecast](#create-a-mimecast-admin-console-test-user)**  – Pokud chcete mít protějšek Britta Simon v konzole pro správu Mimecast propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Mimecast konzoly pro správu.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí konzoly pro správu Mimecast, proveďte následující kroky:**

1. Na portálu Azure na **konzoly pro správu Mimecast** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_samlbase.png)

3. Na **Mimecast správce konzoly domény a adresy URL** část, proveďte následující kroky:

    ![Mimecast správce konzoly domény a adresy URL jednotné přihlašování informace](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_url.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL:
    | |
    | -- |
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|

    > [!NOTE] 
    > Adresa URL přihlašování je konkrétní oblasti.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/mimecast-admin-console-tutorial/tutorial_general_400.png)

6. Na **konfigurace konzoly správy Mimecast** klikněte na tlačítko **konfigurace konzoly pro správu Mimecast** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace konzoly správy Mimecast](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se jako správce do konzoly Správce Mimecast.

8. Přejděte na **služby \> aplikace**.

    ![Služby](./media/mimecast-admin-console-tutorial/ic794998.png "služby")

9. Klikněte na tlačítko **ověřování profily**.

    ![Profily ověřování](./media/mimecast-admin-console-tutorial/ic794999.png "profily ověřování")
    
10. Klikněte na tlačítko **nový profil ověřování**.

    ![Nové profily ověřování](./media/mimecast-admin-console-tutorial/ic795000.png "nové profily ověřování")

11. V **profil ověření** část, proveďte následující kroky:

    ![Profil ověření](./media/mimecast-admin-console-tutorial/ic795015.png "profil ověření")
    
    a. V **popis** textovému poli, zadejte název pro svou konfiguraci.
    
    b. Vyberte **vynutit ověřování SAML pro konzolu Správce Mimecast**.
    
    c. Jako **zprostředkovatele**, vyberte **Azure Active Directory**.
    
    d. Vložení **SAML Entity ID**, který jste zkopírovali z portálu Azure do **URL vystavitele** textové pole.
    
    e. Vložení **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure do **přihlašovací adresa URL** textové pole.

    f. Vložení **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure do **adresy URL odhlašovací** textové pole.
    
    >[!NOTE]
    >Hodnota adresy URL pro přihlášení a hodnota adresy URL odhlašovací jsou pro konzolu Správce Mimecast stejné.
    
    g. Kódování base-64 certifikát stažený z portálu Azure v poznámkovém bloku otevřete odebrat první řádek ("*--*") a poslední řádek ("*--*"), zbývající obsah ho zkopírujte do schránky a vložte jej do **certifikát zprostředkovatele Identity (Metadata)** textové pole.
    
    h. Vyberte **povolit jednotné přihlašování na**.
    
    i. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985) 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/mimecast-admin-console-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/mimecast-admin-console-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/mimecast-admin-console-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/mimecast-admin-console-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-mimecast-admin-console-test-user"></a>Vytvoření zkušebního uživatele Mimecast konzoly pro správu

Pokud chcete povolit uživatelům Azure AD přihlášení do konzoly pro správu Mimecast, musí být zřízená do konzoly pro správu Mimecast. V případě Mimecast konzoly pro správu zřizování je ruční úloha.

* Budete muset registraci domény, před vytvořením uživatele.

**Pokud chcete konfigurovat, zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **konzoly pro správu Mimecast** jako správce.
2. Přejděte na **adresáře \> interní**.
   
   ![Adresáře](./media/mimecast-admin-console-tutorial/ic795003.png "adresáře")
3. Klikněte na tlačítko **zaregistrovat nové domény**.
   
   ![Zaregistrujte novou doménu](./media/mimecast-admin-console-tutorial/ic795004.png "zaregistrovat nové domény")
4. Po vytvoření nové domény, klikněte na tlačítko **novou adresu**.
   
   ![Nové adresy](./media/mimecast-admin-console-tutorial/ic795005.png "novou adresu")
5. V dialogovém okně nové adresy proveďte následující kroky:
   
   ![Uložit](./media/mimecast-admin-console-tutorial/ic795006.png "uložit")
   
   a. Typ **e-mailovou adresu**, **globální název**, **heslo**, a **Potvrdit heslo** atributy platný Azure AD účet má mají být zahrnuty do související textových polí.

   b. Klikněte na **Uložit**.

>[!NOTE]
>Další nástroje pro tvorbu účet uživatele konzoly pro správu Mimecast nebo rozhraní API poskytovaných Mimecast konzoly pro správu můžete použít ke zřízení uživatelských účtů Azure AD. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí konzoly pro správu Mimecast udělení přístupu.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Mimecast konzoly pro správu, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **konzoly pro správu Mimecast**.

    ![Konzola pro správu Mimecast odkaz v seznamu aplikací](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Mimecast konzoly pro správu na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Mimecast konzoly pro správu.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
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

