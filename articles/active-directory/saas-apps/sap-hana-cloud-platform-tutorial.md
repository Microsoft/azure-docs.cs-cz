---
title: 'Kurz: Integrace Azure Active Directory s využitím cloudové platformy SAP | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a cloudové platformy SAPU.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 176ed1718818b24866620d86d7eff4d9af1d378f
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39050870"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Kurz: Integrace Azure Active Directory s využitím cloudové platformy SAP

V tomto kurzu se dozvíte, jak integrovat cloudové platformy SAPU se službou Azure Active Directory (Azure AD).

Integrace cloudové platformy SAPU v Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup ke cloudové platformy SAPU.
- Uživatele, aby automaticky získat přihlášení k cloudové platformy SAPU (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s využitím cloudové platformy SAP, potřebujete následující položky:

- S předplatným služby Azure AD
- Využitím cloudové platformy SAPU jednotného přihlašování povolená předplatného

Po dokončení tohoto kurzu, budou moci jednotné přihlašování do aplikace pomocí uživatele Azure AD, který jste přiřadili do cloudové platformy SAP [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>Budete muset nasaďte svou vlastní aplikaci nebo si chcete otestovat jednotné přihlašování v aplikaci na vašem účtu cloudové platformy SAPU. V tomto kurzu je aplikace nasazená v rámci účtu.
> 

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání cloudové platformy SAPU z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Přidání cloudové platformy SAPU z Galerie
Konfigurace integrace cloudové platformy SAP do Azure AD, budete muset přidat cloudové platformy SAPU v galerii na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat cloudové platformy SAPU z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **cloudové platformy SAPU**vyberte **cloudové platformy SAPU** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Cloudové platformy SAPU v seznamu výsledků](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí SAP Cloudová platforma založená na uživateli test "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v cloudové platformy SAPU je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v cloudové platformy SAPU.

V cloudové platformy SAPU přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s využitím cloudové platformy SAP, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele cloudové platformy SAPU](#create-a-sap-cloud-platform-test-user)**  – Pokud chcete mít protějšek Britta Simon SAP cloudové platformě, která souvisí s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci cloudové platformy SAPU.

**Ke konfiguraci Azure AD jednotné přihlašování s využitím cloudové platformy SAP, proveďte následující kroky:**

1. Na webu Azure Portal na **cloudové platformy SAPU** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_samlbase.png)

3. Na **SAP Cloud Platform domény a adresy URL** části, proveďte následující kroky:

    ![Doména cloudové platformy SAP a adresy URL jednotného přihlašování – informace](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_url.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL použili vašich uživatelů pro přihlášení do vaší **cloudové platformy SAPU** aplikace. Toto je adresa URL účtu konkrétní chráněného prostředku ve vaší aplikaci cloudové platformy SAPU. Adresa URL je založena na vzoru následující: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >Toto je adresa URL, která vyžaduje, aby uživatel k ověření aplikace cloudové platformy SAPU.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. V **identifikátor** textové pole, které vám poskytne vaše cloudové platformy SAPU zadejte adresu URL pomocí jedné z následujících vzorů: 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru:

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL, identifikátoru a adresa URL odpovědi. Kontakt [tým podpory SAP Cloud Platform klienta](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) přihlašovací adresu URL a identifikátor. Adresa URL odpovědi můžete získat z části Správa vztahu důvěryhodnosti, který je vysvětlen později v tomto kurzu.
    > 
     
4. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/sap-hana-cloud-platform-tutorial/tutorial_general_400.png)

6. V okně jiný webový prohlížeč, přihlaste se k SAP řídicí panel Cloud Platform na `https://account.<landscape host>.ondemand.com/cockpit`(například: https://account.hanatrial.ondemand.com/cockpit).

7. Klikněte na tlačítko **důvěřovat** kartu.
   
    ![Důvěřovat](./media/sap-hana-cloud-platform-tutorial/ic790800.png "důvěřovat")

8. V části Správa důvěřovat v rámci **místní poskytovatele služeb**, proveďte následující kroky:

    ![Důvěřovat správu](./media/sap-hana-cloud-platform-tutorial/ic793931.png "důvěřovat správy")
   
    a. Klikněte na **Upravit**.

    b. Jako **typ konfigurace**vyberte **vlastní**.

    c. Jako **místní název zprostředkovatele**, ponechte výchozí hodnotu. Zkopírujte tuto hodnotu a vložte ho do **identifikátor** v konfiguraci Azure AD pro cloudové platformy SAPU.

    d. Ke generování **podpisový klíč** a **certifikát pro podpis** pár klíčů, klikněte na tlačítko **Generování páru klíč**.

    e. Jako **hlavní šíření**vyberte **zakázané**.

    f. Jako **Authentication vynutit**vyberte **zakázané**.

    g. Klikněte na **Uložit**.

9. Po uložení **místní poskytovatele služeb** nastavení, proveďte následující kroky k získání adresy URL odpovědi:
   
    ![Získat Metadata](./media/sap-hana-cloud-platform-tutorial/ic793930.png "získat Metadata")

    a. Kliknutím stáhněte soubor metadat cloudové platformy SAPU **získat Metadata**.

    b. Otevřete stažený soubor metadat XML cloudové platformy SAPU a najděte **ns3:AssertionConsumerService** značky.
 
    c. Zkopírujte hodnotu **umístění** atribut a vložte jej do **adresy URL odpovědi** v konfiguraci Azure AD pro cloudové platformy SAPU.

10. Klikněte na tlačítko **důvěryhodné zprostředkovatele Identity** kartu a potom klikněte na tlačítko **přidat důvěryhodného zprostředkovatele Identity**.
   
    ![Důvěřovat správu](./media/sap-hana-cloud-platform-tutorial/ic790802.png "důvěřovat správy")
   
    >[!NOTE]
    >Ke správě seznamu Důvěryhodné zprostředkovatele identity, budete muset zvolili vlastní typ konfigurace v části místní poskytovatele služeb. Pro typ výchozí konfigurace je nutné jako neupravovatelné a implicitní vztah důvěryhodnosti k ID služby SAP. Pro žádný není nutné žádné nastavení vztahu důvěryhodnosti.
    > 
    > 

11. Klikněte na tlačítko **Obecné** kartu a potom klikněte na tlačítko **Procházet** nahrát soubor stažený metadat.
    
    ![Důvěřovat správu](./media/sap-hana-cloud-platform-tutorial/ic793932.png "důvěřovat správy")
    
    >[!NOTE]
    >Po nahrání souboru metadat, hodnoty **přihlašovací adresu URL jednotného**, **adresu URL jednotného odhlašování**, a **certifikát pro podpis** se vyplní automaticky.
    > 
     
12. Klikněte na kartu **Atributy**.

13. Na **atributy** kartu, proveďte následující kroky:
    
    ![Atributy](./media/sap-hana-cloud-platform-tutorial/ic790804.png "atributy") 

    a. Klikněte na tlačítko **Add Assertion-Based atribut**a poté přidejte následující atributy na základě kontrolního výrazu:
       
    | Atribut kontrolního výrazu | Atribut instanční objekt |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |jméno |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |Příjmení |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |e-mail |
   
     >[!NOTE]
     >Konfigurace atributů závisí na tom, jak tyto aplikace na spojovací bod služby jsou vyvíjeny, to znamená, atributy, které očekávají odpověď SAML a v rámci které název (atribut instanční objekt) přistupují tento atribut v kódu.
     > 
    
    b. **Výchozí atribut** na snímku obrazovky je pouze pro ilustraci. Není potřeba provádět tento scénář fungovat.  
 
    c. Názvy a hodnoty pro **instanční objekt atributu** zobrazené na snímku obrazovky závisí na tom, jak se vyvíjí aplikace. Je možné, že vaše aplikace vyžaduje jiný mapování.

### <a name="assertion-based-groups"></a>Skupiny založené na kontrolní výraz

Jako volitelný krok můžete nakonfigurovat skupiny založené na kontrolní výraz pro zprostředkovatele Identity Azure Active Directory.

Používání skupin v cloudové platformy SAPU vám umožní dynamicky přiřadit jeden nebo více uživatelů k jedné nebo více rolím ve svých aplikacích cloudové platformy SAPU dáno hodnoty atributů v kontrolního výrazu SAML 2.0. 

Například, pokud výraz obsahuje atribut "*kontraktu = dočasné*", můžete chtít všechny ovlivněné uživatele přidat do skupiny"*dočasné*". Skupina "*dočasné*" může obsahovat jednu nebo více rolí z jedné nebo více aplikací, které jsou nasazené ve vašem účtu cloudové platformy SAPU.
 
Použijte skupiny založené na výrazu, když potřebujete mnoho uživatelů současně přiřadit jednu nebo více rolí aplikací ve vašem účtu cloudové platformy SAPU. Pokud chcete přiřadit specifické role pouze na jeden nebo malé počet uživatelů, doporučujeme přiřadíte přímo v "**povolení**" karty řídicí panel cloudové platformy SAPU.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/sap-hana-cloud-platform-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/sap-hana-cloud-platform-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/sap-hana-cloud-platform-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/sap-hana-cloud-platform-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-sap-cloud-platform-test-user"></a>Vytvoření zkušebního uživatele cloudové platformy SAP

Chcete-li povolit Azure AD uživatelům přihlášení do cloudové platformy SAP, je třeba přiřadit role v cloudové platformy SAPU k nim.

**Přiřazení role pro uživatele, proveďte následující kroky:**

1. Přihlaste se k vaší **cloudové platformy SAPU** řídicí panel.

2. Proveďte následující kroky:
   
    ![Povolení](./media/sap-hana-cloud-platform-tutorial/ic790805.png "autorizace")
   
    a. Klikněte na tlačítko **autorizace**.

    b. Klikněte na tlačítko **uživatelé** kartu.

    c. V **uživatele** textového pole zadejte e-mailovou adresu uživatele.

    d. Klikněte na tlačítko **přiřadit** přiřadit uživatele k roli.

    e. Klikněte na **Uložit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup do cloudové platformy SAPU.

![Přiřazení role uživatele][200] 

**Britta Simon přiřadit cloudové platformy SAP, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **cloudové platformy SAPU**.

    ![Odkaz cloudové platformy SAPU v seznamu aplikací](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.

Po kliknutí na dlaždici cloudové platformy SAPU na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci cloudové platformy SAPU.


## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_01.png
[2]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_02.png
[3]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_03.png
[4]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_04.png

[100]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_100.png

[200]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_200.png
[201]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_201.png
[202]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_202.png
[203]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_203.png

