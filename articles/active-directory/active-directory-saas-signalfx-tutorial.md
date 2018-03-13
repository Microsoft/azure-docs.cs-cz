---
title: 'Kurz: Azure Active Directory integrace s SignalFx | Microsoft Docs'
description: "Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SignalFx."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: jeedes
ms.openlocfilehash: 50a86a01c22450ae2d92e6743fb6de7e652d4017
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="tutorial-azure-active-directory-integration-with-signalfx"></a>Kurz: Azure Active Directory integrace s SignalFx

V tomto kurzu zjistěte, jak integrovat SignalFx s Azure Active Directory (Azure AD).

Integrace SignalFx s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k SignalFx.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k SignalFx (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SignalFx, potřebujete následující položky:

- Předplatné služby Azure AD
- SignalFx jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SignalFx z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-signalfx-from-the-gallery"></a>Přidání SignalFx z Galerie
Při konfiguraci integrace SignalFx do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS SignalFx z galerie.

**Pokud chcete přidat SignalFx z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **SignalFx**, vyberte **SignalFx** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![SignalFx v seznamu výsledků](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s SignalFx podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v SignalFx je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v SignalFx musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SignalFx, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele SignalFx](#create-a-signalfx-test-user)**  – Pokud chcete mít protějšek Britta Simon v SignalFx propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci SignalFx.

**Ke konfiguraci Azure AD jednotné přihlašování s SignalFx, proveďte následující kroky:**

1. Na portálu Azure na **SignalFx** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_samlbase.png)

3. Na **SignalFx domény a adresy URL** část, proveďte následující kroky:

    ![SignalFx domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL: `https://api.signalfx.com/v1/saml/metadata`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://api.signalfx.com/v1/saml/acs/<integration ID>`

    > [!NOTE] 
    > Předchozí hodnota není skutečné hodnoty. Aktualizujte hodnotu s skutečná adresa URL odpovědi, který je vysvětlen později v tomto kurzu.

4. Aplikace SignalFx očekává SAML kontrolní výrazy ve specifickém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z **uživatelské atributy** části na stránce integrace aplikace. Následující snímek obrazovky ukazuje příklad pro tento.   

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_attribute.png)

5. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ------------------- | -------------------- |    
    | User.FirstName          | user.givenname |
    | User.email          | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování přidat](./media/active-directory-saas-signalfx-tutorial/tutorial_attribute_04.png)

    ![Konfigurace Addattb přihlášení](./media/active-directory-saas-signalfx-tutorial/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d. Ponechte **Namespace** prázdné.
    
    e. Klikněte na tlačítko **OK**.
 
6. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_certificate.png) 

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-signalfx-tutorial/tutorial_general_400.png)

8. Ke generování **adresu url metadat**, proveďte následující kroky:

    a. Klikněte na tlačítko **registrace aplikace**.
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_appregistrations.png)
   
    b. Klikněte na tlačítko **koncové body** otevřete **koncové body** dialogové okno.  
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_endpointicon.png)

    c. Klikněte na tlačítko Kopírovat kopírování **dokument FEDERAČNÍCH METADAT** adresy url a vložte do poznámkového bloku.
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_endpoint.png)
     
    d. Nyní přejděte na stránku vlastností **SignalFx** a zkopírujte **Id aplikace** pomocí **kopie** tlačítko a vložte do poznámkového bloku.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_appid.png)

    e. Vygenerovat **adresu URL metadat** pomocí následujícího vzorce: `<FEDERATION METADATA DOCUMENT url>?appid=<application id>`

9. Na **SignalFx konfigurace** klikněte na tlačítko **konfigurace SignalFx** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID** z **Stručná referenční příručka části.**

    ![Konfigurace SignalFx](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_configure.png) 

10. Přihlašování k webu společnosti SignalFx jako správce.

11. V SignalFx na nahoře klikněte na **integrace** chcete otevřít stránku integrace.

    ![Integrace SignalFx](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_intg.png)

12. Klikněte na **Azure Active Directory** dlaždici v části **přihlášení služby** části.
 
    ![SignalFx saml](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_saml.png)

13. Klikněte na **nové integrace** a v části **nainstalovat** kartě proveďte následující kroky:
 
    ![SignalFx samlintgpage](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_azure.png)

    a. V **název** jako typ textové pole, nový název integrace, **jednotné přihlašování SAML OurOrgName**.

    b. Kopírování **integrace ID** hodnotu a připojit **adresa URL odpovědi** jako `https://api.signalfx.com/v1/saml/acs/<integration ID>` v **adresa URL odpovědi** textbox z **SignalFx domény a adresy URL** části na portálu Azure.

    c. Klikněte na **nahrát soubor** nahrát **certifikát kódovaný v Base64** stáhli z portálu Azure v **certifikát** textové pole.

    d. V **URL vystavitele** textovému poli, vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure.

    e. V **adresu URL metadat** textovému poli, Vložit **adresu url metadat** vzor, který se mají vygenerovat z portálu Azure.

    f. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-signalfx-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-signalfx-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-signalfx-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-signalfx-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-signalfx-test-user"></a>Vytvoření zkušebního uživatele SignalFx

Cílem této části je vytvoření uživatele v SignalFx nazývá Britta Simon. SignalFx podporuje za běhu zřizování, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k SignalFx, pokud ještě neexistuje.

Když se uživatel přihlásí SignalFx z jednotné přihlašování SAML poprvé, [tým podpory SignalFx](mailto:kmazzola@signalfx.com) je odešle e-mail, který obsahuje odkaz, který je nutné kliknout prostřednictvím k ověření. Proběhne pouze při prvním přihlášení uživatele; pokusů o přihlášení následné nebude vyžadovat ověření e-mailu.

>[!Note]
>Pokud je potřeba ručně vytvořit uživateli, obraťte se na [SignalFx tým podpory](mailto:kmazzola@signalfx.com)

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu SignalFx.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon SignalFx, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **SignalFx**.

    ![V seznamu aplikací na SignalFx odkaz](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici SignalFx na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci SignalFx.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_203.png

