---
title: 'Kurz: Integrace služby Azure Active Directory s ITRP | Dokumenty společnosti Microsoft'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ITRP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: d44391624e29d2bdd182bb07452e0e8def2d1407
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67656694"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Kurz: Integrace služby Azure Active Directory s ITRP

V tomto kurzu se dozvíte, jak integrovat ITRP s Azure Active Directory (Azure AD).
Tato integrace poskytuje tyto výhody:

* Azure AD můžete použít k řízení, kdo má přístup k ITRP.
* Můžete povolit, aby vaši uživatelé byli automaticky přihlášeni k ITRP (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění: na portálu Azure.

Další informace o integraci aplikací SaaS s Azure AD najdete [v tématu Jednotné přihlašování k aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s ITRP, musíte mít:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné ITRP, které má povoleno jednotné přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu budete konfigurovat a testovat jednotné přihlašování Azure AD v testovacím prostředí.

* ITRP podporuje sp-iniciované sso.

## <a name="add-itrp-from-the-gallery"></a>Přidání ITRP z galerie

Chcete-li nastavit integraci ITRP do Azure AD, musíte přidat ITRP z galerie do seznamu spravovaných aplikací SaaS.

1. Na [webu Azure Portal](https://portal.azure.com)v levém podokně vyberte **Službu Azure Active Directory**:

    ![Vyberte Azure Active Directory.](common/select-azuread.png)

2. Přejít na **podnikové aplikace** > **Všechny aplikace**:

    ![Okno podnikových aplikací](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte v horní části okna možnost **Nová aplikace:**

    ![Vybrat novou aplikaci](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ITRP**. Ve výsledcích hledání vyberte **ITRP** a pak vyberte **Přidat**.

     ![Výsledky hledání](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí ITRP pomocí testovacího uživatele s názvem Britta Simon.
Chcete-li povolit jednotné přihlašování, je třeba vytvořit vztah mezi uživatelem Azure AD a odpovídajícím uživatelem v ITRP.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí ITRP, je třeba provést tyto kroky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** tak, aby byla tato funkce povolena pro vaše uživatele.
2. **[Nakonfigurujte jednotné přihlašování ITRP na](#configure-itrp-single-sign-on)** straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlášení Azure AD.
4. **[Přiřaďte testovacímu uživateli Azure AD,](#assign-the-azure-ad-test-user)** aby uživatel povolil jednotné přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele ITRP,](#create-an-itrp-test-user)** který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlášení](#test-single-sign-on)** a ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí ITRP, postupujte takto:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací ITRP vyberte **Jednotné přihlašování**:

    ![Vybrat jednotné přihlašování](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování:

    ![Výběr metody jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** vyberte ikonu **Upravit** a otevřete dialogové okno Základní **konfigurace SAML:**

    ![Ikona úprav](common/edit-urls.png)

4. V dialogovém okně **Základní konfigurace SAML** proveďte následující kroky.

    ![Dialogové okno Základní konfigurace SAML](common/sp-identifier.png)

    1. Do pole **Přihlásit se na adresu URL** zadejte adresu URL v tomto vzoru:
    
       `https://<tenant-name>.itrp.com`

    1. Do pole **Identifikátor (ID entity)** zadejte adresu URL v tomto vzoru:

       `https://<tenant-name>.itrp.com`

    > [!NOTE]
    > Tyto hodnoty jsou zástupné symboly. Musíte použít skutečnou přihlašovací adresu URL a identifikátor. Obraťte se na [tým podpory ITRP](https://www.itrp.com/support) získat hodnoty. Můžete také odkazovat na vzory zobrazené v dialogovém okně **Základní konfigurace SAML** na webu Azure Portal.

5. V části **Podpisový certifikát SAML** vyberte ikonu **Upravit** a otevřete dialogové okno **Podpisový certifikát SAML:**

    ![Ikona úprav](common/edit-certificate.png)

6. V dialogovém okně **podpisový certifikát SAML** zkopírujte hodnotu **kryptografický otisk** a uložte ji:

    ![Zkopírování hodnoty kryptografického otisku](common/copy-thumbprint.png)

7. V části **Nastavit ITRP** zkopírujte příslušné adresy URL na základě vašich požadavků:

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    1. **Adresa URL přihlášení**.

    1. **Identifikátor služby Azure .**

    1. **Adresa URL pro odhlášení**.

### <a name="configure-itrp-single-sign-on"></a>Konfigurace jednotného přihlašování ITRP

1. V novém okně webového prohlížeče se přihlaste k webu společnosti ITRP jako správce.

1. V horní části okna vyberte ikonu **Nastavení:**

    ![Ikona nastavení](./media/itrp-tutorial/ic775570.png "Ikona nastavení")

1. V levém podokně vyberte **Jedno přihlášení**:

    ![Vybrat jednotné přihlašování](./media/itrp-tutorial/ic775571.png "Vybrat jednotné přihlašování")

1. V části **Konfigurace jednotného přihlášení** postupujte podle následujících kroků.

    ![Oddíl jednotného přihlašování](./media/itrp-tutorial/ic775572.png "Oddíl jednotného přihlašování")

    ![Oddíl jednotného přihlašování](./media/itrp-tutorial/ic775573.png "Oddíl jednotného přihlašování")

    1. Vyberte **Povoleno**.

    1. Do pole **Adresa URL vzdáleného odhlášení** vložte hodnotu **adresy URL odhlášení,** kterou jste zkopírovali z portálu Azure.

    1. Do pole **ADRESA URL přisychovaného při správě SAML** vložte hodnotu **adresy URL přihlášení,** kterou jste zkopírovali z portálu Azure.

    1. Do pole **otisků prstů certifikátu** vložte hodnotu **kryptografický otisk** certifikátu, který jste zkopírovali z portálu Azure.

    1. Vyberte **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal vyberte v levém podokně **Službu Azure Active Directory,** vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**:

    ![Vyberte Všichni uživatelé.](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel:**

    ![Vybrat nového uživatele](common/new-user.png)

3. V dialogovém okně **Uživatel** proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    1. Do pole **Název** zadejte **BrittaSimon**.
  
    1. Do pole **Uživatelské jméno** zadejte **BrittaSimon@\<\<> vaší firemní domény. prodloužení>**. (Například BrittaSimon@contoso.com.)

    1. Vyberte **Zobrazit heslo**a poznamenejte si hodnotu, která je v poli **Heslo.**

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části umožníte Britta Simon používat Azure jednotné přihlášení tím, že jí přístup k ITRP.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **ITRP**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **ITRP**.

    ![Seznam žádostí](common/all-applications.png)

3. V levém podokně vyberte **Uživatelé a skupiny**:

    ![Vyberte Uživatelé a skupiny.](common/users-groups-blade.png)

4. V **dialogovém okně Přidat** **přiřazení** vyberte Přidat uživatele a pak vyberte **Uživatelé a skupiny.**

    ![Výběr možnosti Přidat uživatele](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu Uživatelé **Brittu Simonovou** a v dolní části okna klikněte na tlačítko **Vybrat.**

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu. Klepněte na tlačítko **Vybrat** v dolní části okna.

7. V dialogovém okně **Přidat přiřazení** vyberte **Přiřadit**.

### <a name="create-an-itrp-test-user"></a>Vytvoření testovacího uživatele ITRP

Chcete-li povolit uživatelům Azure AD k přihlášení k ITRP, je třeba je přidat do ITRP. Musíte je přidat ručně.

Chcete-li vytvořit uživatelský účet, postupujte takto:

1. Přihlaste se ke svému tenantovi ITRP.

1. V horní části okna vyberte ikonu **Záznamy:**

    ![Ikona Záznamů](./media/itrp-tutorial/ic775575.png "Ikona Záznamů")

1. V nabídce vyberte **Lidé**:

    ![Vybrat lidi](./media/itrp-tutorial/ic775587.png "Vybrat lidi")

1. Výběrem znaménku plus (**+**) přidáte novou osobu:

    ![Výběr znaménku plus](./media/itrp-tutorial/ic775576.png "Výběr znaménku plus")

1. V dialogovém okně **Přidat novou osobu** proveďte následující kroky.

    ![Dialogové okno Přidat novou osobu](./media/itrp-tutorial/ic775577.png "Dialogové okno Přidat novou osobu")

    1. Zadejte název a e-mailovou adresu platného účtu Azure AD, který chcete přidat.

    1. Vyberte **Uložit**.

> [!NOTE]
> Ke zřizování uživatelských účtů Azure AD můžete použít libovolný nástroj pro vytváření uživatelských účtů nebo rozhraní API poskytované ITRP.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Teď je potřeba otestovat konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když vyberete dlaždici ITRP na přístupovém panelu, měli byste být automaticky přihlášeni k instanci ITRP, pro kterou nastavíte přiřazovat služby. Další informace o přístupovém panelu najdete v [tématu Přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Kurzy integrace aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
