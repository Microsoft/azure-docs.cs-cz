---
title: 'Kurz: Azure Active Directory integrace s SAP Cloudová platforma | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Cloudová platforma SAP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 4d39c8437cbed497e2a2a7e64caa05f8e3d04869
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Kurz: Azure Active Directory integrace s SAP Cloudová platforma

V tomto kurzu zjistěte, jak integrovat SAP Cloudová platforma s Azure Active Directory (Azure AD).

Integrace SAP Cloudová platforma s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k SAP Cloudová platforma.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k prostředí SAP Cloudová platforma (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SAP Cloudová platforma, potřebujete následující položky:

- Předplatné služby Azure AD
- Cloudová platforma SAP jednotné přihlašování povolené předplatné

Po dokončení tohoto kurzu, bude moct jednotné přihlašování do aplikace pomocí uživatele Azure AD, které jste přiřadili SAP Cloudová platforma [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>Potřebujete nasadit vlastní aplikaci nebo přihlášení k aplikaci na vašem účtu SAP Cloudová platforma k testování jednotné přihlašování v odběru. V tomto kurzu je aplikace nasazená v účtu.
> 

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SAP Cloudová platforma z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Přidání SAP Cloudová platforma z Galerie
Konfigurace integrace Cloudová platforma SAP do Azure AD, potřebujete přidat SAP Cloudová platforma z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat SAP Cloudová platforma z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **SAP Cloudová platforma**, vyberte **SAP Cloudová platforma** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![SAP Cloudová platforma v seznamu výsledků](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s SAP Cloudová platforma podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v SAP Cloudová platforma je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské SAP cloudové platformy je potřeba navázat.

U platforem cloudu SAP, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SAP Cloudová platforma, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele SAP Cloudová platforma](#create-a-sap-cloud-platform-test-user)**  – Pokud chcete mít protějšek Britta Simon v SAP Cloudová platforma, která je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci SAP Cloudová platforma.

**Ke konfiguraci Azure AD jednotné přihlašování s SAP Cloudová platforma, proveďte následující kroky:**

1. Na portálu Azure na **SAP Cloudová platforma** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_samlbase.png)

3. Na **SAP cloudové platformy domény a adresy URL** část, proveďte následující kroky:

    ![SAP cloudové platformy domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_url.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL používají vaši uživatelé pro přihlášení do vaší **SAP Cloudová platforma** aplikace. Toto je adresa URL účtu konkrétní chráněného prostředku v aplikaci SAP Cloudová platforma. Adresa URL je založena na vzoru následující: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >Toto je adresa URL v aplikaci SAP Cloudová platforma, která vyžaduje ověření uživatele.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. V **identifikátor** textového pole bude poskytovat vaše Cloudová platforma SAP zadejte adresu URL pomocí jedné z následujících kritérií: 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce:

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
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte s skutečná adresa URL přihlašování, identifikátor a adresa URL odpovědi. Obraťte se na [tým podpory SAP cloudové platformy v klientu](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) získat přihlašovací adresa URL a identifikátor. Adresa URL odpovědi můžete získat z oddílu management vztahu důvěryhodnosti, který je vysvětlen později v tomto kurzu.
    > 
     
4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_400.png)

6. V okně prohlížeče jiný web, přihlaste se na řídicím panelu SAP cloudové platformy v `https://account.<landscape host>.ondemand.com/cockpit`(například: https://account.hanatrial.ondemand.com/cockpit).

7. Klikněte **důvěřovat** kartě.
   
    ![Vztah důvěryhodnosti](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790800.png "vztahu důvěryhodnosti")

8. V části Správa vztah důvěryhodnosti v části **místní poskytovatele služeb**, proveďte následující kroky:

    ![Důvěřování správě](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793931.png "důvěřovat správy")
   
    a. Klikněte na **Upravit**.

    b. Jako **typ konfigurace**, vyberte **vlastní**.

    c. Jako **místní název zprostředkovatele**, ponechte výchozí hodnotu. Tato hodnota zkopírujte a vložte ji do **identifikátor** pole v konfiguraci služby Azure AD pro SAP Cloudová platforma.

    d. Generovat **podpisový klíč** a **certifikát pro podpis** dvojice klíčů, klikněte na tlačítko **Generování páru klíč**.

    e. Jako **hlavní šíření**, vyberte **zakázané**.

    f. Jako **vynucení ověřování**, vyberte **zakázané**.

    g. Klikněte na **Uložit**.

9. Po uložení **místní poskytovatele služeb** nastavení, proveďte následující kroky k získání adresa URL odpovědi:
   
    ![Získat Metadata](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793930.png "získat Metadata")

    a. Stažení souboru metadat SAP Cloudová platforma kliknutím **získat Metadata**.

    b. Otevřete stažený soubor XML SAP Cloudová platforma metadata a vyhledejte **ns3:AssertionConsumerService** značky.
 
    c. Zkopírujte hodnotu **umístění** atribut a vložte ji do **adresa URL odpovědi** pole v konfiguraci služby Azure AD pro SAP Cloudová platforma.

10. Klikněte **důvěryhodného zprostředkovatele Identity** a pak klikněte **přidání důvěryhodného zprostředkovatele Identity**.
   
    ![Důvěřování správě](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790802.png "důvěřovat správy")
   
    >[!NOTE]
    >Ke správě seznamu zprostředkovatelů identity důvěryhodné, budete muset vybrali vlastní konfigurace zadejte v části místní poskytovatele služeb. Výchozí typ konfigurace je nutné upravovat a implicitní vztah důvěryhodnosti ke službě ID SAP. Pro None nemáte žádné nastavení důvěryhodnosti.
    > 
    > 

11. Klikněte **Obecné** a pak klikněte **Procházet** nahrát soubor stažený metadat.
    
    ![Důvěřování správě](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793932.png "důvěřovat správy")
    
    >[!NOTE]
    >Po nahrání souboru metadat, hodnoty **adresy jednotného přihlašování**, **jednu adresu URL odhlašovací**, a **certifikát pro podpis** jsou naplněny automaticky.
    > 
     
12. Klikněte na kartu **Atributy**.

13. Na **atributy** kartu, proveďte následující krok:
    
    ![Atributy](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790804.png "atributy") 

    a. Klikněte na tlačítko **Add Assertion-Based atribut**a poté přidejte následující na základě assertion atributy:
       
    | Kontrolní výraz atribut | Objekt zabezpečení atribut |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |FirstName |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |Příjmení |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |e-mail |
   
     >[!NOTE]
     >Konfigurace atributů závisí na tom, jak aplikace na spojovací bod služby jsou vyvinuté, to znamená, které atributy se očekává v odpovědi SAML a pod které názvem (hlavní atribut) přistupují tento atribut v kódu.
     > 
    
    b. **Výchozí atribut** na snímku obrazovky je jen pro účely obrázku. Chcete-li tento scénář fungovat není potřeba.  
 
    c. Názvy a hodnoty pro **hlavní atribut** ukazuje snímek obrazovky závisí na tom, jak je aplikace vyvinuté. Je možné, že vaše aplikace vyžaduje jiný mapování.

### <a name="assertion-based-groups"></a>Skupiny založené na výrazu

Volitelný krok můžete nakonfigurovat skupiny založené na assertion pro zprostředkovatele Identity Azure Active Directory.

Použití skupin na SAP Cloudová platforma umožňuje dynamicky přiřadit jeden nebo více uživatelů k jedné nebo více rolí v aplikacích SAP Cloudová platforma, určit podle hodnot atributů v kontrolního výrazu SAML 2.0. 

Například, pokud kontrolní výraz obsahuje atribut "*kontrakt = dočasné*", může být vhodné všechny ovlivnění uživatelé mají být přidány do skupiny"*dočasné*". Skupiny "*dočasné*" může obsahovat jednu nebo více rolí z jedné nebo více aplikací, které jsou nasazené v účtu SAP Cloudová platforma.
 
Skupiny založené na assertion použijte, pokud chcete současně přiřadit jednu nebo více rolí aplikací ve vašem účtu SAP Cloudová platforma mnoha uživateli. Pokud chcete přiřadit jenom na jeden nebo malý počet uživatelů pro konkrétní role, doporučujeme, abyste jim přiřadil přímo v "**autorizací**" kartě řídící panel SAP Cloudová platforma.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-sap-cloud-platform-test-user"></a>Vytvoření zkušebního uživatele SAP Cloudová platforma

Pokud chcete povolit uživatelům Azure AD přihlášení a SAP Cloudovou platformu, je třeba přiřadit role v Cloudová platforma SAP k nim.

**Pokud chcete přiřadit role pro uživatele, proveďte následující kroky:**

1. Přihlaste se k vaší **SAP Cloudová platforma** řídící panel.

2. Postupujte takto:
   
    ![Povolení](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790805.png "autorizací")
   
    a. Klikněte na tlačítko **autorizace**.

    b. Klikněte **uživatelé** kartě.

    c. V **uživatele** textovému poli, zadejte e-mailovou adresu uživatele.

    d. Klikněte na tlačítko **přiřadit** přiřadit uživatele k roli.

    e. Klikněte na **Uložit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat tak, že udělíte přístup k SAP Cloudová Platforma Azure jednotné přihlašování.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon SAP Cloudová platforma, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **SAP Cloudová platforma**.

    ![V seznamu aplikací na odkaz SAP Cloudová platforma](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Cloudová platforma SAP na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci SAP Cloudová platforma.


## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_203.png

