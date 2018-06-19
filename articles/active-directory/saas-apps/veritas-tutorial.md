---
title: 'Kurz: Integrace Azure Active Directory této společnosti Enterprise Vault.cloud jednotného přihlašování k | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jednotné přihlašování Vault.cloud Enterprise této společnosti.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: jeedes
ms.openlocfilehash: 7e768f424d439041c5602ba219de5bb3f12dfa0e
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35903307"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Kurz: Integrace Azure Active Directory pomocí této společnosti Enterprise Vault.cloud jednotného přihlašování

V tomto kurzu zjistěte, jak k této společnosti Enterprise Vault.cloud jednotného přihlašování k integraci s Azure Active Directory (Azure AD).

Integrace této společnosti Enterprise Vault.cloud jednotné přihlašování s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k této společnosti Enterprise Vault.cloud jednotného přihlašování
- Můžete povolit uživatelům, aby automaticky získat přihlášení k této společnosti Enterprise Vault.cloud jednotné přihlašování (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD pomocí jednotného přihlašování Vault.cloud Enterprise této společnosti, potřebujete následující položky:

- Předplatné služby Azure AD
- Této společnosti Enterprise Vault.cloud jednotného přihlašování k jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání této společnosti Enterprise Vault.cloud jednotného přihlašování z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Přidání této společnosti Enterprise Vault.cloud jednotného přihlašování z Galerie
Při konfiguraci integrace této společnosti Enterprise Vault.cloud přihlašování do služby Azure AD, potřebujete přidat této společnosti Enterprise Vault.cloud jednotného přihlašování z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat této společnosti Enterprise Vault.cloud jednotného přihlašování z galerie, postupujte takto:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **této společnosti Enterprise Vault.cloud jednotného přihlašování k**.

    ![Vytváření testovacího uživatele Azure AD](./media/veritas-tutorial/tutorial_veritas_search.png)

5. Na panelu výsledků vyberte **této společnosti Enterprise Vault.cloud jednotného přihlašování k**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/veritas-tutorial/tutorial_veritas_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí této společnosti Enterprise Vault.cloud jednotného přihlašování na základě testovací uživatele, nazývá "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co příslušného uživatele v této společnosti Enterprise Vault.cloud jednotné přihlašování je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatele v této společnosti Enterprise Vault.cloud jednotné přihlašování musí navázat.

V této společnosti Enterprise Vault.cloud jednotné přihlašování, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí jednotného přihlašování Vault.cloud Enterprise této společnosti, budete muset provést následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele této společnosti Enterprise Vault.cloud jednotného přihlašování k](#creating-a-veritas-enterprise-vaultcloud-sso-test-user)**  – Pokud chcete mít protějšek Britta Simon v této společnosti Enterprise Vault.cloud jednotného přihlašování k propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v této společnosti Enterprise Vault.cloud jednotného přihlašování k aplikaci.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí jednotného přihlašování Vault.cloud Enterprise této společnosti, proveďte následující kroky:**

1. Na portálu Azure na **této společnosti Enterprise Vault.cloud jednotného přihlašování k** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/veritas-tutorial/tutorial_veritas_samlbase.png)

3. Na **této společnosti Enterprise Vault.cloud jednotného přihlašování k doméně a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/veritas-tutorial/tutorial_veritas_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. V **identifikátor** textovému poli, použijte adresu URL podle datového centra

    | Datové centrum| zprostředkovatele identity |
    |----------|----|
    | Severní Amerika| `https://auth.lax.archivecloud.net` |
    | Evropa | `https://auth.ams.archivecloud.net` |
    | Asie a Tichomoří| `https://auth.syd.archivecloud.net`|

    c. V **adresa URL odpovědi** textovému poli, použijte adresu URL podle datového centra

    | Datové centrum| zprostředkovatele identity |
    |----------|----|
    | Severní Amerika| `https://auth.lax.archivecloud.net` |
    | Evropa | `https://auth.ams.archivecloud.net` |
    | Asie a Tichomoří| `https://auth.syd.archivecloud.net`|
    
    > [!NOTE] 
    > Tato hodnota není skutečné. Aktualizujte tuto hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory této společnosti Enterprise Vault.cloud jednotné přihlašování klienta](https://www.veritas.com/support/.html) získat tuto hodnotu. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/veritas-tutorial/tutorial_veritas_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/veritas-tutorial/tutorial_general_400.png)

6. Na **konfiguraci této společnosti Enterprise Vault.cloud jednotného přihlašování k** klikněte na tlačítko **konfigurace této společnosti Enterprise Vault.cloud SSO** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/veritas-tutorial/tutorial_veritas_configure.png) 

7. Konfigurace jednotného přihlašování na **této společnosti Enterprise Vault.cloud jednotného přihlašování k** straně, budete muset odeslat stažené **Certificate(Base64)** a **SAML jeden přihlašování adresa URL služby** k [tým podpory této společnosti Enterprise Vault.cloud jednotného přihlašování k](https://www.veritas.com/support/.html).

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/veritas-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/veritas-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/veritas-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/veritas-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-veritas-enterprise-vaultcloud-sso-test-user"></a>Vytvoření zkušebního uživatele této společnosti Enterprise Vault.cloud jednotného přihlašování

V této části vytvoříte uživatele volat Britta Simon SSO Vault.cloud Enterprise. Práce s [tým podpory této společnosti Enterprise Vault.cloud jednotného přihlašování k](https://www.veritas.com/support/.html) přidejte uživatele v podniku Vault.cloud jednotného přihlašování k platformě. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon k používání Azure jednotné přihlašování v této společnosti Enterprise Vault.cloud jednotného přihlašování k udělení přístupu.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon této společnosti Enterprise Vault.cloud jednotné přihlašování, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **této společnosti Enterprise Vault.cloud jednotného přihlašování k**.

    ![Konfigurovat jednotné přihlašování](./media/veritas-tutorial/tutorial_veritas_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici této společnosti Enterprise Vault.cloud jednotného přihlašování na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci této společnosti Enterprise Vault.cloud jednotné přihlašování.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/veritas-tutorial/tutorial_general_01.png
[2]: ./media/veritas-tutorial/tutorial_general_02.png
[3]: ./media/veritas-tutorial/tutorial_general_03.png
[4]: ./media/veritas-tutorial/tutorial_general_04.png

[100]: ./media/veritas-tutorial/tutorial_general_100.png

[200]: ./media/veritas-tutorial/tutorial_general_200.png
[201]: ./media/veritas-tutorial/tutorial_general_201.png
[202]: ./media/veritas-tutorial/tutorial_general_202.png
[203]: ./media/veritas-tutorial/tutorial_general_203.png

