---
title: 'Kurz: Azure Active Directory integrace s stránka se stavem | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a stránka se stavem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: 948b55a4a72837c9fb6bdfbc0e45dc0c3ba81feb
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36224748"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Kurz: Azure Active Directory integrace s stránka se stavem

V tomto kurzu zjistěte, jak integrovat stránka se stavem s Azure Active Directory (Azure AD).

Stránka se stavem integraci s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k stránka se stavem
- Můžete povolit uživatelům, aby automaticky získat přihlášení k stránka se stavem (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s stránka se stavem, potřebujete následující položky:

- Předplatné služby Azure AD
- Stránka se stavem jednoho přihlášení povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání stránka se stavem z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-statuspage-from-the-gallery"></a>Přidání stránka se stavem z Galerie
Při konfiguraci integrace stránka se stavem do služby Azure AD potřebujete přidat stránka se stavem z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat stránka se stavem z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **stránka se stavem**.

    ![Vytváření testovacího uživatele Azure AD](./media/statuspage-tutorial/tutorial_statuspage_search.png)

5. Na panelu výsledků vyberte **stránka se stavem**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/statuspage-tutorial/tutorial_statuspage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s stránka se stavem podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v stránka se stavem je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v stránka se stavem musí navázat.

V stránka se stavem, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s stránka se stavem, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele stránka se stavem](#creating-a-statuspage-test-user)**  – Pokud chcete mít protějšek Britta Simon v stránka se stavem, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci stránka se stavem.

**Ke konfiguraci Azure AD jednotné přihlašování s stránka se stavem, proveďte následující kroky:**

1. Na portálu Azure na **stránka se stavem** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/statuspage-tutorial/tutorial_statuspage_samlbase.png)

3. Na **stránka se stavem domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/statuspage-tutorial/tutorial_statuspage_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: 
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |

    > [!NOTE]
    > Obraťte se na tým podpory stránka se stavem v [ SupportTeam@statuspage.io ](mailto:SupportTeam@statuspage.io)požádat o metadat nutné nakonfigurovat jednotné přihlašování. 
    >
    >a. Z metadat, zkopírujte hodnotu vystavitele a vložte ji do **identifikátor** textové pole.
    >
    >b. Z metadat, adresa URL odpovědi zkopírujte a vložte ji do **adresa URL odpovědi** textové pole.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/statuspage-tutorial/tutorial_statuspage_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/statuspage-tutorial/tutorial_general_400.png)

6. Na **stránka se stavem konfigurace** klikněte na tlačítko **konfigurace stránka se stavem** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/statuspage-tutorial/tutorial_statuspage_configure.png) 

7. V jiném okně prohlížeče Přihlaste se k serveru vaší společnosti stránka se stavem jako správce.

8. Na hlavním panelu nástrojů klikněte na tlačítko **spravovat účet**.
   
    ![Konfigurovat jednotné přihlašování](./media/statuspage-tutorial/tutorial_statuspage_06.png) 

10. Klikněte **jednotné přihlašování** kartě. 
   
    ![Konfigurovat jednotné přihlašování](./media/statuspage-tutorial/tutorial_statuspage_07.png) 

11. Na stránce nastavení jednotného přihlašování proveďte následující kroky:
   
    ![Konfigurovat jednotné přihlašování](./media/statuspage-tutorial/tutorial_statuspage_08.png) 

    ![Konfigurovat jednotné přihlašování](./media/statuspage-tutorial/tutorial_statuspage_09.png) 
 
    a. V **jednotné přihlašování cílová adresa URL** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.

    b. V poznámkovém bloku otevřete stažený certifikát, kopírovat obsah a vložte ji do **certifikát** textové pole. 

    c. Klikněte na tlačítko **uložení konfigurace**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/statuspage-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/statuspage-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/statuspage-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/statuspage-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-statuspage-test-user"></a>Vytvoření zkušebního uživatele stránka se stavem

Cílem této části je vytvoření uživatele volal Britta Simon v stránka se stavem.

Stránka se stavem podporuje zřizování za běhu. Již je v povolíte [konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on).

**Pokud chcete vytvořit uživateli volat Britta Simon v stránka se stavem, proveďte následující kroky:**

1. Přihlašování k webu společnosti stránka se stavem jako správce.

2. V nabídce v horní části, klikněte na tlačítko **spravovat účet**.

    ![Konfigurovat jednotné přihlašování](./media/statuspage-tutorial/tutorial_statuspage_06.png)

3. Klikněte **členové týmu** kartě. 
   
    ![Vytváření testovacího uživatele Azure AD](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

4. Klikněte na tlačítko **člen týmu přidat**. 
   
    ![Vytváření testovacího uživatele Azure AD](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

5. Typ **e-mailovou adresu**, **křestní jméno**, a **Sur název** platný uživatele, který chcete mají být zahrnuty do související textových polí. 
   
    ![Vytváření testovacího uživatele Azure AD](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

6. Jako **Role**, zvolte **správce klienta**.

7. Klikněte na tlačítko **vytvořit účet**.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu stránka se stavem.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon stránka se stavem, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **stránka se stavem**.

    ![Konfigurovat jednotné přihlašování](./media/statuspage-tutorial/tutorial_statuspage_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici stránka se stavem na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci stránka se stavem.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/statuspage-tutorial/tutorial_general_01.png
[2]: ./media/statuspage-tutorial/tutorial_general_02.png
[3]: ./media/statuspage-tutorial/tutorial_general_03.png
[4]: ./media/statuspage-tutorial/tutorial_general_04.png

[100]: ./media/statuspage-tutorial/tutorial_general_100.png

[200]: ./media/statuspage-tutorial/tutorial_general_200.png
[201]: ./media/statuspage-tutorial/tutorial_general_201.png
[202]: ./media/statuspage-tutorial/tutorial_general_202.png
[203]: ./media/statuspage-tutorial/tutorial_general_203.png

