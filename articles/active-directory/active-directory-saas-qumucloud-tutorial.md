---
title: 'Kurz: Azure Active Directory integrace s Qumu cloudu | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Qumu cloudu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d8c4a97b-4de6-49d4-b64e-42222c2ec6c9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2018
ms.author: jeedes
ms.openlocfilehash: 1778917a383aef2fc0f791cda3235cff74b77f18
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350691"
---
# <a name="tutorial-azure-active-directory-integration-with-qumu-cloud"></a>Kurz: Azure Active Directory integrace s Qumu cloudu

V tomto kurzu zjistěte, jak integrovat Qumu cloudu s Azure Active Directory (Azure AD).

Integrace Qumu cloudu s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup do cloudu Qumu.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Qumu cloudu (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s cloudem Qumu, potřebujete následující položky:

- Předplatné služby Azure AD
- Cloudu Qumu jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Qumu cloudu z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-qumu-cloud-from-the-gallery"></a>Přidání Qumu cloudu z Galerie
Při konfiguraci integrace Qumu cloudu do služby Azure AD, potřebujete přidat Qumu cloudu z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Qumu cloudu z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Qumu cloudu**, vyberte **Qumu cloudu** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Qumu cloudu v seznamu výsledků](./media/active-directory-saas-qumucloud-tutorial/tutorial_qumucloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat, testovací Azure AD jednotné přihlašování s cloudem Qumu podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v cloudu Qumu je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v cloudu Qumu musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Qumu cloudu, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Qumu Cloud](#create-a-qumu-cloud-test-user)**  – Pokud chcete mít protějšek Britta Simon Qumu cloudu, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Qumu cloudu.

**Ke konfiguraci Azure AD jednotné přihlašování s Qumu cloudu, proveďte následující kroky:**

1. Na portálu Azure na **Qumu cloudu** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-qumucloud-tutorial/tutorial_qumucloud_samlbase.png)

3. Na **Qumu cloudové domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Qumu cloudové domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-qumucloud-tutorial/tutorial_qumucloud_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.qumucloud.com/saml/SSO`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.qumucloud.com/saml/SSO`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Qumu cloudové domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-qumucloud-tutorial/tutorial_qumucloud_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.qumucloud.com`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [tým podpory klient Cloud Qumu](mailto:support@qumu.com) k získání těchto hodnot.

4. Qumu cloudových aplikací očekává SAML kontrolní výrazy ve specifickém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z "**uživatelské atributy**" části na stránce integrace aplikace. Následující snímek obrazovky ukazuje příklad pro tento.
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-qumucloud-tutorial/attribute.png)
    
5. Klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** zaškrtnout políčko **uživatelské atributy** rozbalte atributy. Proveďte následující kroky na každém z zobrazených atributů-

    | Název atributu | Hodnota atributu |
    | ---------------| --------------- |    
    | název urn: oid:2.5.4.42 | user.givenname |
    | název urn: oid:2.5.4.4 | user.surname |
    | urn:oid:0.9.2342.19200300.100.1.3 | User.Mail |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |

    a. Klikněte na atribut, který se otevře **Upravit atribut** okno.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-qumucloud-tutorial/tutorial_attribute_04.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-qumucloud-tutorial/tutorial_attribute_05.png)

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d. Zachovat **Namespace** textové pole prázdné.
    
    e. Klikněte na tlačítko **OK**.

5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-qumucloud-tutorial/tutorial_qumucloud_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-qumucloud-tutorial/tutorial_general_400.png)
    
7. Konfigurace jednotného přihlašování na **Qumu cloudu** straně, budete muset odeslat stažené **soubor XML s metadaty** k [tým podpory Qumu cloudu](mailto:support@qumu.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-qumucloud-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-qumucloud-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-qumucloud-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-qumucloud-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-qumu-cloud-test-user"></a>Vytvoření zkušebního uživatele Qumu cloudu

Cílem této části je vytvoření uživatele názvem Britta Simon v Qumu cloudu. Qumu Cloud podporuje za běhu zřizování, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Qumu cloudu, pokud ještě neexistuje.
>[!Note]
>Pokud je potřeba ručně vytvořit uživateli, obraťte se na [tým podpory klient Cloud Qumu](mailto:support@qumu.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat tak, že udělíte přístup do cloudu Qumu Azure jednotné přihlašování.

![Přiřadit role uživatele][200] 

**Přiřadit Britta Simon Qumu cloudu, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Qumu cloudu**.

    ![V seznamu aplikací na odkaz Qumu cloudu](./media/active-directory-saas-qumucloud-tutorial/tutorial_qumucloud_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Qumu cloudu na přístupovém panelu, jste měli získat automaticky přihlášení k Qumu cloudové aplikace.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_203.png

