---
title: 'Kurz: Integrace Azure Active Directory s konektorem JoinNow SecureW2 | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SecureW2 JoinNow konektoru.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2445b3af-f827-40de-9097-6f5c933d0f53
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b367befb90ec28ece963d67b479749e1c8ad363
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175315"
---
# <a name="tutorial-azure-active-directory-integration-with-securew2-joinnow-connector"></a>Kurz: Integrace Azure Active Directory s konektorem JoinNow SecureW2

V tomto kurzu se dozvíte, jak integrovat SecureW2 JoinNow konektoru pomocí Azure Active Directory (Azure AD).

Integrace konektoru JoinNow SecureW2 s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k SecureW2 JoinNow konektoru.
- Uživatele, aby automaticky získat přihlášení k SecureW2 JoinNow konektoru (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s konektorem JoinNow SecureW2, potřebujete následující položky:

- Předplatné Azure AD
- Konektor JoinNow SecureW2 jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání konektoru JoinNow SecureW2 z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-securew2-joinnow-connector-from-the-gallery"></a>Přidání konektoru JoinNow SecureW2 z Galerie
Konfigurace integrace SecureW2 JoinNow konektoru do služby Azure AD, budete muset přidat SecureW2 JoinNow konektor z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat SecureW2 JoinNow konektor z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **SecureW2 JoinNow konektor**vyberte **SecureW2 JoinNow konektor** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![SecureW2 JoinNow konektor v seznamu výsledků](./media/securejoinnow-tutorial/tutorial_securejoinnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí konektoru JoinNow SecureW2 podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek v konektoru JoinNow SecureW2 je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v SecureW2 JoinNow konektoru je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s konektorem JoinNow SecureW2, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele SecureW2 JoinNow konektor](#create-a-securew2-joinnow-connector-test-user)**  – Pokud chcete mít protějšek Britta Simon SecureW2 JoinNow konektor, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci SecureW2 JoinNow konektoru.

**Ke konfiguraci Azure AD jednotné přihlašování s konektorem JoinNow SecureW2, proveďte následující kroky:**

1. Na webu Azure Portal na **SecureW2 JoinNow konektor** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/securejoinnow-tutorial/tutorial_securejoinnow_samlbase.png)

3. Na **SecureW2 JoinNow konektor domény a adresy URL** části, proveďte následující kroky:

    ![SecureW2 JoinNow konektor domény a adresy URL jednotného přihlašování – informace](./media/securejoinnow-tutorial/tutorial_securejoinnow_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<organization-identifier>-auth.securew2.com/auth/saml/SSO`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<organization-identifier>-auth.securew2.com/auth/saml`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory SecureW2 JoinNow konektor klienta](mailto:support@securew2.com) k získání těchto hodnot. 

4. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/securejoinnow-tutorial/tutorial_securejoinnow_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/securejoinnow-tutorial/tutorial_general_400.png)

6. Ke konfiguraci jednotného přihlašování na **SecureW2 JoinNow konektor** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [tým podpory SecureW2 JoinNow konektor](mailto:support@securew2.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/securejoinnow-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/securejoinnow-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/securejoinnow-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/securejoinnow-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-securew2-joinnow-connector-test-user"></a>Vytvoření zkušebního uživatele SecureW2 JoinNow konektoru

V této části vytvořte uživatele Britta Simon v SecureW2 JoinNow konektoru. Práce s [tým podpory SecureW2 JoinNow konektor klienta](mailto:support@securew2.com) přidat uživatele na platformě SecureW2 JoinNow konektoru. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k SecureW2 JoinNow konektoru.

![Přiřazení role uživatele][200] 

**Britta Simon přiřadit SecureW2 JoinNow konektoru, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **SecureW2 JoinNow konektor**.

    ![Odkaz SecureW2 JoinNow konektor v seznamu aplikací](./media/securejoinnow-tutorial/tutorial_securejoinnow_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

**K otestování aplikace, proveďte následující kroky:** 

a. Otevřete klienta SecureW2 JoinNow konektor, vyberte odpovídající zařízení ze seznamu a klikněte na **Sign In** tlačítko.

b. Výchozí prohlížeč by měla otevřít a musí být znovu orientovaného na webu Azure portal pro ověřování.

c. Po úspěšném ověření by měl vrátit zpět na počáteční cílová stránka SecureW2 JoinNow konektoru.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/securejoinnow-tutorial/tutorial_general_01.png
[2]: ./media/securejoinnow-tutorial/tutorial_general_02.png
[3]: ./media/securejoinnow-tutorial/tutorial_general_03.png
[4]: ./media/securejoinnow-tutorial/tutorial_general_04.png

[100]: ./media/securejoinnow-tutorial/tutorial_general_100.png

[200]: ./media/securejoinnow-tutorial/tutorial_general_200.png
[201]: ./media/securejoinnow-tutorial/tutorial_general_201.png
[202]: ./media/securejoinnow-tutorial/tutorial_general_202.png
[203]: ./media/securejoinnow-tutorial/tutorial_general_203.png

