---
title: 'Kurz: Azure Active Directory integrace s Andromeda | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Andromeda.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7a142c86-ca0c-4915-b1d8-124c08c3e3d8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: jeedes
ms.openlocfilehash: 09f2f854e3b5a70938b6c47bb7dfb5fdf6d52df3
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36223207"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Kurz: Azure Active Directory integrace s Andromeda

V tomto kurzu zjistěte, jak integrovat Andromeda s Azure Active Directory (Azure AD).

Integrace Andromeda s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Andromeda.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Andromeda (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Andromeda, potřebujete následující položky:

- Předplatné služby Azure AD
- Andromeda jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Andromeda z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-andromeda-from-the-gallery"></a>Přidání Andromeda z Galerie
Při konfiguraci integrace Andromeda do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Andromeda z galerie.

**Pokud chcete přidat Andromeda z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Andromeda**, vyberte **Andromeda** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Andromeda v seznamu výsledků](./media/andromedascm-tutorial/tutorial_andromedascm_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Andromeda podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Andromeda je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Andromeda musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Andromeda, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovací uživatele s Andromeda](#create-an-andromeda-test-user)**  – Pokud chcete mít protějšek Britta Simon v Andromeda propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Andromeda.

**Ke konfiguraci Azure AD jednotné přihlašování s Andromeda, proveďte následující kroky:**

1. Na portálu Azure na **Andromeda** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/andromedascm-tutorial/tutorial_andromedascm_samlbase.png)

3. Na **Andromeda domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Andromeda domény a adresy URL jednotné přihlašování informace](./media/andromedascm-tutorial/tutorial_andromedascm_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<tenantURL>.ngcxpress.com/`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Andromeda domény a adresy URL jednotné přihlašování informace](./media/andromedascm-tutorial/tutorial_andromedascm_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`
     
    > [!NOTE] 
    > Předchozí hodnota není skutečné hodnoty. Aktualizujte hodnotu s skutečné identifikátor, adresa URL odpovědi a přihlašování adresy URL, která se vysvětluje dále v tomto kurzu.

5. Aplikace Andromeda očekává SAML kontrolní výrazy ve specifickém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z **uživatelské atributy** části na stránce integrace aplikace. Následující snímek obrazovky ukazuje příklad pro tento.
    
    ![Konfigurovat jednotné přihlašování attb](./media/andromedascm-tutorial/tutorial_andromedascm_attribute.png)

    > [!Important]
    > Zrušte definic obor názvů při těchto nastavení.
    
6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | -------------- | -------------------- |    
    | role        | Role pro konkrétní aplikace |
    | type        | Typ aplikace |
    | Společnosti       | NázevSpolečnosti    |

    > [!NOTE]
    > Nejsou k dispozici skutečné hodnoty. Tyto hodnoty jsou pouze pro ukázku účel, použijte prosím vaše organizace role.

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování přidat](./media/andromedascm-tutorial/tutorial_attribute_04.png)

    ![Konfigurace Addattb přihlášení](./media/andromedascm-tutorial/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d. Ponechte **Namespace** prázdné.
    
    e. Klikněte na tlačítko **OK**.

7. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/andromedascm-tutorial/tutorial_andromedascm_certificate.png) 

8. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/andromedascm-tutorial/tutorial_general_400.png)
    
9. Na **Andromeda konfigurace** klikněte na tlačítko **konfigurace Andromeda** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_configure.png)

10. Přihlašování k webu společnosti Andromeda jako správce.

11. Nahoře řádek nabídek klikněte na **správce** a přejděte do **správy**.

    ![Správce Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

12. Na levé straně panelu nástrojů v části **rozhraní** klikněte na tlačítko **konfigurace SAML**.

    ![Andromeda saml](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

13. Na **konfigurace SAML** části stránky, proveďte následující kroky:

    ![Konfigurace Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Zkontrolujte **povolení přihlášení SSO s SAML**.

    b. V části **Andromeda informace** část, zkopírujte **SP Identity** a vložte ji do **identifikátor** textbox z **Andromeda domény a adresy URL** části.

    c. Kopírování **příjemce URL** a vložte ji do **adresa URL odpovědi** textbox z **Andromeda domény a adresy URL** části.

    d. Kopírování **přihlašovací adresa URL** a vložte ji do **přihlašovací adresa URL** textbox z **Andromeda domény a adresy URL** části.

    e. V části **poskytovatele Identity SAML** zadejte název vaší deklarací identity.

    f. V **jeden znak na koncový bod** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** , který jste zkopírovali z portálu Azure.

    g. Otevřete stažené **certifikát kódovaný v Base64** z portálu Azure v programu Poznámkový blok, vložte jej do **certifikátu X 509** textové pole.
    
    h. Namapujte tyto atributy s odpovídající hodnotou usnadňuje jednotného přihlášení z Azure AD. **ID uživatele** atribut je požadován pro přihlášení. Pro zřizování, **e-mailu**, **společnosti**, **UserType**, a **Role** jsou povinné. V této části jsme definovali atributy mapování (názvu a hodnot) které korelovat těm, které jsou definované v rámci portálu Azure

    ![Andromeda attbmap](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/andromedascm-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/andromedascm-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/andromedascm-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/andromedascm-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-andromeda-test-user"></a>Vytvořit uživatele s Andromeda testu

Cílem této části je vytvoření uživatele v Andromeda nazývá Britta Simon. Andromeda podporuje za běhu zřizování, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Andromeda, pokud ještě neexistuje.

>[!Note]
>Pokud je potřeba ručně vytvořit uživateli, obraťte se na [tým podpory Andromeda klienta](https://www.ngcsoftware.com/support/).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Andromeda.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Andromeda, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Andromeda**.

    ![V seznamu aplikací na Andromeda odkaz](./media/andromedascm-tutorial/tutorial_andromedascm_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Andromeda na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Andromeda.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/andromedascm-tutorial/tutorial_general_01.png
[2]: ./media/andromedascm-tutorial/tutorial_general_02.png
[3]: ./media/andromedascm-tutorial/tutorial_general_03.png
[4]: ./media/andromedascm-tutorial/tutorial_general_04.png

[100]: ./media/andromedascm-tutorial/tutorial_general_100.png

[200]: ./media/andromedascm-tutorial/tutorial_general_200.png
[201]: ./media/andromedascm-tutorial/tutorial_general_201.png
[202]: ./media/andromedascm-tutorial/tutorial_general_202.png
[203]: ./media/andromedascm-tutorial/tutorial_general_203.png
