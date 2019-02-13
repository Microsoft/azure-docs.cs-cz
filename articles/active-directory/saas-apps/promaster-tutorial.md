---
title: 'Kurz: Integrace Azure Active Directory s ProMaster (podle Inlogik) | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ProMaster (podle Inlogik).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 348dbd37-dc4f-49df-bb90-53d249d456b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 218f0c8a49d99c611219a43cc4b51214e4087012
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56205235"
---
# <a name="tutorial-azure-active-directory-integration-with-promaster-by-inlogik"></a>Kurz: Integrace Azure Active Directory s ProMaster (podle Inlogik)

V tomto kurzu se dozvíte, jak integrovat ProMaster (podle Inlogik) se službou Azure Active Directory (Azure AD).

Integrace ProMaster (podle Inlogik) s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k ProMaster (podle Inlogik).
- Můžete povolit uživatelům, aby automaticky získat přihlášení k ProMaster (podle Inlogik) (jednotné přihlašování) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ProMaster (podle Inlogik), potřebujete následující položky:

- Předplatné Azure AD
- ProMaster (podle Inlogik) jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání ProMaster (podle Inlogik) z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-promaster-by-inlogik-from-the-gallery"></a>Přidání ProMaster (podle Inlogik) z Galerie

Konfigurace integrace ProMaster (podle Inlogik) do služby Azure AD, musíte přidat ProMaster (podle Inlogik) z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat ProMaster (podle Inlogik) z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **ProMaster (podle Inlogik)** vyberte **ProMaster (podle Inlogik)** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![ProMaster (podle Inlogik) v seznamu výsledků](./media/promaster-tutorial/tutorial_promaster_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s ProMaster (podle Inlogik) na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v ProMaster (podle Inlogik) je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v ProMaster (podle Inlogik) musí být vytvořeno.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ProMaster (podle Inlogik), které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele ProMaster (podle Inlogik)](#create-a-promaster-by-inlogik-test-user)**  – Pokud chcete mít protějšek Britta Simon ProMaster (podle Inlogik), který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci ProMaster (podle Inlogik).

**Ke konfiguraci Azure AD jednotné přihlašování s ProMaster (podle Inlogik), proveďte následující kroky:**

1. Na webu Azure Portal na **ProMaster (podle Inlogik)** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/promaster-tutorial/tutorial_promaster_samlbase.png)

3. Na **ProMaster (podle Inlogik) domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![ProMaster (podle Inlogik) domény a adresy URL jednotného přihlašování – informace](./media/promaster-tutorial/tutorial_promaster_url1.png)

    a. V **identifikátor** textové pole, použijte některou z následujících vzor adresy URL:

    | |
    | - |-|
    |  `https://secure.inlogik.com/<COMPANYNAME>`|
    | `https://<CUSTOMDOMAIN>/SAMLBASE`|
    | |

    b. V **adresy URL odpovědi** textové pole, použijte některou z následujících vzor adresy URL:

    | |
    | - |-|
    | `https://secure.inlogik.com/<COMPANYNAME>/saml/acs`|
    | `https://<CUSTOMDOMAIN>/SAMLBASE/saml/acs`|
    | |

4. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![ProMaster (podle Inlogik) domény a adresy URL jednotného přihlašování – informace](./media/promaster-tutorial/tutorial_promaster_url2.png)

    V **přihlašovací adresa URL** textové pole, použijte některou z následujících vzor adresy URL:

    | |
    | - |-|
    | `https://secure.inlogik.com/<COMPANYNAME>/saml/acs `|
    | `https://<CUSTOMDOMAIN>/SAMLBASE/saml/acs`|
    | |

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory ProMaster (podle Inlogik) klienta](mailto:michael.boldiston@inlogik.com) k získání těchto hodnot.

5. Na **podpisový certifikát SAML** klikněte na tlačítko co kopírování **adresa Url federačních metadat aplikace** a vložte ho do poznámkového bloku.

    ![Odkaz ke stažení certifikátu](./media/promaster-tutorial/tutorial_promaster_certificate.png)

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/promaster-tutorial/tutorial_general_400.png)

7. Ke konfiguraci jednotného přihlašování na **ProMaster (podle Inlogik)** straně, je nutné odeslat **adresa Url federačních metadat aplikace** k [tým podpory ProMaster (podle Inlogik)](mailto:michael.boldiston@inlogik.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/promaster-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/promaster-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/promaster-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/promaster-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-promaster-by-inlogik-test-user"></a>Vytvoření zkušebního uživatele ProMaster (podle Inlogik)

V této části vytvoříte uživatele volány Britta Simon v ProMaster (Inlogik). Práce s [tým podpory ProMaster (podle Inlogik)](mailto:michael.boldiston@inlogik.com) přidat uživatele na platformě ProMaster (podle Inlogik). Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k ProMaster (podle Inlogik).

![Přiřazení role uživatele][200]

**Pokud chcete přiřadit Britta Simon ProMaster (podle Inlogik), proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **ProMaster (podle Inlogik)**.

    ![Odkaz ProMaster (podle Inlogik) v seznamu aplikací](./media/promaster-tutorial/tutorial_promaster_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici ProMaster (podle Inlogik) na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci ProMaster (podle Inlogik).
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/promaster-tutorial/tutorial_general_01.png
[2]: ./media/promaster-tutorial/tutorial_general_02.png
[3]: ./media/promaster-tutorial/tutorial_general_03.png
[4]: ./media/promaster-tutorial/tutorial_general_04.png

[100]: ./media/promaster-tutorial/tutorial_general_100.png

[200]: ./media/promaster-tutorial/tutorial_general_200.png
[201]: ./media/promaster-tutorial/tutorial_general_201.png
[202]: ./media/promaster-tutorial/tutorial_general_202.png
[203]: ./media/promaster-tutorial/tutorial_general_203.png

