---
title: 'Kurz: Integrace Azure Active Directory se SAP cloudem objekt Business | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP Business objekt cloudu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: ffd4480a13549caba17becff27a43f51fcaa1988
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041734"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Kurz: Integrace Azure Active Directory se SAP Business objekt cloudem

V tomto kurzu se dozvíte, jak integrovat SAP Business objekt cloudu s Azure Active Directory (Azure AD).

Při integraci SAP Business objekt cloudu s Azure AD, získáte následující výhody:

- Ve službě Azure AD můžete řídit, kdo má přístup k SAP Business objekt cloudu.
- Uživatelům SAP Business objekt cloudu můžete automaticky přihlásit pomocí jednotného přihlašování a k účtu služby Azure AD.
- Můžete spravovat své účty v jednom, centrálním místě na webu Azure portal.

Další informace o softwaru jako integraci služby (SaaS) aplikací s Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Postup nastavení integrace služby Azure AD se SAP Business objekt cloudem, potřebujete následující položky:

- S předplatným služby Azure AD
- SAP Business objekt cloudu s více než jednotné přihlašování zapnuto

> [!NOTE]
> Pokud testovací kroky v tomto kurzu, doporučujeme, není jejich testování v produkčním prostředí.

Doporučení pro testovací kroky v tomto kurzu:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat bezplatnou zkušební verzi měsíčního](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. 

Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. SAP Business objekt cloudu přidejte z galerie.
2. Nastavení a testování Azure AD jednotného přihlašování.

## <a name="add-sap-business-object-cloud-from-the-gallery"></a>Přidání SAP Business objekt Cloud z Galerie
Nastavení integrace SAP Business objekt cloudu s Azure AD v galerii, přidejte si na seznam spravovaných aplikací SaaS SAP Business objekt cloudu.

Chcete-li přidat SAP Business objekt Cloud z galerie:

1. V [webu Azure portal](https://portal.azure.com), v nabídce vlevo vyberte **Azure Active Directory**. 

    ![Tlačítko Azure Active Directory][1]

2. Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Stránka aplikace organizace][2]
    
3. Chcete-li přidat novou aplikaci, **novou aplikaci**.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **SAP Business objekt cloudu**.

    ![Do vyhledávacího pole](./media/sapboc-tutorial/tutorial_sapboc_search.png)

5. Na panelu výsledků vyberte **SAP Business objekt cloudu**a pak vyberte **přidat**.

    ![SAP Business objekt cloudu v seznamu výsledků](./media/sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="set-up-and-test-azure-ad-single-sign-on"></a>Nastavení a testování Azure AD jednotného přihlašování

V této části nastavíte a testovací služby Azure AD jednotného přihlašování se SAP cloudem objekt firmy podle testovacího uživatele s názvem *Britta Simon*.

Azure AD pro jednotné přihlašování pro práci, potřebuje vědět protějšek uživatele Azure AD v cloudu SAP Business objektu. Vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatelů v cloudu objekt SAP Business musí vytvořit.

K navázání vztahu odkazu, v cloudu pro SAP Business objektu, pro **uživatelské jméno**, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se SAP Business objekt cloudem, proveďte následující úlohy:

1. [Nastavení Azure AD jednotného přihlašování](#set-up-azure-ad-single-sign-on). Nastaví uživatele tak, aby tuto funkci používat.
2. [Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user). Zkoušky Azure AD jednotné přihlašování s uživatelem Britta Simon.
3. [Vytvořit testovacího uživatele SAP Business objekt cloudu](#create-an-sap-business-object-cloud-test-user). Vytvoří protějšek Britta Simon v SAP Business objekt cloudu, který je propojený s Azure AD zastoupení uživatele.
4. [Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user). Nastaví Britta Simon používání Azure AD jednotného přihlašování.
5. [Otestovat jednotné přihlašování](#test-single-sign-on). Ověřuje, že konfigurace funguje.

### <a name="set-up-azure-ad-single-sign-on"></a>Nastavení Azure AD jednotného přihlašování

V této části posunete se v Azure AD jednoho přihlašování na portálu Azure portal. Poté nastavíte jednotné přihlašování v SAP Business objekt cloudové aplikace.

Nastavení Azure AD jednotného přihlašování se SAP cloudem objekt firmy:

1. Na webu Azure Portal na **SAP Business objekt cloudu** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Vyberte jednotného přihlašování][4]

2. Na **jednotného přihlašování** stránky, pro **režimu**vyberte **přihlašování na základě SAML**.
 
    ![Vyberte přihlašování na základě SAML](./media/sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. V části **SAP Business objekt Cloudová doména a adresy URL**, proveďte následující kroky:

    1. V **přihlašovací adresa URL** zadejte adresu URL, která má následujícímu vzoru: 
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    2. V **identifikátor** zadejte adresu URL, která má následujícímu vzoru:
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    ![SAP Business objekt Cloudová doména a adresy URL adresy URL stránky](./media/sapboc-tutorial/tutorial_sapboc_url.png)
 
    > [!NOTE] 
    > Hodnoty v těchto adres URL jsou pouze ukázku. Aktualizujte hodnoty skutečné přihlašovací adresu URL a identifikátoru adresy URL. Získat adresu URL přihlašování, obraťte se [tým podpory SAP Business objekt cloudu klienta](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/en-US). Identifikátoru adresy URL můžete získat stažením metadata SAP Business objekt cloudu z konzoly pro správu. To je vysvětleno dále v tomto kurzu. 

4. V části **podpisový certifikát SAML**vyberte **soubor XML s metadaty**. Uložte soubor metadat ve vašem počítači.

    ![Vyberte soubor XML s metadaty](./media/sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. Vyberte **Uložit**.

    ![Vyberte Uložit.](./media/sapboc-tutorial/tutorial_general_400.png)

6. V okně jiné webové prohlížeče Přihlaste se k serveru SAP Business objekt cloudu společnosti jako správce.

7. Vyberte **nabídky** > **systému** > **správu**.
    
    ![Vyberte nabídky, pak systém a Správa](./media/sapboc-tutorial/config1.png)

8. Na **zabezpečení** kartu, vyberte **upravit** ikony (pero).
    
    ![Na kartě zabezpečení vyberte ikonu pro úpravy](./media/sapboc-tutorial/config2.png)  

9. Pro **metodu ověřování**vyberte **SAML jednotné přihlašování (SSO)**.

    ![Vyberte metodu ověřování SAML jednotného přihlašování](./media/sapboc-tutorial/config3.png)  

10. Chcete-li stáhnout metadata poskytovatele služby (krok 1), vyberte **Stáhnout**. V souboru metadat, vyhledejte a zkopírujte **entityID** hodnotu. Na webu Azure Portal v části **SAP Business objekt Cloudová doména a adresy URL**, vložte tuto hodnotu v **identifikátor** pole.

    ![Zkopírujte a vložte tuto hodnotu entityID](./media/sapboc-tutorial/config4.png)  

11. Nahrát zprostředkovatele metadat služby (krok 2) v souboru, který jste si stáhli z webu Azure portal v části **metadat zprostředkovatele Identity nahrát**vyberte **nahrát**.  

    ![V části metadata uložit zprostředkovatele Identity výběr nahrání](./media/sapboc-tutorial/config5.png)

12. V **atribut uživatele** vyberte atribut uživatele (krok 3), který chcete použít pro implementaci. Tento atribut uživatele se mapuje na zprostředkovatele identity. Chcete-li zadat vlastní atribut na stránku uživatele, použijte **vlastní mapování SAML** možnost. Nebo můžete vybrat, zda **e-mailu** nebo **ID uživatele** jako atribut uživatele. V našem příkladu jsme vybrali **e-mailu** protože jsme namapované identifikátor deklarace identity uživatele s **userprincipalname** atribut **atributy uživatele** oddíl ve službě Azure portál. Tímto způsobem e-mail jedinečných uživatelů, která se posílají do SAP Business objekt cloudové aplikace v každé úspěšné odpovědi SAML.

    ![Vyberte atribut uživatele](./media/sapboc-tutorial/config6.png)

13. Chcete-li ověřit účet pomocí zprostředkovatele identity (krok 4), v **přihlašovacích údajů (E-mail)** zadejte e-mailovou adresu uživatele. Vyberte **ověřte účet**. Systém přidá přihlašovací údaje uživatelského účtu.

    ![Zadejte e-mailu a vyberte ověření účtu](./media/sapboc-tutorial/config7.png)

14. Vyberte **Uložit** ikonu.

    ![Ikonu Uložit](./media/sapboc-tutorial/save.png)

> [!TIP]
> Stručné verzi těchto pokynů si můžete přečíst [webu Azure portal](https://portal.azure.com), když nastavujete aplikace! Poté, co přidáte aplikaci tak, že vyberete **služby Active Directory** > **podnikové aplikace**, vyberte **Single Sign-On** kartu. Přistupujete k dokumentaci vložený v **konfigurace** části, v dolní části stránky. Další informace najdete v tématu [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD
V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

Chcete-li vytvořit testovacího uživatele ve službě Azure AD:

1. Na webu Azure Portal, v nabídce vlevo vyberte **Azure Active Directory**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/sapboc-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, vyberte **uživatelů a skupin**a pak vyberte **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/sapboc-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogu **přidat**.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/sapboc-tutorial/create_aaduser_03.png) 

4. V **uživatele** dialogové okno pole, proveďte následující kroky:
 
    1. V **název** zadejte **BrittaSimon**.

    2. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    3. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    4. Vyberte **Vytvořit**.

        ![Dialogové okno uživatele](./media/sapboc-tutorial/create_aaduser_04.png) 

    ![Vytvoření uživatele Azure AD][100]

### <a name="create-an-sap-business-object-cloud-test-user"></a>Vytvořit testovacího uživatele SAP Business objekt cloudu

Uživatelé Azure AD musí být poskytnuty v cloudu objekt SAP Business předtím, než se mohl přihlásit k SAP Business objekt cloudu. V SAP Business objekt cloudu zřizování je ruční úloha.

Chcete-li vytvořit uživatelský účet:

1. Přihlaste se k serveru SAP Business objekt cloudu společnosti jako správce.

2. Vyberte **nabídky** > **zabezpečení** > **uživatelé**.

    ![Přidat zaměstnance](./media/sapboc-tutorial/user1.png)

3. Na **uživatelé** stránky a přidat nové podrobnosti o uživateli, vyberte **+**. 

    ![Stránka Přidat uživatele](./media/sapboc-tutorial/user4.png)

    Potom proveďte následující kroky:

    1. V **ID uživatele** zadejte ID uživatele uživatele, jako je třeba **Britta**.

    2. V **KŘESTNÍ jméno** zadejte jméno uživatele, jako je třeba **Britta**.

    3. V **příjmení** zadejte příjmení uživatele, jako je třeba **Simon**.

    4. V **ZOBRAZOVANÝ název** zadejte celé jméno uživatele, jako je třeba **Britta Simon**.

    5. V **e-mailu** zadejte e-mailovou adresu uživatele, jako je třeba **brittasimon@contoso.com**.

    6. Na **vybrat role** stránky, vyberte vhodnou roli pro uživatele a pak vyberte **OK**.

      ![Výběr role](./media/sapboc-tutorial/user3.png)

    7. Vyberte **Uložit** ikonu.    


### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části vám umožní uživateli Britta Simon pomocí služby Azure AD jednotného přihlašování udělení přístupu uživatelskému účtu k SAP Business objekt cloudu.

Přiřadit Cloud SAP Business objekt Britta Simon:

1. Na webu Azure Portal otevřete zobrazení aplikace a pak přejděte do zobrazení adresáře. Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **SAP Business objekt cloudu**.

    ![Konfigurace jednotného přihlašování](./media/sapboc-tutorial/tutorial_sapboc_app.png) 

3. V nabídce vlevo vyberte **uživatelů a skupin**.

    ![Vyberte uživatele a skupiny][202] 

4. Vyberte **Přidat**. Potom na **přidat přiřazení** stránce **uživatelů a skupin**.

    ![Stránka Přidat přiřazení][203]

5. Na **uživatelů a skupin** v seznamu uživatelů, vyberte na stránce **Britta Simon**.

6. Na **uživatelů a skupin** stránce **vyberte**.

7. Na **přidat přiřazení** stránce **přiřadit**.

![Přiřazení role uživatele][200] 
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Při výběru dlaždice SAP Business objekt cloudem na přístupovém panelu, můžete by měl být automaticky přihlášeni k SAP Business objekt cloudové aplikace.

Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/sapboc-tutorial/tutorial_general_01.png
[2]: ./media/sapboc-tutorial/tutorial_general_02.png
[3]: ./media/sapboc-tutorial/tutorial_general_03.png
[4]: ./media/sapboc-tutorial/tutorial_general_04.png

[100]: ./media/sapboc-tutorial/tutorial_general_100.png

[200]: ./media/sapboc-tutorial/tutorial_general_200.png
[201]: ./media/sapboc-tutorial/tutorial_general_201.png
[202]: ./media/sapboc-tutorial/tutorial_general_202.png
[203]: ./media/sapboc-tutorial/tutorial_general_203.png

