---
title: 'Kurz: Integrace Azure Active Directory s SignalFx | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SignalFx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 6f2d869f345aeb8f50d42de6b1533b849ffb2182
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197564"
---
# <a name="tutorial-azure-active-directory-integration-with-signalfx"></a>Kurz: Integrace Azure Active Directory s SignalFx

V tomto kurzu se dozvíte, jak integrovat SignalFx s Azure Active Directory (Azure AD).

SignalFx integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k SignalFx.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k SignalFx (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SignalFx, potřebujete následující položky:

- Předplatné Azure AD
- SignalFx jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SignalFx z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-signalfx-from-the-gallery"></a>Přidání SignalFx z Galerie
Konfigurace integrace SignalFx do služby Azure AD, budete muset přidat SignalFx z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat SignalFx z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **SignalFx**vyberte **SignalFx** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![SignalFx v seznamu výsledků](./media/signalfx-tutorial/tutorial_signalfx_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí SignalFx podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v SignalFx je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v SignalFx potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SignalFx, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele SignalFx](#create-a-signalfx-test-user)**  – Pokud chcete mít protějšek Britta Simon SignalFx, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci SignalFx.

**Ke konfiguraci Azure AD jednotné přihlašování s SignalFx, proveďte následující kroky:**

1. Na webu Azure Portal na **SignalFx** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/signalfx-tutorial/tutorial_signalfx_samlbase.png)

1. Na **SignalFx domény a adresy URL** části, proveďte následující kroky:

    ![SignalFx domény a adresy URL jednotného přihlašování – informace](./media/signalfx-tutorial/tutorial_signalfx_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL: `https://api.signalfx.com/v1/saml/metadata`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://api.signalfx.com/v1/saml/acs/<integration ID>`

    > [!NOTE] 
    > Předchozí hodnota není skutečné hodnoty. Aktualizujte hodnotu pomocí příkazu skutečnou adresu URL odpovědi, který je vysvětlen později v tomto kurzu.

1. SignalFx aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro tuto.   

    ![Konfigurace jednotného přihlašování](./media/signalfx-tutorial/tutorial_signalfx_attribute.png)

1. V **atributy uživatele** části na **jednotného přihlašování** dialogovém okně Konfigurovat atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ------------------- | -------------------- |    
    | User.FirstName          | user.givenname |
    | User.email          | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurovat Single Sign-On přidat](./media/signalfx-tutorial/tutorial_attribute_04.png)

    ![Nakonfigurovat jednotné přihlašování Addattb](./media/signalfx-tutorial/tutorial_attribute_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. Nechte **Namespace** prázdné.
    
    e. Klikněte na tlačítko **OK**.
 
1. Na **podpisový certifikát SAML** části, proveďte následující kroky: 

    ![Odkaz ke stažení certifikátu](./media/signalfx-tutorial/tutorial_signalfx_certificate.png)

    a. Klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a vložte ho do poznámkového bloku.

    b. Klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/signalfx-tutorial/tutorial_general_400.png)

1. Na **SignalFx konfigurace** klikněte na tlačítko **nakonfigurovat SignalFx** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID** z **Stručná referenční příručka oddílu.**

    ![Konfigurace SignalFx](./media/signalfx-tutorial/tutorial_signalfx_configure.png) 

1. Přihlašování k webu společnosti SignalFx jako správce.

1. V SignalFx na nahoře klikněte na **integrace** otevřete stránku integrace.

    ![Integrace SignalFx](./media/signalfx-tutorial/tutorial_signalfx_intg.png)

1. Klikněte na **Azure Active Directory** dlaždici v části **přihlášení služby** oddílu.
 
    ![SignalFx saml](./media/signalfx-tutorial/tutorial_signalfx_saml.png)

1. Klikněte na **NOVĚ zavedené INTEGRACI** a v části **nainstalovat** kartu, proveďte následující kroky:
 
    ![SignalFx samlintgpage](./media/signalfx-tutorial/tutorial_signalfx_azure.png)

    a. V **název** typ textové pole, nový název integrace, jako jsou **OurOrgName SAML SSO**.

    b. Kopírovat **integrace ID** hodnoty a doplnit o **adresy URL odpovědi** jako `https://api.signalfx.com/v1/saml/acs/<integration ID>` v **adresy URL odpovědi** textové pole z **SignalFx domény a adresy URL** části webu Azure Portal.

    c. Klikněte na **nahrát soubor** k nahrání **certifikát kódovaný v Base64** stáhnout z webu Azure portal v **certifikát** textového pole.

    d. V **URL vystavitele** textového pole vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure portal.

    e. V **adresa URL metadat** vložit do textového pole **adresa Url federačních metadat aplikace** který jste zkopírovali z portálu Azure portal.

    f. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/signalfx-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/signalfx-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/signalfx-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/signalfx-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-signalfx-test-user"></a>Vytvoření zkušebního uživatele SignalFx

Cílem této části je vytvořte uživatele Britta Simon v SignalFx. SignalFx podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k SignalFx, pokud ještě neexistuje.

Když se uživatel přihlásí SignalFx ze SAML SSO poprvé, [tým podpory SignalFx](mailto:kmazzola@signalfx.com) je odešle e-mail s odkazem, jenž se musí proklikejte se k ověření. K tomu dochází pouze při prvním přihlášení uživatele; pokusy o přihlášení. následné nebude vyžadovat ověření e-mailem.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [SignalFx tým podpory](mailto:kmazzola@signalfx.com)

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k SignalFx použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit SignalFx Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **SignalFx**.

    ![Odkaz SignalFx v seznamu aplikací](./media/signalfx-tutorial/tutorial_signalfx_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici SignalFx na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci SignalFx.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/signalfx-tutorial/tutorial_general_01.png
[2]: ./media/signalfx-tutorial/tutorial_general_02.png
[3]: ./media/signalfx-tutorial/tutorial_general_03.png
[4]: ./media/signalfx-tutorial/tutorial_general_04.png

[100]: ./media/signalfx-tutorial/tutorial_general_100.png

[200]: ./media/signalfx-tutorial/tutorial_general_200.png
[201]: ./media/signalfx-tutorial/tutorial_general_201.png
[202]: ./media/signalfx-tutorial/tutorial_general_202.png
[203]: ./media/signalfx-tutorial/tutorial_general_203.png

