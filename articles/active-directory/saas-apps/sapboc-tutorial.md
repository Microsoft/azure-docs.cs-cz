---
title: 'Kurz: Azure Active Directory integrace s SAP Business objektu cloudu | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP Business objektu cloudu.
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
ms.openlocfilehash: 4848132254736aca12be5e31325d98a936754af2
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36220630"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Kurz: Azure Active Directory integrace s SAP Business objektu cloudu

V tomto kurzu zjistěte, jak integrovat SAP Business objektu cloudu s Azure Active Directory (Azure AD).

Při integraci SAP Business objektu cloudu s Azure AD získáte následující výhody:

- Ve službě Azure AD můžete řídit, kdo má přístup k SAP Business objektu cloudu.
- Uživatelům SAP Business objektu cloudu můžete automaticky přihlásit pomocí jednotného přihlašování a účtu uživatele Azure AD.
- Můžete spravovat své účty pomocí nich centrální umístění, portálu Azure.

Další informace o softwaru jako integraci aplikace služby (SaaS) s Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

K nastavení integrace Azure AD s cloudem objekt SAP Business, potřebujete následující položky:

- Předplatné služby Azure AD
- SAP Business objektu cloudu, pomocí jednotného přihlašování zapnutý

> [!NOTE]
> Pokud testujete kroky v tomto kurzu, doporučujeme, abyste si je vyzkoušeli v provozním prostředí.

Doporučení pro testování kroky v tomto kurzu:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat bezplatnou zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. 

Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidejte SAP Business objektu cloudu z galerie.
2. Nastavte a otestujte Azure AD jednotné přihlašování.

## <a name="add-sap-business-object-cloud-from-the-gallery"></a>Přidat SAP Business objektu cloudu z Galerie
K nastavení integrace SAP Business objektu cloudu s Azure AD, v galerii, přidejte si na seznam spravovaných aplikací SaaS SAP Business objektu cloudu.

Chcete-li přidat SAP Business objektu cloudu z galerie:

1. V [portál Azure](https://portal.azure.com), v nabídce vlevo vyberte **Azure Active Directory**. 

    ![Tlačítko Azure Active Directory][1]

2. Vyberte **podnikové aplikace, které**a potom vyberte **všechny aplikace**.

    ![Stránku podnikových aplikací][2]
    
3. Chcete-li přidat novou aplikaci, vyberte **novou aplikaci**.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **SAP Business objektu cloudu**.

    ![Do vyhledávacího pole](./media/sapboc-tutorial/tutorial_sapboc_search.png)

5. Na panelu výsledků vyberte **SAP Business objektu cloudu**a potom vyberte **přidat**.

    ![SAP Business objektu cloudu v seznamu výsledků](./media/sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="set-up-and-test-azure-ad-single-sign-on"></a>Nastavte a otestujte Azure AD jednotné přihlašování

V této části můžete nastavit a testovací Azure AD jednotného přihlášení SAP Business objektu cloudu podle testovacího uživatele s názvem *Britta Simon*.

Pro jednotné přihlašování pro práci Azure AD musí znát příslušného uživatele Azure AD v cloudu objekt SAP Business. Je nutné vytvořit odkaz vztah mezi uživatele Azure AD a související uživatelské v SAP Business objektu cloudu.

K navázání vztahu odkaz, v cloudu objekt SAP Business pro **uživatelské jméno**, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SAP Business objektu cloudu, proveďte následující úlohy:

1. [Nastavení Azure AD jednotné přihlašování](#set-up-azure-ad-single-sign-on). Nastaví uživatele tak, aby tuto funkci používat.
2. [Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user). Testy Azure AD jednotné přihlašování s uživatelem Britta Simon.
3. [Vytvořit testovací uživatele s SAP Business objektu cloudu](#create-an-sap-business-object-cloud-test-user). Vytvoří protějšek Britta Simon SAP Business objektu cloudu, který je propojený s Azure AD reprezentace uživatele.
4. [Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user). Nastaví Britta Simon používat Azure AD jednotného přihlašování.
5. [Test jednotného přihlašování](#test-single-sign-on). Ověřuje, že konfigurace funguje.

### <a name="set-up-azure-ad-single-sign-on"></a>Nastavení Azure AD jednotné přihlašování

V této části můžete zapnout jedním Azure AD přihlášení na portálu Azure. Potom nastavíte jednotné přihlašování v aplikaci SAP Business objektu cloudu.

Nastavení Azure AD jednotné přihlašování s SAP Business objektu cloudu:

1. Na portálu Azure na **SAP Business objektu cloudu** stránky integrace aplikací, vyberte **jednotného přihlašování**.

    ![Vybrat jednotné přihlašování][4]

2. Na **jednotného přihlašování** stránky, pro **režimu**, vyberte **na základě SAML přihlašování**.
 
    ![Vyberte na základě SAML přihlášení](./media/sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. V části **SAP Business objekt cloudové domény a adresy URL**, proveďte následující kroky:

    1. V **přihlašovací adresa URL** zadejte adresu URL, která má následující vzoru: 
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    2. V **identifikátor** zadejte adresu URL, která má následující vzoru:
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    ![Adresy URL stránky SAP Business objekt cloudové domény a adresy URL](./media/sapboc-tutorial/tutorial_sapboc_url.png)
 
    > [!NOTE] 
    > Hodnoty v těchto adres URL jsou pouze jako ukázka. Aktualizujte hodnoty se skutečné přihlašovací adresa URL a identifikátoru adresy URL. Chcete-li získat adresu URL přihlašování, obraťte se [tým podpory SAP Business objektu cloudu klienta](https://www.sap.com/product/analytics/cloud-analytics.support.html). Identifikátoru adresy URL můžete získat tak, že stáhnete SAP Business objektu cloudu metadat z konzoly správce. To se vysvětluje dále v tomto kurzu. 

4. V části **SAML podpisový certifikát**, vyberte **soubor XML s metadaty**. Uložte soubor metadat ve vašem počítači.

    ![Vyberte soubor XML s metadaty](./media/sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. Vyberte **Uložit**.

    ![Vyberte Uložit.](./media/sapboc-tutorial/tutorial_general_400.png)

6. V okně prohlížeče jiný web Přihlaste se k serveru SAP Business objektu cloudu společnosti jako správce.

7. Vyberte **nabídky** > **systému** > **správy**.
    
    ![Vyberte nabídky, pak systém a Správa](./media/sapboc-tutorial/config1.png)

8. Na **zabezpečení** vyberte **upravit** ikona (pera).
    
    ![Na kartě zabezpečení vyberte ikonu pro úpravy](./media/sapboc-tutorial/config2.png)  

9. Pro **metodu ověřování**, vyberte **SAML jednotné přihlašování (SSO)**.

    ![Vyberte metodu ověřování SAML jednotné přihlašování](./media/sapboc-tutorial/config3.png)  

10. Chcete-li stáhnout metadata služby zprostředkovatele (krok 1), vyberte **Stáhnout**. V souboru metadat, najít a zkopírovat **entityID** hodnotu. Na portálu Azure v části **SAP Business objekt cloudové domény a adresy URL**, vložit hodnotu v **identifikátor** pole.

    ![Zkopírujte a vložte hodnotu entityID](./media/sapboc-tutorial/config4.png)  

11. Nahrát metadat zprostředkovatelů služby (krok 2) v souboru, který jste si stáhli z portálu Azure v části **nahrát zprostředkovatele Identity metadata**, vyberte **nahrát**.  

    ![V části metadata nahrát zprostředkovatele Identity vyberte možnost odeslat](./media/sapboc-tutorial/config5.png)

12. V **atribut uživatele** vyberte atribut uživatele (krok 3), který chcete použít pro implementaci. Tento atribut uživatele se mapuje na zprostředkovatele identit. Pokud chcete zadat vlastní atribut na stránce tohoto uživatele, použijte **vlastní mapování SAML** možnost. Nebo můžete vybrat buď **e-mailu** nebo **ID uživatele** jako atribut uživatele. V našem příkladu jsme vybrali **e-mailu** vzhledem k tomu můžeme namapované identifikátor deklarace identity uživatele s **userprincipalname** atribut **uživatelské atributy** části ve službě Azure portál. To poskytuje e-mail jedinečný uživatele, která se posílají SAP Business objekt cloudových aplikací v každé úspěšné odpovědi SAML.

    ![Vyberte atribut uživatele](./media/sapboc-tutorial/config6.png)

13. Chcete-li ověřit účet pomocí zprostředkovatele identity (krok 4), v **přihlašovací pověření (e-mailu)** zadejte e-mailovou adresu uživatele. Pak vyberte **ověřte účet**. Systém přidá přihlašovací údaje k uživatelskému účtu.

    ![Zadejte e-mailu a vyberte účet, ověřte](./media/sapboc-tutorial/config7.png)

14. Vyberte **Uložit** ikonu.

    ![Ikonu Uložit](./media/sapboc-tutorial/save.png)

> [!TIP]
> Můžete si přečíst stručným verzi tyto pokyny v [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace! Po přidání aplikace tak, že vyberete **služby Active Directory** > **podnikové aplikace, které**, vyberte **jednotné přihlašování** kartě. Dostanete embedded dokumentaci v **konfigurace** části, v dolní části stránky. Další informace najdete v tématu [Azure AD vložených dokumentaci]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD
V této části vytvoříte testovacího uživatele s názvem Britta Simon na portálu Azure.

Vytvoření zkušebního uživatele ve službě Azure AD:

1. Na portálu Azure v levé nabídce vyberte **Azure Active Directory**.

    ![Vytváření testovacího uživatele Azure AD](./media/sapboc-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, vyberte **uživatelů a skupin**a potom vyberte **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/sapboc-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, vyberte **přidat**.
 
    ![Vytváření testovacího uživatele Azure AD](./media/sapboc-tutorial/create_aaduser_03.png) 

4. V **uživatele** dialogové okno pole, proveďte následující kroky:
 
    1. V **název** zadejte **BrittaSimon**.

    2. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    3. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    4. Vyberte **Vytvořit**.

        ![Dialogové okno uživatele](./media/sapboc-tutorial/create_aaduser_04.png) 

    ![Vytvořit uživatele Azure AD][100]

### <a name="create-an-sap-business-object-cloud-test-user"></a>Vytvořit testovací uživatele s SAP Business objektu cloudu

Azure AD Uživatelé musí být zřízená v cloudu objekt SAP Business, než se můžete přihlásit do cloudu objekt SAP Business. V cloudu objekt SAP Business zřizování je ruční úloha.

K poskytnutí uživatelského účtu:

1. Přihlaste se k serveru SAP Business objektu cloudu společnosti jako správce.

2. Vyberte **nabídky** > **zabezpečení** > **uživatelé**.

    ![Můžete přidat zaměstnance](./media/sapboc-tutorial/user1.png)

3. Na **uživatelé** , přidat nové uživatele podrobnosti, vyberte **+**. 

    ![Stránka Přidat uživatele](./media/sapboc-tutorial/user4.png)

    Dokončete následující kroky:

    1. V **ID uživatele** zadejte ID uživatele uživatele, jako je třeba **Britta**.

    2. V **KŘESTNÍ jméno** zadejte jméno uživatele, jako je třeba **Britta**.

    3. V **příjmení** zadejte příjmení uživatele, jako je třeba **Simon**.

    4. V **ZOBRAZOVANÝ název** zadejte úplný jméno uživatele, jako je třeba **Britta Simon**.

    5. V **e-mailu** zadejte e-mailovou adresu uživatele, jako je třeba **brittasimon@contoso.com**.

    6. Na **vybrat role** , vyberte příslušnou roli pro uživatele a pak vyberte **OK**.

      ![Výběr role](./media/sapboc-tutorial/user3.png)

    7. Vyberte **Uložit** ikonu.    


### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části vám umožní uživateli Britta Simon používat Azure AD jednotné přihlašování pomocí udělení přístupu uživatelskému účtu SAP Business objektu cloudu.

Přiřazení Britta Simon SAP Business objektu cloudu:

1. Na portálu Azure otevřete zobrazení aplikace a pak přejděte do zobrazení adresáře. Vyberte **podnikové aplikace, které**a potom vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **SAP Business objektu cloudu**.

    ![Konfigurovat jednotné přihlašování](./media/sapboc-tutorial/tutorial_sapboc_app.png) 

3. V nabídce vlevo vyberte **uživatelů a skupin**.

    ![Vyberte uživatele a skupiny][202] 

4. Vyberte **Přidat**. Potom na **přidat přiřazení** vyberte **uživatelů a skupin**.

    ![Stránka Přidat přiřazení][203]

5. Na **uživatelů a skupin** v seznamu uživatelů, vyberte na stránce **Britta Simon**.

6. Na **uživatelů a skupin** vyberte **vyberte**.

7. Na **přidat přiřazení** vyberte **přiřadit**.

![Přiřadit role uživatele][200] 
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části otestovat vaše konfigurace Azure AD jeden přihlašování pomocí přístupového panelu.

Když vyberete dlaždici SAP Business objektu cloudu na přístupovém panelu, můžete by měl být automaticky přihlášeni do vaší aplikace SAP Business objektu cloudu.

Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS v Azure Active Directory](tutorial-list.md)
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

