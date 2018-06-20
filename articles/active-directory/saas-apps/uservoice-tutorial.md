---
title: 'Kurz: Azure Active Directory integrace s UserVoice | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a služby UserVoice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.openlocfilehash: 6d8562965a5616c39aab598d48046dc2362c3e67
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36227798"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Kurz: Azure Active Directory integrace s UserVoice

V tomto kurzu zjistěte, jak integrovat UserVoice s Azure Active Directory (Azure AD).

Integrace služby UserVoice s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k UserVoice.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k UserVoice (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s UserVoice, potřebujete následující položky:

- Předplatné služby Azure AD
- Webu UserVoice jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání UserVoice z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-uservoice-from-the-gallery"></a>Přidání UserVoice z Galerie
Při konfiguraci integrace služby UserVoice do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS UserVoice z galerie.

**Pokud chcete přidat UserVoice z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **UserVoice**, vyberte **UserVoice** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![UserVoice v seznamu výsledků](./media/uservoice-tutorial/tutorial_uservoice_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s UserVoice podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v UserVoice je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v UserVoice musí navázat.

V UserVoice, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s UserVoice, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele UserVoice](#create-a-uservoice-test-user)**  – Pokud chcete mít protějšek Britta Simon v UserVoice propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci UserVoice.

**Ke konfiguraci Azure AD jednotné přihlašování s UserVoice, proveďte následující kroky:**

1. Na portálu Azure na **UserVoice** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/uservoice-tutorial/tutorial_uservoice_samlbase.png)

3. Na **UserVoice domény a adresy URL** část, proveďte následující kroky:

    ![Doména služby UserVoice a adresy URL jednotné přihlašování informace](./media/uservoice-tutorial/tutorial_uservoice_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<tenantname>.UserVoice.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<tenantname>.UserVoice.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory UserVoice klienta](https://www.uservoice.com/) k získání těchto hodnot.

4. Na **SAML podpisový certifikát** část, zkopírujte **kryptografický OTISK** hodnota certifikátu.

    ![Odkaz ke stažení certifikátu](./media/uservoice-tutorial/tutorial_uservoice_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/uservoice-tutorial/tutorial_general_400.png)

6. Na **UserVoice konfigurace** klikněte na tlačítko **konfigurace UserVoice** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL a SAML jeden přihlašování služby URL** z **Stručná referenční příručka části.**

    ![Konfigurace služby UserVoice](./media/uservoice-tutorial/tutorial_uservoice_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti UserVoice jako správce.

8. Na panelu nástrojů v horní části klikněte na tlačítko **nastavení**a potom vyberte **webový portál** z nabídky.
   
    ![V oddílu nastavení na straně aplikace](./media/uservoice-tutorial/ic777519.png "nastavení")

9. Na **webový portál** ve **ověření uživatele** klikněte na tlačítko **upravit** otevřete **upravit ověření uživatele** dialogové okno stránky.
   
    ![Webový portál karta](./media/uservoice-tutorial/ic777520.png "webový portál")

10. Na **upravit ověření uživatele** dialogové okno proveďte následující kroky:
   
    ![Upravit ověření uživatele](./media/uservoice-tutorial/ic777521.png "upravit ověření uživatele")
   
    a. Klikněte na tlačítko **jednotné přihlašování (SSO)**.
 
    b. Vložení **SAML jeden přihlašování adresa URL služby** hodnotu, kterou jste zkopírovali z portálu Azure do **jednotné přihlašování vzdáleného přihlášení** textové pole.

    c. Vložení **Sign-Out URL** hodnotu, kterou jste zkopírovali z portálu Azure do **vzdáleného Sign-Out jednotného přihlašování k textovému poli**.
 
    d. Vložení **kryptografický otisk** hodnotu, kterou jste zkopírovali z portálu Azure do **aktuální otisků prstů certifikátů SHA1** textové pole.
    
    e. Klikněte na tlačítko **uložit nastavení ověřování**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/uservoice-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/uservoice-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/uservoice-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/uservoice-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-uservoice-test-user"></a>Vytvoření zkušebního uživatele UserVoice

Povolit uživatelům Azure AD přihlášení na stránku UserVoice, musí být zřízená do UserVoice. V případě UserVoice zřizování je ruční úloha.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>K poskytnutí uživatelského účtu, proveďte následující kroky:
1. Přihlaste se k vaší **UserVoice** klienta.

2. Přejděte na **nastavení**.
   
    ![Nastavení](./media/uservoice-tutorial/ic777811.png "nastavení")

3. Klikněte na tlačítko **Obecné**.

4. Klikněte na tlačítko **agenty a oprávnění**.
   
    ![Agenti a oprávnění](./media/uservoice-tutorial/ic777812.png "agenty a oprávnění")

5. Klikněte na tlačítko **přidat admins**.
   
    ![Přidat admins](./media/uservoice-tutorial/ic777813.png "přidat admins")

6. Na **pozvat správci** dialogové okno, proveďte následující kroky:
   
    ![Pozvat správci](./media/uservoice-tutorial/ic777814.png "pozvat správci")
   
    a. Do textového pole e-mailů, zadejte e-mailovou adresu účtu chcete zřídit a pak klikněte na tlačítko **přidat**.
   
    b. Klikněte na tlačítko **pozvat**.

> [!NOTE]
> Můžete použít všechny ostatní UserVoice uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované UserVoice zřídit AAD uživatelské účty.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu UserVoice.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon UserVoice, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **UserVoice**.

    ![V seznamu aplikací na odkaz UserVoice](./media/uservoice-tutorial/tutorial_uservoice_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici služby UserVoice na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci UserVoice.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/uservoice-tutorial/tutorial_general_01.png
[2]: ./media/uservoice-tutorial/tutorial_general_02.png
[3]: ./media/uservoice-tutorial/tutorial_general_03.png
[4]: ./media/uservoice-tutorial/tutorial_general_04.png

[100]: ./media/uservoice-tutorial/tutorial_general_100.png

[200]: ./media/uservoice-tutorial/tutorial_general_200.png
[201]: ./media/uservoice-tutorial/tutorial_general_201.png
[202]: ./media/uservoice-tutorial/tutorial_general_202.png
[203]: ./media/uservoice-tutorial/tutorial_general_203.png

