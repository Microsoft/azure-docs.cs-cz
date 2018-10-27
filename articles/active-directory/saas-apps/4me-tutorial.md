---
title: 'Kurz: Integrace Azure Active Directory se službou 4me | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a 4me.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 983eecc6-41f8-49b7-b7f6-dcf833dde121
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2018
ms.author: jeedes
ms.openlocfilehash: c33edf13a8bcafd4a6c3d4885553fc856ec941d8
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158121"
---
# <a name="tutorial-azure-active-directory-integration-with-4me"></a>Kurz: Integrace Azure Active Directory se službou 4me

V tomto kurzu se dozvíte, jak integrovat 4me s Azure Active Directory (Azure AD).

4me integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k 4me.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k 4me (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s 4me, potřebujete následující položky:

- Předplatné Azure AD
- 4me jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání 4me z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-4me-from-the-gallery"></a>Přidání 4me z Galerie
Konfigurace integrace 4me do služby Azure AD, budete muset přidat 4me z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat 4me z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **4me**vyberte **4me** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![4me v seznamu výsledků](./media/4me-tutorial/tutorial_4me_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí 4me podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v 4me je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v 4me potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s 4me, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele 4me](#create-a-4me-test-user)**  – Pokud chcete mít protějšek Britta Simon v 4me, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci 4me.

**Ke konfiguraci Azure AD jednotné přihlašování s 4me, proveďte následující kroky:**

1. Na webu Azure Portal na **4me** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/4me-tutorial/tutorial_4me_samlbase.png)

3. Na **4me domény a adresy URL** části, proveďte následující kroky:

    ![4me domény a adresy URL jednotné přihlašování – informace](./media/4me-tutorial/tutorial_4me_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru:

    | Prostředí| zprostředkovatele identity|
    |---|---|
    | PRODUKČNÍ | `https://<SUBDOMAIN>.4me.com`|
    | KONTROLA KVALITY| `https://<SUBDOMAIN>.4me.qa`|
  
    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    
    | Prostředí| zprostředkovatele identity|
    |---|---|
    | PRODUKČNÍ | `https://<SUBDOMAIN>.4me.com`|
    | KONTROLA KVALITY| `https://<SUBDOMAIN>.4me.qa`|

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory klienta 4me](mailto:support@4me.com) k získání těchto hodnot. 
 
4. 4me aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro tuto.
    
    ![Konfigurace jednotného přihlašování](./media/4me-tutorial/tutorial_4me_attribute.png)

5. V **atributy uživatele** části na **jednotného přihlašování** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ---------------| --------------- |    
    | křestní_jméno | user.givenname |
    | Příjmení | user.surname |

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/4me-tutorial/tutorial_attribute_04.png)

    ![Konfigurace jednotného přihlašování](./media/4me-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. Nechte **Namespace** prázdné.
    
    d. Klikněte na tlačítko **Ok**

6. Na **podpisový certifikát SAML** tématu, zkopírujte **kryptografický OTISK** hodnotu ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/4me-tutorial/tutorial_4me_certificate.png) 

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/4me-tutorial/tutorial_general_400.png)

8. Na **4me konfigurace** klikněte na tlačítko **nakonfigurovat 4me** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace 4me](./media/4me-tutorial/tutorial_4me_configure.png) 

9. V jiné okno webového prohlížeče, přihlaste se k 4me jako správce.

10. V levém horním rohu, klikněte na **nastavení** logo a na panelu klikněte na levé straně **Single Sign-On**.

    ![nastavení 4me](./media/4me-tutorial/tutorial_4me_settings.png)

11. Na **Single Sign-On** stránce, proveďte následující kroky:

    ![4me singleasignon](./media/4me-tutorial/tutorial_4me_singlesignon.png)

    a. Vyberte **povoleno** možnost.

    b. V **vzdálené odhlašovací adresa URL** textového pole vložte hodnotu **odhlašování URL**, který jste zkopírovali z portálu Azure portal.

    c. V části **SAML** sekci **adresu URL jednotného přihlašování SAML** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    d. V **otisku certifikátu** vložit do textového pole **kryptografický OTISK** hodnota oddělených středníkem v pořadí duplets (AA:BB:CC:DD:EE:FF:GG:HH:II), který jste zkopírovali z portálu Azure portal.

    e. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/4me-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/4me-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/4me-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/4me-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-4me-test-user"></a>Vytvoření zkušebního uživatele 4me

Cílem této části je vytvořte uživatele Britta Simon v 4me. 4me podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k 4me, pokud ještě neexistuje.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory 4me](mailto:support@4me.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k 4me.

![Přiřazení role uživatele][200] 

**Přiřadit 4me Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **4me**.

    ![Odkaz 4me v seznamu aplikací](./media/4me-tutorial/tutorial_4me_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici 4me na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci 4me.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/4me-tutorial/tutorial_general_01.png
[2]: ./media/4me-tutorial/tutorial_general_02.png
[3]: ./media/4me-tutorial/tutorial_general_03.png
[4]: ./media/4me-tutorial/tutorial_general_04.png

[100]: ./media/4me-tutorial/tutorial_general_100.png

[200]: ./media/4me-tutorial/tutorial_general_200.png
[201]: ./media/4me-tutorial/tutorial_general_201.png
[202]: ./media/4me-tutorial/tutorial_general_202.png
[203]: ./media/4me-tutorial/tutorial_general_203.png

