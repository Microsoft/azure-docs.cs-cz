---
title: 'Kurz: Azure Active Directory integrace s Amazon Web Services (AWS) | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: jeedes
ms.openlocfilehash: 92189eba7df49aa45adaee7ee3c93c8972b5594b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Kurz: Azure Active Directory integrace s Amazon Web Services (AWS)

V tomto kurzu zjistěte, jak integrovat Amazon Web Services (AWS) s Azure Active Directory (Azure AD).

Integrace Amazon Web Services (AWS) s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Amazon Web Services (AWS).
- Můžete povolit uživatelům, aby automaticky získat přihlášeného k Amazon Web Services (AWS) (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD pomocí Amazon Web Services (AWS), potřebujete následující položky:

- Předplatné služby Azure AD
- Amazon Web Services (AWS) jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Amazon Web Services (AWS) z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Přidání Amazon Web Services (AWS) z Galerie
Při konfiguraci integrace služby Amazon Web Services (AWS) do služby Azure AD, musíte přidat do seznamu spravovaných aplikací SaaS Amazon Web Services (AWS) z galerie.

**Pokud chcete přidat Amazon Web Services (AWS) z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Amazon Web Services (AWS)**, vyberte **Amazon Web Services (AWS)** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Amazon Web Services (AWS) v seznamu výsledků](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí Amazon Web Services (AWS) podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Amazon Web Services (AWS) je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah propojení mezi uživatele Azure AD a související uživatelské Amazon Web Services (AWS).

V Amazon Web Services (AWS), přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí Amazon Web Services (AWS), je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovací uživatele s Amazon Web Services (AWS)](#create-an-amazon-web-services-aws-test-user)**  – Pokud chcete mít protějšek Britta Simon v Amazon Web Services (AWS), propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Amazon Web Services (AWS).

**Pokud chcete konfigurovat Azure AD jednotné přihlašování pomocí Amazon Web Services (AWS), proveďte následující kroky:**

1. Na portálu Azure na **Amazon Web Services (AWS)** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. Na **Amazon Web Services (AWS) domény a adresy URL** části uživatel nemusí provádět žádné kroky, protože aplikace je už předem integrováno s Azure.

    ![Amazon Web Services (AWS) domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. Aplikace Software Amazon Web Services (AWS) očekává SAML kontrolní výrazy ve specifickém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z "**uživatelské atributy**" části na stránce integrace aplikace. Následující snímek obrazovky ukazuje příklad pro tento.

    ![Konfigurovat jednotné přihlašování attb](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_attribute.png)   

5. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu  | Hodnota atributu | Obor názvů |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Role            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Musíte nakonfigurovat zřizování uživatelů ve službě Azure AD se načíst všechny role z konzoly AWS. Najdete v následujících zřizování kroků.

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování přidat](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![Konfigurovat jednotné přihlašování addattb](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d. V **Namespace** textovému poli, zadejte hodnotu oboru názvů, který je uvedený na příslušném řádku.
    
    d. Klikněte na tlačítko **OK**.

6. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. V okně jiný prohlížeč přihlašování k webu společnosti Amazon Web Services (AWS) jako správce.

9. Klikněte na tlačítko **konzoly domovské**.
   
    ![Konfigurace domovské jednotné přihlašování][11]

10. Klikněte na tlačítko **identita a správa přístupu**. 
   
    ![Konfiguraci Identity přihlášení][12]

11. Klikněte na tlačítko **zprostředkovatelů Identity**a potom klikněte na **vytvoření zprostředkovatele**. 
   
    ![Konfigurace zprostředkovatele přihlášení][13]

12. Na **konfigurace zprostředkovatele** dialogové okno stránky, proveďte následující kroky: 
   
    ![Konfigurovat jednotné přihlašování v dialogovém okně][14]
 
    a. Jako **typ zprostředkovatele**, vyberte **SAML**.

    b. V **název zprostředkovatele** textovému poli, zadejte název zprostředkovatele (například: *službou WAAD*).

    c. Nahrát váš stažené **soubor metadat** z portálu Azure, klikněte na tlačítko **zvolit soubor**.

    d. Klikněte na tlačítko **dalším krokem**.

13. Na **ověřte informace o poskytovateli** dialogové okno stránky, klikněte na tlačítko **vytvořit**. 
    
    ![Konfigurovat jednotné přihlašování ověření][15]

14. Klikněte na tlačítko **role**a potom klikněte na **vytvořit novou roli**. 
    
    ![Konfiguraci rolí přihlášení][16]

15. Na **nastavit název Role** dialogové okno, proveďte následující kroky: 
    
    ![Konfigurace názvu přihlášení][17] 

    a. V **název Role** textovému poli, zadejte název role (například: *TestUser*). 

    b. Klikněte na tlačítko **dalším krokem**.

16. Na **vyberte typ Role** dialogové okno, proveďte následující kroky: 
    
    ![Nakonfigurujte typ Role přihlášení][18] 

    a. Vyberte **Role pro přístup poskytovatele Identity**. 

    b. V **Grant webové jednotné přihlašování (WebSSO) přístup k poskytovatelům SAML** klikněte na tlačítko **vyberte**.

17. Na **navázání vztahu důvěryhodnosti** dialogové okno, proveďte následující kroky:  
    
    ![Konfigurace důvěryhodnosti přihlášení][19] 

    a. Jako poskytovatel SAML vybrat zprostředkovatele SAML jste dříve vytvořili (například: *službou WAAD*) 
  
    b. Klikněte na tlačítko **dalším krokem**.

18. Na **ověřte důvěřovat Role** dialogové okno, klikněte na tlačítko **další krok**. 
    
    ![Konfigurovat vztah důvěryhodnosti Role přihlášení][32]

19. Na **připojit zásady** dialogové okno, klikněte na tlačítko **další krok**.  
    
    ![Nakonfigurujte zásady přihlášení][33]

20. Na **zkontrolujte** dialogové okno, proveďte následující kroky:   
    
    ![Konfigurace zkontrolujte přihlášení][34] 

    a. Klikněte na tlačítko **vytvořit roli**.

    b. Vytvořit tolik role podle potřeby a jejich namapování na zprostředkovatele Identity.

21. Pomocí pověření účtu služby AWS pro načítání role z účtu AWS v zřizování uživatele Azure AD. V takovém případě otevřete konzolu AWS domácí.

22. Klikněte na **služby** -> **zabezpečení, Identity a dodržování předpisů** -> **IAM**.

    ![načítání z účtu AWS role](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole1.png)

23. Vyberte **zásady** kartě v části IAM.

    ![načítání z účtu AWS role](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole2.png)

24. Kliknutím na vytvořit novou zásadu **vytvořit zásadu**.

    ![Vytvoření nové zásady](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole3.png)
 
25. Vytvořte vlastní zásadu načíst všechny role z AWS účty tak, že provedete následující kroky:

    ![Vytvoření nové zásady](./media/active-directory-saas-amazon-web-service-tutorial/policy1.png)

    a. V **"Vytvořit zásady"** části klikněte na **"JSON"** kartě.

    b. V dokumentu zásady, přidejte níže JSON.
    
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

    c. Klikněte na **zkontrolujte zásady tlačítko** se ověřit zásady.

    ![Definovat nové zásady](./media/active-directory-saas-amazon-web-service-tutorial/policy5.png)

26. Definování **nové zásady** provedením následujících kroků:

    ![Definovat nové zásady](./media/active-directory-saas-amazon-web-service-tutorial/policy2.png)

    a. Zadejte **název zásady** jako **AzureAD_SSOUserRole_Policy**.

    b. Můžete zadat **popis** zásad jako **tyto zásady vám umožní načíst role z účtů AWS**.
    
    c. Klikněte na **"Vytvořit zásadu"** tlačítko.
        
27. Vytvořte nový uživatelský účet ve službě IAM AWS provedením následujících kroků:

    a. Klikněte na **uživatelé** navigace v konzole AWS IAM.

    ![Definovat nové zásady](./media/active-directory-saas-amazon-web-service-tutorial/policy3.png)
    
    b. Klikněte na **přidat uživatele** tlačítko pro vytvoření nového uživatele.

    ![Přidání uživatele](./media/active-directory-saas-amazon-web-service-tutorial/policy4.png)

    c. V **přidat uživatele** část, proveďte následující kroky:
    
    ![Přidání uživatele](./media/active-directory-saas-amazon-web-service-tutorial/adduser1.png)
    
    * Zadejte uživatelské jméno jako **AzureADRoleManager**.
    
    * V typu přístupu, vyberte **programový přístup** možnost. Tímto způsobem uživatele můžete volat rozhraní API a načíst role z účtu AWS.
    
    * Klikněte na **další oprávnění** tlačítko v pravém horním rohu.

28. Teď vytvořte novou zásadu pro tohoto uživatele tak, že provedete následující kroky:

    ![Přidání uživatele](./media/active-directory-saas-amazon-web-service-tutorial/adduser2.png)
    
    a. Klikněte na **přímo připojit existující zásady** tlačítko.

    b. Vyhledejte nově vytvořenou zásadu v části filtru **AzureAD_SSOUserRole_Policy**.
    
    c. Vyberte **zásad** a potom klikněte na **Další: kontrolní** tlačítko.

29. Projděte si zásady připojené uživatele tak, že provedete následující kroky:

    ![Přidání uživatele](./media/active-directory-saas-amazon-web-service-tutorial/adduser3.png)
    
    a. Zkontrolujte uživatelské jméno, typ přístupu a zásady, které jsou namapované na uživatele.
    
    b. Klikněte na **vytvořit uživateli** tlačítko v pravém dolním rohu vytvořit uživateli.

30. Stáhněte si přihlašovací údaje uživatele uživatele tak, že provedete následující kroky:

    ![Přidání uživatele](./media/active-directory-saas-amazon-web-service-tutorial/adduser4.png)
    
    a. Zkopírujte uživatele **přístup klíče ID** a **tajný přístupový klíč**.
    
    b. Zadejte tyto přihlašovací údaje do Azure AD zřizování uživatelů části načíst role z konzoly AWS.
    
    c. Klikněte na **Zavřít** tlačítko dole.

31. Přejděte na **zřizování uživatelů** části Amazon Web Services aplikace na portálu správy Azure AD.

    ![Přidání uživatele](./media/active-directory-saas-amazon-web-service-tutorial/provisioning.png)

32. Zadejte **přístupový klíč** a **tajný klíč** v **tajný klíč klienta** a **tajný klíč tokenu** pole v uvedeném pořadí.

    ![Přidání uživatele](./media/active-directory-saas-amazon-web-service-tutorial/provisioning1.png)
    
    a. Zadejte přístupový klíč AWS uživatele v **clientsecret** pole.
    
    b. Zadejte tajný klíč AWS uživatele v **tajný klíč tokenu** pole.
    
    c. Klikněte na **otestovat připojení** tlačítko měli úspěšně otestovat toto připojení.

    d. Uložte nastavení kliknutím na **Uložit** tlačítka v horní části.
 
33. Nyní se ujistěte, že povolíte stav zřizování **na** v části nastavení tím, že na přepínači a potom kliknutím na **Uložit** tlačítka v horní části.

    ![Přidání uživatele](./media/active-directory-saas-amazon-web-service-tutorial/provisioning2.png)

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>Vytvořit testovací uživatele s Amazon Web Services (AWS)

Cílem této části je vytvoření uživatele volat Britta Simon Amazon Web Services (AWS). Amazon Web Services (AWS) nevyžaduje uživatele, který bude vytvořen v jejich systému pro jednotné přihlašování, takže není nutné provádět veškeré akce v tomto poli.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon chcete použít Azure jednotného přihlašování k udělení přístupu k Amazon Web Services (AWS).

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon k Amazon Web Services (AWS), proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Amazon Web Services (AWS)**.

    ![V seznamu aplikací na odkaz Amazon Web Services (AWS)](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Amazon Web Services (AWS) na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Amazon Web Services (AWS).
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795025.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[36]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png

