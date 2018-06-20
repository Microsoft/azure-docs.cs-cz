---
title: 'Kurz: Azure Active Directory integrace s iQualify pro správu vzdělávacího procesu | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a iQualify pro správu vzdělávacího procesu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.openlocfilehash: 166dfa8c5b3456de59055e5f62a566064540db31
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225020"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Kurz: Azure Active Directory integrace s iQualify pro správu vzdělávacího procesu

V tomto kurzu zjistěte, jak integrovat iQualify pro správu vzdělávacího procesu s Azure Active Directory (Azure AD).

Integrace iQualify pro správu vzdělávacího procesu s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k iQualify pro správu vzdělávacího procesu.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k iQualify pro správu vzdělávacího procesu (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s iQualify pro správu vzdělávacího procesu, potřebujete následující položky:

- Předplatné služby Azure AD
- IQualify pro správu vzdělávacího procesu jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání iQualify pro správu vzdělávacího procesu z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-iqualify-lms-from-the-gallery"></a>Přidání iQualify pro správu vzdělávacího procesu z Galerie
Při konfiguraci integrace iQualify pro správu vzdělávacího procesu do služby Azure AD potřebujete přidat iQualify pro správu vzdělávacího procesu z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat iQualify pro správu vzdělávacího procesu z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **iQualify pro správu vzdělávacího procesu**, vyberte **iQualify pro správu vzdělávacího procesu** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![iQualify pro správu vzdělávacího procesu v seznamu výsledků](./media/iqualify-tutorial/tutorial_iqualify_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s iQualify pro správu vzdělávacího procesu v závislosti na testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, jaké příslušného uživatele v iQualify pro správu vzdělávacího procesu je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v iQualify pro správu vzdělávacího procesu musí navázat.

V iQualify pro správu vzdělávacího procesu přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s iQualify pro správu vzdělávacího procesu, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovací uživatele pro správu vzdělávacího procesu iQualify](#create-an-iqualify-lms-test-user)**  – Pokud chcete mít protějšek Britta Simon v iQualify pro správu vzdělávacího procesu propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci pro správu vzdělávacího procesu iQualify.

**Ke konfiguraci Azure AD jednotné přihlašování s iQualify pro správu vzdělávacího procesu, proveďte následující kroky:**

1. Na portálu Azure na **iQualify pro správu vzdělávacího procesu** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/iqualify-tutorial/tutorial_iqualify_samlbase.png)

3. Na **iQualify pro správu vzdělávacího procesu domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v režimu rozšíření IDP iniciované:

    ![informace o iQualify pro správu vzdělávacího procesu domény a adresy URL jednotné přihlašování](./media/iqualify-tutorial/tutorial_iqualify_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: 
    | |
    |--|--|
    | Produkční prostředí: `https://<yourorg>.iqualify.com/`|
    | Testovací prostředí: `https://<yourorg>.iqualify.io`|
    
    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: 
    | |
    |--|--|
    | Produkční prostředí: `https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Testovací prostředí: `https://<yourorg>.iqualify.io/auth/saml2/callback` |

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![informace o iQualify pro správu vzdělávacího procesu domény a adresy URL jednotné přihlašování](./media/iqualify-tutorial/tutorial_iqualify_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | |
    |--|--|
    | Produkční prostředí: `https://<yourorg>.iqualify.com/login` |
    | Testovací prostředí: `https://<yourorg>.iqualify.io/login` |
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [tým podpory pro správu vzdělávacího procesu klienta iQualify](https://www.iqualify.com) k získání těchto hodnot. 

5. Aplikace pro správu vzdělávacího procesu iQualify očekává kontrolní výrazy Security (Assertion Markup Language SAML) pro zobrazení v konkrétním formátu. Konfigurace deklarace identity a správa hodnoty atributů v **uživatelské atributy** části iQualify integrace stránky aplikace, jak je znázorněno na následujícím snímku obrazovky:
    
    ![Konfigurovat jednotné přihlašování](./media/iqualify-tutorial/atb.png)

6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno proveďte následující kroky pro každý řádek v tabulce níže:
    
    | Název atributu | Hodnota atributu |
    | --- | --- |    
    | e-mail | user.userprincipalname |
    | křestní_jméno | user.givenname |
    | Příjmení | user.surname |
    | person_id | "atribut" | 

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/iqualify-tutorial/atb2.png)

    ![Konfigurovat jednotné přihlašování](./media/iqualify-tutorial/atb3.png)
    
    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.
    
    d. Klikněte na tlačítko **Ok**

    e. Opakujte kroky "a" pomocí "d" pro další řádky tabulky. 

    > [!Note]
    > Opakováním kroků "a" pomocí "d" pro **person_id** atribut je **volitelné**

7. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base 64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/iqualify-tutorial/tutorial_iqualify_certificate.png) 

8. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/iqualify-tutorial/tutorial_general_400.png)
    
9. Na **iQualify konfigurace pro správu vzdělávacího procesu** klikněte na tlačítko **konfigurace iQualify pro správu vzdělávacího procesu** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL a SAML jeden přihlašování služby URL** z **Stručná referenční příručka části.**

    ![iQualify konfigurace pro správu vzdělávacího procesu](./media/iqualify-tutorial/tutorial_iqualify_configure.png) 

10.  Otevřete nové okno prohlížeče a pak se přihlaste k prostředí iQualify jako správce.

11. Jakmile jste přihlášeni, klikněte na miniatury vpravo nahoře, a potom klikněte na **"Nastavení účtu."**

    ![Nastavení účtu](./media/iqualify-tutorial/setting1.png) 
12. V oblasti nastavení účtu klikněte na pásu karet na levé straně a klikněte na **"Integrace".**
    
    ![INTEGRACE](./media/iqualify-tutorial/setting2.png)

13. V části integrace, klikněte na **SAML** ikonu.

    ![Ikona SAML](./media/iqualify-tutorial/setting3.png)

14. V **nastavení ověřování SAML** dialogové okno pole, proveďte následující kroky:

    ![Nastavení ověřování SAML](./media/iqualify-tutorial/setting4.png)

    a. V **adresy služby jednotného přihlašování SAML** pole, vložte **SAML jednu adresu URL služby Sign‑On** hodnota zkopírována z okna konfigurace aplikace Azure AD.
    
    b. V **adresy URL ODHLAŠOVACÍ SAML** pole, vložte **Sign‑Out URL** hodnota zkopírována z okna konfigurace aplikace Azure AD.
    
    c. V poznámkovém bloku otevřete soubor stažený certifikát, kopírovat obsah a vložte ho **veřejný certifikát** pole.
    
    d. V **POPISKŮ TLAČÍTKA přihlášení** zadejte název pro tlačítko zobrazený na přihlašovací stránku.
    
    e. Klikněte na **ULOŽIT**.

    f. Klikněte na tlačítko **aktualizace**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/iqualify-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/iqualify-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/iqualify-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/iqualify-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-iqualify-lms-test-user"></a>Vytvořit uživatele s iQualify pro správu vzdělávacího procesu testu

V této části se uživatel volá Britta Simon vytvoří v iQualify. iQualify pro správu vzdělávacího procesu podporuje just‑in‑time zřizování uživatelů, který je ve výchozím nastavení povolené.

Neexistuje žádná položka akce pro vás v této části. Pokud uživatel v iQualify ještě neexistuje, vytvoří se nový při pokusu o přístup k iQualify pro správu vzdělávacího procesu.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon chcete použít Azure jednotného přihlašování k udělení přístupu k iQualify pro správu vzdělávacího procesu.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon iQualify pro správu vzdělávacího procesu, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **iQualify pro správu vzdělávacího procesu**.

    ![V seznamu aplikací na iQualify odkaz pro správu vzdělávacího procesu](./media/iqualify-tutorial/tutorial_iqualify_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Po kliknutí na tlačítko iQualify, který pro správu vzdělávacího procesu dlaždici na přístupovém panelu, měli byste obdržet přihlašovací stránku vaší aplikace pro správu vzdělávacího procesu iQualify. 

   ![přihlašovací stránky](./media/iqualify-tutorial/login.png) 

Klikněte na tlačítko **Přihlaste se pomocí Azure AD** tlačítko by měl získat automaticky přihlášení k aplikaci pro správu vzdělávacího procesu iQualify.

Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/iqualify-tutorial/tutorial_general_01.png
[2]: ./media/iqualify-tutorial/tutorial_general_02.png
[3]: ./media/iqualify-tutorial/tutorial_general_03.png
[4]: ./media/iqualify-tutorial/tutorial_general_04.png

[100]: ./media/iqualify-tutorial/tutorial_general_100.png

[200]: ./media/iqualify-tutorial/tutorial_general_200.png
[201]: ./media/iqualify-tutorial/tutorial_general_201.png
[202]: ./media/iqualify-tutorial/tutorial_general_202.png
[203]: ./media/iqualify-tutorial/tutorial_general_203.png

