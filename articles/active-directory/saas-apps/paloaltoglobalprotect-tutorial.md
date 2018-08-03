---
title: 'Kurz: Integrace Azure Active Directory se službou Palo Alto Networks - GlobalProtect | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Palo Alto Networks - GlobalProtect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 03bef6f2-3ea2-4eaa-a828-79c5f1346ce5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: d3fdf52d07faa4242a0267ebc929946bbc95418a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427614"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---globalprotect"></a>Kurz: Integrace Azure Active Directory se službou Palo Alto Networks - GlobalProtect

V tomto kurzu se dozvíte, jak integrovat Palo Alto Networks - GlobalProtect se službou Azure Active Directory (Azure AD).

Integrace Palo Alto Networks - GlobalProtect s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Palo Alto Networks - GlobalProtect.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Palo Alto Networks - GlobalProtect (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Palo Alto Networks - GlobalProtect, potřebujete následující položky:

- S předplatným služby Azure AD
- Palo Alto Networks – GlobalProtect jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Palo Alto Networks - GlobalProtect z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>Přidání Palo Alto Networks - GlobalProtect z Galerie
Konfigurace integrace Palo Alto Networks - GlobalProtect do služby Azure AD, budete muset přidat Palo Alto Networks - GlobalProtect z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Palo Alto Networks - GlobalProtect z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Palo Alto Networks - GlobalProtect**vyberte **Palo Alto Networks - GlobalProtect** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace .

    ![Palo Alto Networks - GlobalProtect v seznamu výsledků](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Palo Alto Networks – GlobalProtect podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Palo Alto Networks - GlobalProtect je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Palo Alto Networks - GlobalProtect musí být vytvořeno.

V Palo Alto Networks - GlobalProtect, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Palo Alto Networks - GlobalProtect, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření Palo Alto Networks - GlobalProtect testovacího uživatele](#create-a-palo-alto-networks---globalprotect-test-user)**  – Pokud chcete mít protějšek Britta Simon Palo Alto Networks - GlobalProtect, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v Palo Alto Networks - GlobalProtect aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s Palo Alto Networks - GlobalProtect, proveďte následující kroky:**

1. Na webu Azure Portal na **Palo Alto Networks - GlobalProtect** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_samlbase.png)

1. Na **Palo Alto Networks - GlobalProtect domény a adresy URL** části, proveďte následující kroky:

    ![Palo Alto Networks - GlobalProtect domény a adresy URL jednotné přihlašování – informace](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<Customer Firewall URL>`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<Customer Firewall URL>/SAML20/SP`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [Palo Alto Networks - tým podpory GlobalProtect klienta](https://support.paloaltonetworks.com/support) k získání těchto hodnot. 
 
1. Palo Alto Networks - GlobalProtect aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte prosím následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z "**atributy uživatele**" části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro tuto.
    
    ![Konfigurace jednotného přihlašování](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_attribute.png)
    
1. V **atributy uživatele** části na **jednotného přihlašování** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | --- | --- |    
    | uživatelské jméno | user.userprincipalname |

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/paloaltoglobalprotect-tutorial/tutorial_attribute_04.png)

    ![Konfigurace jednotného přihlašování](./media/paloaltoglobalprotect-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek. Jsme změnili hodnotu s user.userprincipalname jako příklad, ale můžete ho namapovat s odpovídající hodnotou. 
    
    d. Klikněte na tlačítko **Ok**


1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/paloaltoglobalprotect-tutorial/tutorial_general_400.png)

1. Otevřete uživatelské rozhraní Správce brány Firewall sítě Palo Alto jako správce v jiném okně prohlížeče.

1. Klikněte na **zařízení**.

    ![Konfigurace Palo Alto jednotného přihlašování](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

1. Vyberte **zprostředkovatele Identity SAML** na levém navigačním panelu a klikněte na tlačítko "Import" k importu souboru metadat.

    ![Konfigurace Palo Alto jednotného přihlašování](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

1. Proveďte následující akce v okně Import

    ![Konfigurace Palo Alto jednotného přihlašování](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. V **název profilu** textového pole zadejte například název Azure AD GlobalProtect.
    
    b. V **metadat zprostředkovatele Identity**, klikněte na tlačítko **Procházet** a vyberte soubor metadata.xml, který jste si stáhli z webu Azure portal
    
    c. Klikněte na tlačítko **OK**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/paloaltoglobalprotect-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/paloaltoglobalprotect-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/paloaltoglobalprotect-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/paloaltoglobalprotect-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-palo-alto-networks---globalprotect-test-user"></a>Vytvoření Palo Alto Networks - GlobalProtect testovacího uživatele

Palo Alto Networks - GlobalProtect zřizování uživatelů podporuje Just-in-time tak uživatele automaticky se vytvoří v systému po úspěšném ověření Pokud zatím již existuje. Není nutné provádět žádnou akci zde. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Palo Alto Networks - GlobalProtect.

![Přiřazení role uživatele][200] 

**Přiřadit Britta Simon Palo Alto Networks - GlobalProtect, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Palo Alto Networks - GlobalProtect**.

    ![Sítě Palo Alto - GlobalProtect odkaz v seznamu aplikací](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na Palo Alto Networks - GlobalProtect dlaždici na přístupovém panelu, vám by měl získat automaticky přihlášení k Palo Alto Networks - GlobalProtect aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_01.png
[2]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_02.png
[3]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_03.png
[4]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_04.png

[100]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_100.png

[200]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_200.png
[201]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_201.png
[202]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_202.png
[203]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_203.png

