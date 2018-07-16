---
title: 'Kurz: Integrace Azure Active Directory se službou Matic záznam dění na monitoru-O | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Matic záznam dění na monitoru-O.
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
ms.openlocfilehash: 20c0acebde232bd50e6e5befed0facc96ee11b4d
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39040945"
---
# <a name="tutorial-azure-active-directory-integration-with-screencast-o-matic"></a>Kurz: Integrace Azure Active Directory s Matic záznam dění na monitoru – O

V tomto kurzu se dozvíte, jak můžete integrovat s Azure Active Directory (Azure AD) Matic záznam dění na monitoru-O.

Záznam dění na monitoru-O-Matic a integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k vstupně-Matic pro záznam dění na monitoru.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k záznam dění na monitoru-O-Matic (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Matic záznam dění na monitoru-O, potřebujete následující položky:

- S předplatným služby Azure AD
- Záznam dění na monitoru-O Matic jedno přihlášení povolený předplatné

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Matic záznam dění na monitoru-O z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-screencast-o-matic-from-the-gallery"></a>Přidání Matic záznam dění na monitoru-O z Galerie
Konfigurovat integraci Matic záznam dění na monitoru-O do služby Azure AD, budete muset přidat záznam dění na monitoru-O Matic z Galerie na váš seznam spravovaných aplikací SaaS.

**Pokud chcete přidat záznam dění na monitoru-O Matic z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Matic záznam dění na monitoru-O**vyberte **Matic záznam dění na monitoru-O** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Záznam dění na monitoru-O-Matic v seznamu výsledků](./media/screencast-tutorial/tutorial_screencast_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí záznam dění na monitoru-O-Matic na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Matic záznam dění na monitoru-O je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Matic záznam dění na monitoru-O.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se O-Matic pro záznam dění na monitoru, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořte záznam dění na monitoru-O Matic testovacího uživatele](#create-a-screencast-o-matic-test-user)**  – Pokud chcete mít protějšek Britta Simon záznam dění na monitoru-O-Matic, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v Matic záznam dění na monitoru-O aplikaci.

**Ke konfiguraci Azure AD jednotné přihlašování s Matic záznam dění na monitoru-O, proveďte následující kroky:**

1. Na webu Azure Portal na **Matic záznam dění na monitoru-O** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/screencast-tutorial/tutorial_screencast_samlbase.png)

3. Na **Matic záznam dění na monitoru-O domény a adresy URL** části, proveďte následující kroky:

    ![Záznam dění na monitoru-O Matic domény a adresy URL jednotného přihlašování – informace](./media/screencast-tutorial/tutorial_screencast_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE] 
    > Hodnota přihlašovací adresa URL není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory O-Matic pro záznam dění na monitoru klienta](mailto:support@screencast-o-matic.com) má být získána hodnota. 
 
4. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/screencast-tutorial/tutorial_screencast_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/screencast-tutorial/tutorial_general_400.png)

6. V jiné okno webového prohlížeče, přihlaste se k záznam dění na monitoru-O-Matic jako správce.

7. Klikněte na **předplatné**.

    ![Předplatné](./media/screencast-tutorial/tutorial_screencast_sub.png)

8. V části **stránku** části, klikněte na tlačítko **nastavení**.

    ![Přístup](./media/screencast-tutorial/tutorial_screencast_setup.png)

9. Na **stránky nastavení přístupu**, proveďte následující kroky:

    * V části **adresa URL přístupového** oddíl, do zadaného textového pole zadejte vaše instancename.

    ![Přístup](./media/screencast-tutorial/tutorial_screencast_access.png)

    * Vyberte **vyžadují uživatele domény** pod **omezení uživatele SAML (volitelné)** oddílu.

    * V části **nahrát soubor XML metadat zprostředkovatele identity**, klikněte na tlačítko **zvolit soubor** nahrát metadat, který jste si stáhli z webu Azure portal.

    * Klikněte na **OK**. 

    ![Přístup](./media/screencast-tutorial/tutorial_screencast_save.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/screencast-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/screencast-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/screencast-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/screencast-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-screencast-o-matic-test-user"></a>Vytvořte záznam dění na monitoru-O Matic testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v Matic záznam dění na monitoru-O. Záznam dění na monitoru-O Matic podporuje just-in-time zřizování, což je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k vstupně-Matic pro záznam dění na monitoru, pokud ještě neexistuje.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory O-Matic pro záznam dění na monitoru klienta](mailto:support@screencast-o-matic.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k vstupně-Matic pro záznam dění na monitoru.

![Přiřazení role uživatele][200] 

**Přiřadit Britta Simon Matic záznam dění na monitoru-O, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Matic záznam dění na monitoru-O**.

    ![Záznam dění na monitoru-O Matic odkaz v seznamu aplikací](./media/screencast-tutorial/tutorial_screencast_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na záznam dění na monitoru-O Matic dlaždici na přístupovém panelu, které by měl získat automaticky přihlášení k Matic záznam dění na monitoru-O aplikaci.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
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

