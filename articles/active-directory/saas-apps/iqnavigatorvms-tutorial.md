---
title: 'Kurz: Integrace Azure Active Directory s virtuálními počítači IQNavigator | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a virtuálními počítači IQNavigator.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a8a09b25-dfa5-4c31-aea2-53bf1853b365
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: jeedes
ms.openlocfilehash: 9b264a7ba1479e485ff528ee242c78c4b39010dc
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39052512"
---
# <a name="tutorial-azure-active-directory-integration-with-iqnavigator-vms"></a>Kurz: Integrace Azure Active Directory s virtuálními počítači IQNavigator

V tomto kurzu se dozvíte, jak integrovat IQNavigator virtuálních počítačů s Azure Active Directory (Azure AD).

Integrace IQNavigator virtuálních počítačů s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k virtuálním počítačům IQNavigator
- Uživatele, aby automaticky získat přihlášeného k virtuálním počítačům IQNavigator (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s virtuálními počítači IQNavigator, potřebujete následující položky:

- S předplatným služby Azure AD
- Virtuálních počítačích IQNavigator jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební zde [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání IQNavigator virtuálních počítačů z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-iqnavigator-vms-from-the-gallery"></a>Přidání IQNavigator virtuálních počítačů z Galerie
Ke konfiguraci integrace IQNavigator virtuálních počítačů do služby Azure AD, budete muset přidat IQNavigator virtuální počítače z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat IQNavigator virtuálních počítačů z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **virtuálních počítačů IQNavigator**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_search.png)

5. Na panelu výsledků vyberte **virtuálních počítačů IQNavigator**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurujete a test Azure AD jednotné přihlašování s IQNavigator virtuálních počítačů podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek ve virtuálních počítačích IQNavigator je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské ve virtuálních počítačích IQNavigator potřeba navázat.

Ve virtuálních počítačích IQNavigator, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí IQNavigator virtuálních počítačů, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření virtuálních počítačů IQNavigator testovacího uživatele](#creating-a-iqnavigator-vms-test-user)**  – Pokud chcete mít protějšek Britta Simon ve virtuálních počítačích IQNavigator, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci IQNavigator virtuálních počítačů.

**Ke konfiguraci Azure AD jednotné přihlašování s virtuálními počítači IQNavigator, proveďte následující kroky:**

1. Na webu Azure Portal na **virtuálních počítačů IQNavigator** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_samlbase.png)

3. Na **IQNavigator virtuální počítače domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL:`iqn.com`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.iqnavigator.com/security/login?client_name=https://sts.window.net/<instance name>`

4. Zkontrolujte **zobrazit pokročilé nastavení URL**, postupujte následovně:

    ![Konfigurace jednotného přihlašování](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_url1.png)

    V **propojení stavu** textového pole zadejte adresu URL pomocí následujícímu vzoru:`https://<subdomain>.iqnavigator.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečným stavem adresy URL odpovědi a propojení. Kontakt [tým podpory IQNavigator virtuálních počítačů klienta](https://www.beeline.com/iqn-product-support/) k získání těchto hodnot.

5. Na **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a vložte ho do poznámkového bloku.
    
    ![Konfigurace jednotného přihlašování](./media/iqnavigatorvms-tutorial/tutorial_metadataurl.png)

6. Aplikace IQNavigator očekávat, že hodnota identifikátoru jedinečných uživatelů v deklaraci identity identifikátor názvu. Odběratele můžete namapovat správnou hodnotu pro deklaraci identity identifikátor názvu. V tomto případě jsme změnili uživatele. UserPrincipalName pro účely ukázky. Ale podle nastavení organizaci byste měli namapovat správnou hodnotu pro něj.

    ![Konfigurace jednotného přihlašování](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_attribute.png)

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/iqnavigatorvms-tutorial/tutorial_general_400.png)

8. Na **konfigurace virtuálních počítačů IQNavigator** klikněte na tlačítko **konfigurace virtuálních počítačů IQNavigator** otevřete **nakonfigurovat přihlašování** okno. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_configure.png)

9. Ke konfiguraci jednotného přihlašování na **IQNavigator virtuálních počítačů** straně, je nutné odeslat **adresa Url federačních metadat aplikace**, **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby**k [tým podpory virtuálních počítačů IQNavigator](https://www.beeline.com/iqn-product-support/). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/iqnavigatorvms-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/iqnavigatorvms-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.

    ![Vytváří se testovací uživatele služby Azure AD](./media/iqnavigatorvms-tutorial/create_aaduser_03.png)

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/iqnavigatorvms-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="creating-a-iqnavigator-vms-test-user"></a>Vytváření virtuálních počítačů IQNavigator testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon ve virtuálních počítačích IQNavigator. Práce s [tým podpory virtuálních počítačů IQNavigator](https://www.beeline.com/iqn-product-support/) k přidání uživatelů v účtu IQNavigator virtuálních počítačů.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k virtuálním počítačům IQNavigator.

![Přiřadit uživatele][200]

**Pokud chcete přiřadit k virtuálním počítačům IQNavigator Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **virtuálních počítačů IQNavigator**.

    ![Konfigurace jednotného přihlašování](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici IQNavigator virtuálních počítačů na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci IQNavigator virtuálních počítačů.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/iqnavigatorvms-tutorial/tutorial_general_01.png
[2]: ./media/iqnavigatorvms-tutorial/tutorial_general_02.png
[3]: ./media/iqnavigatorvms-tutorial/tutorial_general_03.png
[4]: ./media/iqnavigatorvms-tutorial/tutorial_general_04.png

[100]: ./media/iqnavigatorvms-tutorial/tutorial_general_100.png

[200]: ./media/iqnavigatorvms-tutorial/tutorial_general_200.png
[201]: ./media/iqnavigatorvms-tutorial/tutorial_general_201.png
[202]: ./media/iqnavigatorvms-tutorial/tutorial_general_202.png
[203]: ./media/iqnavigatorvms-tutorial/tutorial_general_203.png

