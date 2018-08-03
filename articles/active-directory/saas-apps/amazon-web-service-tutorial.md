---
title: 'Kurz: Integrace Azure Active Directory Amazon Web Services (AWS) | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: 797be143284566efcefce5ed6c7ded822d5aa97f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438893"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Kurz: Integrace Azure Active Directory Amazon Web Services (AWS)

V tomto kurzu se dozvíte, jak integrovat Amazon Web Services (AWS) pomocí služby Azure Active Directory (Azure AD).

Integrace služby Amazon Web Services (AWS) s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Amazon Web Services (AWS).
- Uživatele, aby automaticky získat přihlášeného k Amazon Web Services (AWS) (jednotné přihlašování) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD, Amazon Web Services (AWS), potřebujete následující položky:

- S předplatným služby Azure AD
- Amazon Web Services (AWS) jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Amazon Web Services (AWS) z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Přidání Amazon Web Services (AWS) z Galerie
Ke konfiguraci integrace služby Amazon Web Services (AWS) do služby Azure AD, budete muset přidat Amazon Web Services (AWS) do seznamu spravovaných aplikací SaaS z galerie.

**Chcete-li přidat Amazon Web Services (AWS) z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Amazon Web Services (AWS)** vyberte **Amazon Web Services (AWS)** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Amazon Web Services (AWS) v seznamu výsledků](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí Amazon Web Services (AWS) na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek Amazon Web Services (AWS) je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské Amazon Web Services (AWS).

V Amazon Web Services (AWS), přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí Amazon Web Services (AWS), které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvořit testovacího uživatele Amazon Web Services (AWS)](#create-an-amazon-web-services-aws-test-user)**  – Pokud chcete mít protějšek Britta Simon v Amazon Web Services (AWS), který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Amazon Web Services (AWS).

**Ke konfiguraci Azure AD jednotné přihlašování pomocí Amazon Web Services (AWS), proveďte následující kroky:**

1. Na webu Azure Portal na **Amazon Web Services (AWS)** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

1. Na **Amazon Web Services (AWS) domény a adresy URL** části uživatel nemá k provedení všech kroků jako aplikace je už předem integrováno s Azure.

    ![Amazon Web Services (AWS) domény a adresy URL jednotného přihlašování – informace](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

1. Amazon Web Services (AWS) softwarová aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z "**atributy uživatele**" části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro tuto.

    ![Konfigurovat Single Sign-On attb](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_attribute.png) 

1. V **atributy uživatele** části na **jednotného přihlašování** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu  | Hodnota atributu | Obor názvů |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Role            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Musíte nakonfigurovat zřizování uživatelů ve službě Azure AD k načtení všech rolí z konzoly AWS. Přečtěte si následující postup zřizování.

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurovat Single Sign-On přidat](./media/amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![Konfigurovat Single Sign-On addattb](./media/amazon-web-service-tutorial/tutorial_attribute_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. V **Namespace** textového pole zadejte hodnotu oboru názvů, který je zobrazený pro tento řádek.
    
    d. Klikněte na tlačítko **OK**.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/amazon-web-service-tutorial/tutorial_general_400.png)

1. V jiném okně prohlížeče přihlašování k webu společnosti Amazon Web Services (AWS) jako správce.

1. Klikněte na tlačítko **AWS domovské**.
   
    ![Konfigurovat Single Sign-On Domovská stránka][11]

1. Klikněte na tlačítko **správu identit a přístupu**. 
   
    ![Konfigurace Identity jednotné přihlašování][12]

1. Klikněte na tlačítko **zprostředkovatelé Identity**a potom klikněte na tlačítko **vytvořit poskytovatele**. 
   
    ![Konfigurace zprostředkovatele přihlášení][13]

1. Na **konfigurovat zprostředkovatele** dialogového okna stránky, proveďte následující kroky: 
   
    ![Konfigurovat Single Sign-On dialogového okna][14]
 
    a. Jako **typ zprostředkovatele**vyberte **SAML**.

    b. V **název zprostředkovatele** textového pole zadejte název zprostředkovatele (například: *WAAD*).

    c. K odeslání vaší stažené **soubor metadat** z webu Azure portal, klikněte na tlačítko **zvolit soubor**.

    d. Klikněte na tlačítko **další krok**.

1. Na **ověřte informace o poskytovateli** dialogového okna stránky, klikněte na tlačítko **vytvořit**. 
    
    ![Konfigurovat Single Sign-On ověření][15]

1. Klikněte na tlačítko **role**a potom klikněte na tlačítko **vytvořit roli**. 
    
    ![Konfiguraci rolí jednotné přihlašování][16]

1. Na **vytvořit roli** stránce, proveďte následující kroky:  
    
    ![Nakonfigurovat jednotné přihlašování vztah důvěryhodnosti][19] 

    a. Vyberte **SAML 2.0 federace** pod **vyberte typ entity pro důvěryhodného**.

    b. V části **zvolte oddíl SAML 2.0 poskytovatele**, vyberte **SAML zprostředkovatele** jste vytvořili dříve (Příklad: *WAAD*)

    c. Vyberte **povolit programové a přístup ke konzole správy AWS**.
  
    d. Klikněte na tlačítko **Další: oprávnění**.

1. Na **připojit zásady oprávnění** dialogového okna, nemusíte připojit žádné zásady. Klikněte na tlačítko **Další: Revize**.  
    
    ![Nakonfigurujte zásady přihlašování][33]

1. Na **revize** dialogového okna, proveďte následující kroky:   
    
    ![Nakonfigurovat jednotné přihlašování revize][34] 

    a. V **název Role** textového pole zadejte název Role.

    b. V **popis Role** textového pole zadejte popis.

    c. Klikněte na tlačítko **vytvořit roli**.

    d. Vytvořte tolik role podle potřeby a jejich namapování na zprostředkovatele Identity.

1. Pomocí přihlašovacích údajů účtu služby AWS pro načtení rolí z účtu AWS v zřizování uživatelů služby Azure AD. V takovém případě otevřete konzolu AWS home.

1. Klikněte na **služby** -> **Security, Identity & Compliance** -> **IAM**.

    ![Načítání rolí z účtu AWS](./media/amazon-web-service-tutorial/fetchingrole1.png)

1. Vyberte **zásady** kartu v oddílu IAM.

    ![Načítání rolí z účtu AWS](./media/amazon-web-service-tutorial/fetchingrole2.png)

1. Vytvořit novou zásadu kliknutím na **vytvořit zásadu** pro načtení rolí z účtu AWS v zřizování uživatelů služby Azure AD.

    ![Vytvoření nové zásady](./media/amazon-web-service-tutorial/fetchingrole3.png)

1. Vytvoření vlastních zásad pro načtení všech rolí z účtů AWS provedením následujících kroků:

    ![Vytvoření nové zásady](./media/amazon-web-service-tutorial/policy1.png)

    a. V **"Vytvořit zásady"** části klikněte na **"JSON"** kartu.

    b. V dokumentu zásad, přidejte následující JSON.
    
    ```
    
    {

    "Version": "2012-10-17",

    "Statement": [

    {

    "Effect": "Allow",
        
    "Action": [
        
    "iam:ListRoles"
        
    ],

    "Resource": "*"

    }

    ]

    }
    
    ```

    c. Klikněte na **zásady revize tlačítko** ověřit zásady.

    ![Definovat nové zásady](./media/amazon-web-service-tutorial/policy5.png)

1. Definovat **nové zásady** provedením následujících kroků:

    ![Definovat nové zásady](./media/amazon-web-service-tutorial/policy2.png)

    a. Zadejte **Název_zásady** jako **AzureAD_SSOUserRole_Policy**.

    b. Můžete zadat **popis** zásad jako **tato zásada vám umožní načíst role z účtů AWS**.
    
    c. Klikněte na **"Vytvořit zásadu"** tlačítko.

1.  Vytvořte nový uživatelský účet ve službě AWS IAM provedením následujících kroků:

    a. Klikněte na **uživatelé** navigace v konzole AWS IAM.

    ![Definovat nové zásady](./media/amazon-web-service-tutorial/policy3.png)
    
    b. Klikněte na **přidat uživatele** pro vytvoření nového uživatele.

    ![Přidání uživatele](./media/amazon-web-service-tutorial/policy4.png)

    c. V **přidat uživatele** části, proveďte následující kroky:
    
    ![Přidání uživatele](./media/amazon-web-service-tutorial/adduser1.png)
    
    * Zadejte uživatelské jméno jako **AzureADRoleManager**.
    
    * Typ přístupu, vyberte **programový přístup** možnost. Tímto způsobem uživatele můžete volat rozhraní API a načíst role z účtu AWS.
    
    * Klikněte na **další oprávnění** tlačítko v pravém dolním rohu.

1. Teď vytvořte novou zásadu pro tohoto uživatele pomocí následujících kroků:

    ![Přidání uživatele](./media/amazon-web-service-tutorial/adduser2.png)
    
    a. Klikněte na **připojit existující zásady přímo** tlačítko.

    b. Vyhledejte nově vytvořenou zásadu do oddílu filtrů **AzureAD_SSOUserRole_Policy**.
    
    c. Vyberte **zásady** a potom klikněte na **Další: Revize** tlačítko.

1.  Projděte si zásady tak, aby připojený uživatel provedením následujících kroků:

    ![Přidání uživatele](./media/amazon-web-service-tutorial/adduser3.png)
    
    a. Zkontrolujte uživatelské jméno, typ přístupu a zásady, které jsou namapované na uživatele.
    
    b. Klikněte na **vytvořit uživatele** tlačítko v pravém dolním rohu pro vytvoření uživatele.

1. Stáhněte si přihlašovací údaje uživatele uživatele tak, že provedete následující kroky:

    ![Přidání uživatele](./media/amazon-web-service-tutorial/adduser4.png)
    
    a. Zkopírujte uživatel **přístup klíče ID** a **tajný přístupový klíč**.
    
    b. Zadejte tyto přihlašovací údaje do služby Azure AD zřizování uživatelů části načíst role z konzoly AWS.
    
    c. Klikněte na **Zavřít** tlačítko dole.

1. Přejděte do **zřizování uživatelů** oddíl aplikace služby Amazon Web Services v portálu pro správu Azure AD.

    ![Přidání uživatele](./media/amazon-web-service-tutorial/provisioning.png)

1. Zadejte **přístupový klíč** a **tajný kód** v **tajný kód klienta** a **tajný klíč tokenu** pole v uvedeném pořadí.

    ![Přidání uživatele](./media/amazon-web-service-tutorial/provisioning1.png)
    
    a. Zadejte přístupový klíč pro uživatele AWS v **clientsecret** pole.
    
    b. Zadejte tajný kód uživatele AWS v **tajný klíč tokenu** pole.
    
    c. Klikněte na **Test připojení** tlačítko by toto připojení úspěšně otestovat.

    d. Uložte nastavení kliknutím na **Uložit** tlačítko v horní části.
 
1. Nyní Ujistěte se, že povolíte stav zřizování **na** v sekci nastavení tím, že přepínač na a pak kliknutím na **Uložit** tlačítko v horní části.

    ![Přidání uživatele](./media/amazon-web-service-tutorial/provisioning2.png)

> [!Note]
> Pokud chcete na integraci více účtů AWS do jednoho účtu Azure pro jednotné přihlašování, najdete [to](https://docs.microsoft.com/azure/active-directory/active-directory-saas-aws-multi-accounts-tutorial) článku. 


### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/amazon-web-service-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/amazon-web-service-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/amazon-web-service-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/amazon-web-service-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>Vytvořit testovacího uživatele Amazon Web Services (AWS)

Cílem této části je vytvořte uživatele Britta Simon Amazon Web Services (AWS). Amazon Web Services (AWS) nemusí uživatel mají být vytvořeny v jejich systému pro jednotné přihlašování, takže není nutné provádět žádnou akci zde.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Amazon Web Services (AWS).

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit Britta Simon na Amazon Web Services (AWS), postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Amazon Web Services (AWS)**.

    ![V seznamu aplikací na odkaz Amazon Web Services (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Amazon Web Services (AWS), na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Amazon Web Services (AWS). Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/amazon-web-service-tutorial/tutorial_general_203.png
[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
