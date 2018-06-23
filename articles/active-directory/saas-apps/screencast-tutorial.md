---
title: 'Kurz: Azure Active Directory integrace s Matic záznam dění na monitoru-O | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a E-Matic pro záznam dění na monitoru.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 525ad47d-5488-40e2-aeaf-ae6183745682
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: jeedes
ms.openlocfilehash: 7212e0b07b525286f0b194a53c6780269630ad9c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320727"
---
# <a name="tutorial-azure-active-directory-integration-with-screencast-o-matic"></a>Kurz: Azure Active Directory integrace s E-Matic pro záznam dění na monitoru

V tomto kurzu zjistěte, jak integrovat Matic záznam dění na monitoru-O službě Azure Active Directory (Azure AD).

Integrace Matic záznam dění na monitoru-O s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k E-Matic pro záznam dění na monitoru.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k záznam dění na monitoru-O-Matic (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s E-Matic pro záznam dění na monitoru, potřebujete následující položky:

- Předplatné služby Azure AD
- E-Matic pro záznam dění na monitoru jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání E-Matic pro záznam dění na monitoru z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-screencast-o-matic-from-the-gallery"></a>Přidání E-Matic pro záznam dění na monitoru z Galerie
Pokud chcete nakonfigurovat integraci E-Matic pro záznam dění na monitoru do služby Azure AD, potřebujete přidat Matic záznam dění na monitoru-O z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat záznam dění na monitoru-O Matic z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Matic záznam dění na monitoru-O**, vyberte **Matic záznam dění na monitoru-O** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Záznam dění na monitoru-O-Matic a v seznamu výsledků](./media/screencast-tutorial/tutorial_screencast_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s záznam dění na monitoru-O-Matic a podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v E-Matic pro záznam dění na monitoru je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživateli v E-Matic pro záznam dění na monitoru musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s E-Matic pro záznam dění na monitoru, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Matic záznam dění na monitoru-O](#create-a-screencast-o-matic-test-user)**  – Pokud chcete mít protějšek Britta Simon v záznam dění na monitoru-O-Matic a propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v Matic záznam dění na monitoru-O aplikaci.

**Ke konfiguraci Azure AD jednotné přihlašování s E-Matic pro záznam dění na monitoru, proveďte následující kroky:**

1. Na portálu Azure na **Matic záznam dění na monitoru-O** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/screencast-tutorial/tutorial_screencast_samlbase.png)

3. Na **Matic záznam dění na monitoru-O domény a adresy URL** část, proveďte následující kroky:

    ![Záznam dění na monitoru-O Matic domény a adresy URL jednotné přihlašování informace](./media/screencast-tutorial/tutorial_screencast_url.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE] 
    > Hodnota přihlašovací adresa URL není skutečné. Aktualizujte hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory Matic záznam dění na monitoru-O klienta](mailto:support@screencast-o-matic.com) k získání hodnoty. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/screencast-tutorial/tutorial_screencast_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/screencast-tutorial/tutorial_general_400.png)

6. V okně prohlížeče jiný web a přihlášení k záznam dění na monitoru-O-Matic a jako správce.

7. Klikněte na **předplatné**.

    ![Odběr](./media/screencast-tutorial/tutorial_screencast_sub.png)

8. V části **stránce** části, klikněte na tlačítko **instalační program**.

    ![Přístup](./media/screencast-tutorial/tutorial_screencast_setup.png)

9. Na **stránce instalace**, proveďte následující kroky:

    * V části **adresu URL pro přístup** část, zadaný textového pole zadejte vaše instancename.

    ![Přístup](./media/screencast-tutorial/tutorial_screencast_access.png)

    * Vyberte **vyžadují uživatele domény** pod **omezení uživatele SAML (volitelné)** části.

    * V části **nahrát soubor XML IDP Metadata**, klikněte na tlačítko **zvolit soubor** nahrát metadata, která jste si stáhli z portálu Azure.

    * Klikněte na **OK**. 

    ![Přístup](./media/screencast-tutorial/tutorial_screencast_save.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/screencast-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/screencast-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/screencast-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/screencast-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-screencast-o-matic-test-user"></a>Vytvoření E-Matic pro záznam dění na monitoru zkušebního uživatele

Cílem této části je vytvoření uživatele volal Britta Simon v E-Matic pro záznam dění na monitoru. Záznam dění na monitoru-O Matic podporuje za běhu zřizování, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k E-Matic pro záznam dění na monitoru, pokud ještě neexistuje.

>[!Note]
>Pokud je potřeba ručně vytvořit uživateli, obraťte se na [tým podpory Matic záznam dění na monitoru-O klienta](mailto:support@screencast-o-matic.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k E-Matic pro záznam dění na monitoru.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Matic záznam dění na monitoru-O, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Matic záznam dění na monitoru-O**.

    ![Odkaz O-Matic pro záznam dění na monitoru v seznamu aplikací](./media/screencast-tutorial/tutorial_screencast_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na Matic záznam dění na monitoru-O dlaždici na přístupovém panelu, jste měli získat automaticky přihlášení k Matic záznam dění na monitoru-O aplikaci.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/screencast-tutorial/tutorial_general_01.png
[2]: ./media/screencast-tutorial/tutorial_general_02.png
[3]: ./media/screencast-tutorial/tutorial_general_03.png
[4]: ./media/screencast-tutorial/tutorial_general_04.png

[100]: ./media/screencast-tutorial/tutorial_general_100.png

[200]: ./media/screencast-tutorial/tutorial_general_200.png
[201]: ./media/screencast-tutorial/tutorial_general_201.png
[202]: ./media/screencast-tutorial/tutorial_general_202.png
[203]: ./media/screencast-tutorial/tutorial_general_203.png

