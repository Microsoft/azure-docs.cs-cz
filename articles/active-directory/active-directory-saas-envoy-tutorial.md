---
title: 'Kurz: Azure Active Directory integrace s Envoy | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Envoy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 71f7afcc-1033-4098-9b7e-4f9f2b26f734
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: jeedes
ms.openlocfilehash: ebe159cc8527bd92c9b2b8970dec9ddbdbb5ecf1
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34340589"
---
# <a name="tutorial-azure-active-directory-integration-with-envoy"></a>Kurz: Azure Active Directory integrace s Envoy

V tomto kurzu zjistěte, jak integrovat Envoy s Azure Active Directory (Azure AD).

Integrace Envoy s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Envoy.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Envoy (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Envoy, potřebujete následující položky:

- Předplatné služby Azure AD
- Envoy jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Envoy z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-envoy-from-the-gallery"></a>Přidání Envoy z Galerie
Při konfiguraci integrace Envoy do služby Azure AD potřebujete přidat Envoy z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Envoy z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Envoy**, vyberte **Envoy** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Envoy v seznamu výsledků](./media/active-directory-saas-envoy-tutorial/tutorial_envoy_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Envoy podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Envoy je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Envoy musí navázat.

V Envoy, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Envoy, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovací uživatele s Envoy](#create-an-envoy-test-user)**  – Pokud chcete mít protějšek Britta Simon v Envoy propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Envoy.

**Ke konfiguraci Azure AD jednotné přihlašování s Envoy, proveďte následující kroky:**

1. Na portálu Azure na **Envoy** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-envoy-tutorial/tutorial_envoy_samlbase.png)

3. Na **Envoy domény a adresy URL** část, proveďte následující kroky:

    ![Envoy domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-envoy-tutorial/tutorial_envoy_url.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<tenant-name>.Envoy.com`
    
    > [!NOTE] 
    > Tato hodnota není skutečné. Aktualizujte tuto hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory Envoy klienta](https://envoy.com/contact/) získat tuto hodnotu.

4. Na **SAML podpisový certifikát** část, zkopírujte **kryptografický OTISK** hodnota certifikátu...

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-envoy-tutorial/tutorial_envoy_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-envoy-tutorial/tutorial_general_400.png)

6. Na **Envoy konfigurace** klikněte na tlačítko **konfigurace Envoy** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace Envoy](./media/active-directory-saas-envoy-tutorial/tutorial_envoy_configure.png)

7. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti Envoy.

8. Na panelu nástrojů v horní části klikněte na tlačítko **nastavení**.

    ![Envoy](./media/active-directory-saas-envoy-tutorial/ic776782.png "Envoy")

9. Klikněte na tlačítko **společnosti**.

    ![Společnosti](./media/active-directory-saas-envoy-tutorial/ic776783.png "společnosti")

10. Klikněte na tlačítko **SAML**.

    ![SAML](./media/active-directory-saas-envoy-tutorial/ic776784.png "SAML")

11. V **ověřování SAML** konfigurace části, proveďte následující kroky:

    ![Ověřování SAML](./media/active-directory-saas-envoy-tutorial/ic776785.png "ověřování SAML")
    
    >[!NOTE]
    >Hodnota pro Identifikátor umístění Ústředí je automaticky generovaný aplikací.
    
    a. V **otisk prstu** textovému poli, Vložit **kryptografický otisk** hodnota certifikát, který jste zkopírovali z portálu Azure.
    
    b. Vložení **SAML jeden přihlašování adresa URL služby** hodnotu, kterou jste zkopírovali formuláři na portálu Azure do **adresa URL IDENTITY zprostředkovatele protokolu HTTP SAML** textové pole.
    
    c. Klikněte na tlačítko **uložit změny**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-envoy-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-envoy-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-envoy-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-envoy-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-envoy-test-user"></a>Vytvořit uživatele s Envoy testu

Neexistuje žádná položka akce můžete nakonfigurovat na Envoy zřizování uživatelů. Když přiřazený uživatel se pokusí přihlásit pomocí přístupového panelu Envoy, Envoy ověří, zda uživatel existuje. Pokud neexistuje žádný účet k dispozici dosud, se automaticky vytvoří pomocí Envoy.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Envoy.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Envoy, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Envoy**.

    ![V seznamu aplikací na Envoy odkaz](./media/active-directory-saas-envoy-tutorial/tutorial_envoy_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Envoy na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Envoy.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-envoy-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-envoy-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-envoy-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-envoy-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-envoy-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-envoy-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-envoy-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-envoy-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-envoy-tutorial/tutorial_general_203.png

