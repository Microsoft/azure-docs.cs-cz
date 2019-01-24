---
title: 'Kurz: Integrace Azure Active Directory s vytvářením sestav cena Predictix | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a vytváření sestav Predictix cena.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 691d0c43-3aa1-4220-9e46-e7a88db234ad
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: jeedes
ms.openlocfilehash: fb02b83ef64c04063f393368578ef813b0de6073
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54820033"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-price-reporting"></a>Kurz: Integrace Azure Active Directory s vytvářením sestav Predictix cena

V tomto kurzu se dozvíte, jak integrovat Predictix cena generování sestav Azure Active Directory (Azure AD).

Integrace s Azure AD Predictix cena Reporting poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k vytváření sestav cena Predictix.
- Uživatele, aby automaticky získat přihlášení k vytváření sestav cena Predictix (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s vytvářením sestav cena Predictix, potřebujete následující položky:

- Předplatné Azure AD
- Vytváření sestav cena Predictix jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání vytváření sestav cena Predictix z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-predictix-price-reporting-from-the-gallery"></a>Přidání vytváření sestav cena Predictix z Galerie
Konfigurace integrace generování sestav cena Predictix do služby Azure AD, musíte doplnit Predictix cena Reporting z Galerie váš seznam spravovaných aplikací SaaS.

**Přidání vytváření sestav cena Predictix z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Predictix cena Reporting**vyberte **Predictix cena Reporting** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Predictix cena vytváření sestav v seznamu výsledků](./media/predictixpricereporting-tutorial/tutorial_predictixpricereporting_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestovat Azure AD jednotné přihlašování s vytvářením sestav cena Predictix podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co protějšek uživatele ve službě Reporting Predictix cena je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele ve službě Reporting Predictix cena je potřeba navázat.

Ve službě Reporting Predictix cena, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s vytvářením sestav Predictix ceny, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Predictix cena Reporting](#create-a-predictix-price-reporting-test-user)**  – Pokud chcete mít protějšek Britta Simon Predictix cena vytváření sestav, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Predictix cena Reporting.

**Ke konfiguraci Azure AD jednotné přihlašování s vytvářením sestav cena Predictix, proveďte následující kroky:**

1. Na webu Azure Portal na **Predictix cena Reporting** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/predictixpricereporting-tutorial/tutorial_predictixpricereporting_samlbase.png)

1. Na **Predictix cena Reporting domény a adresy URL** části, proveďte následující kroky:

    ![Predictix cena Reporting domény a adresy URL jednotného přihlašování – informace](./media/predictixpricereporting-tutorial/tutorial_predictixpricereporting_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname-pricing>.predictix.com/sso/request`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    
    | |
    |--|
    | `https://<companyname-pricing>.predictix.com` |
    | `https://<companyname-pricing>.dev.predictix.com` |

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory Predictix cena Reporting klienta](https://www.infor.com/company/customer-center/) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/predictixpricereporting-tutorial/tutorial_predictixpricereporting_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/predictixpricereporting-tutorial/tutorial_general_400.png)

1. Na **konfigurace služby Reporting cena Predictix** klikněte na **konfigurovat vytváření sestav cena Predictix** otevřete **nakonfigurovat přihlašování** okno. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace služby Reporting Predictix cena](./media/predictixpricereporting-tutorial/tutorial_predictixpricereporting_configure.png) 

1. Ke konfiguraci jednotného přihlašování na **Predictix cena Reporting** straně, je nutné odeslat na stažený **certifikát (Base64)**, **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby**  k [Reporting cena Predictix tým podpory](https://www.infor.com/company/customer-center/). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/predictixpricereporting-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/predictixpricereporting-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/predictixpricereporting-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/predictixpricereporting-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-predictix-price-reporting-test-user"></a>Vytvoření zkušebního uživatele Predictix cena Reporting

V této části vytvořte uživatele Britta Simon v sestavách Predictix cena. Práce s [Reporting cena Predictix tým podpory](https://www.infor.com/company/customer-center/) přidat uživatele na platformě Predictix cena Reporting.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k vytváření sestav cena Predictix.

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Predictix cena Reporting, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Reporting cena Predictix**.

    ![Vytváření sestav cena Predictix odkaz v seznamu aplikací](./media/predictixpricereporting-tutorial/tutorial_predictixpricereporting_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Reporting Predictix cena na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Predictix cena Reporting.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/predictixpricereporting-tutorial/tutorial_general_01.png
[2]: ./media/predictixpricereporting-tutorial/tutorial_general_02.png
[3]: ./media/predictixpricereporting-tutorial/tutorial_general_03.png
[4]: ./media/predictixpricereporting-tutorial/tutorial_general_04.png

[100]: ./media/predictixpricereporting-tutorial/tutorial_general_100.png

[200]: ./media/predictixpricereporting-tutorial/tutorial_general_200.png
[201]: ./media/predictixpricereporting-tutorial/tutorial_general_201.png
[202]: ./media/predictixpricereporting-tutorial/tutorial_general_202.png
[203]: ./media/predictixpricereporting-tutorial/tutorial_general_203.png

