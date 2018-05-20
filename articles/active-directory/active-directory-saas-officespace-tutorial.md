---
title: 'Kurz: Azure Active Directory integrace s OfficeSpace softwarem | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi OfficeSpace softwarem a Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 5f8698e50f9740d46a5cec708e3d5ae6bf1043ce
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Kurz: Azure Active Directory integrace s OfficeSpace softwaru

V tomto kurzu zjistěte, jak integrovat OfficeSpace softwaru s Azure Active Directory (Azure AD).

Integrace OfficeSpace softwaru s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k OfficeSpace softwaru.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k softwaru OfficeSpace (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s OfficeSpace softwaru, potřebujete následující položky:

- Předplatné služby Azure AD
- OfficeSpace softwaru jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání softwaru OfficeSpace z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-officespace-software-from-the-gallery"></a>Přidání softwaru OfficeSpace z Galerie
Při konfiguraci integrace OfficeSpace softwaru do služby Azure AD, musíte přidat OfficeSpace Software z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat OfficeSpace softwaru z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **OfficeSpace softwaru**, vyberte **OfficeSpace softwaru** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![OfficeSpace softwaru v seznamu výsledků](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat, testovací Azure AD jednotné přihlašování s OfficeSpace softwarem podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v OfficeSpace softwaru je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v OfficeSpace softwaru je nutné stanovit.

V softwaru OfficeSpace přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s OfficeSpace softwaru, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele softwaru OfficeSpace](#create-a-officespace-software-test-user)**  – Pokud chcete mít protějšek Britta Simon v OfficeSpace Software, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci OfficeSpace softwaru.

**Ke konfiguraci Azure AD jednotné přihlašování s OfficeSpace softwarem, proveďte následující kroky:**

1. Na portálu Azure na **OfficeSpace softwaru** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_samlbase.png)

3. Na **OfficeSpace softwaru domény a adresy URL** část, proveďte následující kroky:

    ![OfficeSpace softwaru domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `<company name>.officespacesoftware.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory klientský Software OfficeSpace](mailto:support@officespacesoftware.com) k získání těchto hodnot. 

4. OfficeSpace softwarová aplikace očekává SAML kontrolní výrazy ve specifickém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z "**uživatelské atributy**" části na stránce integrace aplikace. Následující snímek obrazovky ukazuje příklad pro tento.
    
    ![Konfigurace atributů](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_attribute.png)

5. V **uživatelské atributy** části na **jednotného přihlašování** dialogovém okně, vyberte **user.mail** jako **uživatelský identifikátor** a pro každý řádek v tabulce níže, proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | --- | --- |    
    | e-mail | User.Mail |
    | jméno | user.displayname |
    | křestní_jméno | user.givenname |
    | Příjmení | user.surname |

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurace přidat ](./media/active-directory-saas-officespace-tutorial/tutorial_attribute_04.png)

    ![Konfigurace atributů](./media/active-directory-saas-officespace-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.
    
    d. Klikněte na tlačítko **Ok**
 
6. Na **SAML podpisový certifikát** část, zkopírujte **kryptografický OTISK** hodnota certifikátu.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_certificate.png) 

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-officespace-tutorial/tutorial_general_400.png)

8. Na **OfficeSpace softwarové konfigurace** klikněte na tlačítko **konfigurace softwaru OfficeSpace** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL a SAML jeden přihlašování služby URL** z **Stručná referenční příručka části.**

    ![Konfigurace OfficeSpace softwaru](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_configure.png) 

9. V okně prohlížeče jiný web Přihlaste se ke klientovi OfficeSpace softwaru jako správce.

10. Přejděte na **nastavení** a klikněte na tlačítko **konektory**.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_002.png)

11. Klikněte na tlačítko **ověřování SAML**.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_003.png)

12. V **ověřování SAML** část, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_004.png)

    a. V **adresy url odhlašovací zprostředkovatele** textovému poli, vložte hodnotu **Sign-Out URL** který jste zkopírovali z portálu Azure.

    b. V **klienta idp cílová adresa url** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.

    c. Vložení **kryptografický otisk** hodnotu, která jste zkopírovali z portálu Azure do **otisk certifikátu klienta IDP** textové pole. 

    d. Klikněte na tlačítko **uložit nastavení**.


> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-officespace-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-officespace-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-officespace-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-officespace-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-officespace-software-test-user"></a>Vytvoření zkušebního uživatele OfficeSpace softwaru

Cílem této části je vytvoření uživatele volal Britta Simon v OfficeSpace softwaru. OfficeSpace Software podporuje za běhu zřizování, který je ve výchozím nastavení povolené.

Neexistuje žádná položka akce pro vás v této části. Vytvoří se nový uživatel během pokusu o přístup k softwaru OfficeSpace, pokud ještě neexistuje.

> [!NOTE]
> Pokud potřebujete vytvořit uživatele s ručně, musíte kontaktovat [tým podpory OfficeSpace softwaru](mailto:support@officespacesoftware.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu OfficeSpace softwaru.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon OfficeSpace softwaru, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **OfficeSpace softwaru**.

    ![V seznamu aplikací na odkaz OfficeSpace softwaru](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici OfficeSpace softwaru na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci OfficeSpace softwaru.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_203.png

