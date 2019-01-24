---
title: 'Kurz: Integrace Azure Active Directory s OfficeSpace softwarem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a OfficeSpace softwaru.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 2f299b41e1def13c0d5e646cc515894c85e97a02
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54826696"
---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Kurz: Integrace Azure Active Directory s OfficeSpace softwaru

V tomto kurzu se dozvíte, jak integrovat OfficeSpace Software s Azure Active Directory (Azure AD).

Integrace softwaru OfficeSpace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k softwaru OfficeSpace.
- Uživatele, aby automaticky získat přihlášení k softwaru OfficeSpace (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s OfficeSpace softwaru, potřebujete následující položky:

- Předplatné Azure AD
- OfficeSpace Software jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání OfficeSpace softwaru z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-officespace-software-from-the-gallery"></a>Přidání OfficeSpace softwaru z Galerie
Konfigurace integrace OfficeSpace softwaru do služby Azure AD, budete muset přidat OfficeSpace Software z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat OfficeSpace softwaru z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **OfficeSpace softwaru**vyberte **OfficeSpace softwaru** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![OfficeSpace Software v seznamu výsledků](./media/officespace-tutorial/tutorial_officespace_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a test Azure AD jednotné přihlašování s OfficeSpace Software podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek OfficeSpace softwaru je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské OfficeSpace softwaru je potřeba navázat.

V softwaru OfficeSpace přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s OfficeSpace softwaru, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele softwaru OfficeSpace](#create-a-officespace-software-test-user)**  – Pokud chcete mít protějšek Britta Simon OfficeSpace Software, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci OfficeSpace softwaru.

**Ke konfiguraci Azure AD jednotné přihlašování s OfficeSpace softwaru, proveďte následující kroky:**

1. Na webu Azure Portal na **OfficeSpace softwaru** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/officespace-tutorial/tutorial_officespace_samlbase.png)

1. Na **OfficeSpace softwaru domény a adresy URL** části, proveďte následující kroky:

    ![OfficeSpace softwaru domény a adresy URL jednotného přihlašování – informace](./media/officespace-tutorial/tutorial_officespace_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `<company name>.officespacesoftware.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory OfficeSpace softwarového klienta](mailto:support@officespacesoftware.com) k získání těchto hodnot. 

1. OfficeSpace softwarová aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte prosím následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z "**atributy uživatele**" části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro tuto.
    
    ![Konfigurace atributů](./media/officespace-tutorial/tutorial_officespace_attribute.png)

1. V **atributy uživatele** části na **jednotného přihlašování** dialogového okna, vyberte **user.mail** jako **identifikátor uživatele** a pro každý řádek je znázorněno v v tabulce níže, proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | --- | --- |    
    | e-mail | user.mail |
    | jméno | user.displayname |
    | křestní_jméno | user.givenname |
    | Příjmení | user.surname |

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace přidat ](./media/officespace-tutorial/tutorial_attribute_04.png)

    ![Konfigurace atributů](./media/officespace-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.
    
    d. Klikněte na tlačítko **Ok**
 
1. Na **podpisový certifikát SAML** tématu, zkopírujte **kryptografický OTISK** hodnota certifikátu.

    ![Odkaz ke stažení certifikátu](./media/officespace-tutorial/tutorial_officespace_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/officespace-tutorial/tutorial_general_400.png)

1. Na **konfigurace softwaru OfficeSpace** klikněte na tlačítko **konfigurace softwaru OfficeSpace** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace OfficeSpace softwaru](./media/officespace-tutorial/tutorial_officespace_configure.png) 

1. V okně jiné webové prohlížeče Přihlaste se ke tenanta OfficeSpace softwaru jako správce.

1. Přejděte na **nastavení** a klikněte na tlačítko **konektory**.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/officespace-tutorial/tutorial_officespace_002.png)

1. Klikněte na tlačítko **ověřování SAML**.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/officespace-tutorial/tutorial_officespace_003.png)

1. V **ověřování SAML** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/officespace-tutorial/tutorial_officespace_004.png)

    a. V **adresa url pro odhlášení zprostředkovatele** textového pole vložte hodnotu **odhlašování URL** zkopírovanou z webu Azure portal.

    b. V **klienta zprostředkovatele identity cílové adrese url** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.

    c. Vložit **kryptografický otisk** hodnotu, která jste zkopírovali z portálu Azure portal do **otisk certifikátu klienta IDP** textového pole. 

    d. Klikněte na tlačítko **uložit nastavení**.


> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/officespace-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/officespace-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/officespace-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/officespace-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-officespace-software-test-user"></a>Vytvoření zkušebního uživatele OfficeSpace softwaru

Cílem této části je vytvořte uživatele Britta Simon OfficeSpace softwaru. OfficeSpace Software podporuje just-in-time zřizování, který je ve výchozím nastavení povolená.

Neexistuje žádná položka akce pro vás v této části. Vytvoří se nový uživatel během pokusu o přístup k softwaru OfficeSpace, pokud ještě neexistuje.

> [!NOTE]
> Pokud je potřeba ručně vytvořit uživatele, je potřeba kontaktu [tým podpory softwaru OfficeSpace](mailto:support@officespacesoftware.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k softwaru OfficeSpace.

![Přiřazení role uživatele][200] 

**Britta Simon přiřadit OfficeSpace softwaru, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **OfficeSpace softwaru**.

    ![Odkaz OfficeSpace Software v seznamu aplikací](./media/officespace-tutorial/tutorial_officespace_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici OfficeSpace softwaru na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci OfficeSpace softwaru.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/officespace-tutorial/tutorial_general_01.png
[2]: ./media/officespace-tutorial/tutorial_general_02.png
[3]: ./media/officespace-tutorial/tutorial_general_03.png
[4]: ./media/officespace-tutorial/tutorial_general_04.png

[100]: ./media/officespace-tutorial/tutorial_general_100.png

[200]: ./media/officespace-tutorial/tutorial_general_200.png
[201]: ./media/officespace-tutorial/tutorial_general_201.png
[202]: ./media/officespace-tutorial/tutorial_general_202.png
[203]: ./media/officespace-tutorial/tutorial_general_203.png

