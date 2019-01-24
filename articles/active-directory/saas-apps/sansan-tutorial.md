---
title: 'Kurz: Integrace Azure Active Directory s Sansan | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Sansan.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: f653a0f2-c44a-4670-b936-68c136b578ea
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 6bd84e42bf46885a9dced363724538cbd4d2066a
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54815000"
---
# <a name="tutorial-azure-active-directory-integration-with-sansan"></a>Kurz: Integrace Azure Active Directory s Sansan

V tomto kurzu se dozvíte, jak integrovat Sansan s Azure Active Directory (Azure AD).

Sansan integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Sansan
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Sansan (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Sansan, potřebujete následující položky:

- Předplatné Azure AD
- Sansan jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Sansan z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-sansan-from-the-gallery"></a>Přidání Sansan z Galerie
Konfigurace integrace Sansan do služby Azure AD, budete muset přidat Sansan z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Sansan z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Sansan**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/sansan-tutorial/tutorial_sansan_search.png)

5. Na panelu výsledků vyberte **Sansan**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/sansan-tutorial/tutorial_sansan_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Sansan podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Sansan je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Sansan potřeba navázat.

V Sansan, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Sansan, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Sansan](#creating-a-sansan-test-user)**  – Pokud chcete mít protějšek Britta Simon Sansan, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Sansan.

**Ke konfiguraci Azure AD jednotné přihlašování s Sansan, proveďte následující kroky:**

1. Na webu Azure Portal na **Sansan** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/sansan-tutorial/tutorial_sansan_samlbase.png)

3. Na **Sansan domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/sansan-tutorial/tutorial_sansan_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujících vzorů: 
    
    | Prostředí | zprostředkovatele identity |
    |:--- |:--- |
    | PC web |`https://ap.sansan.com/v/saml2/<company name>/acs` |
    | Nativní mobilní aplikace |`https://internal.api.sansan.com/saml2/<company name>/acs` |
    | Nastavení mobilního prohlížeče |`https://ap.sansan.com/s/saml2/<company name>/acs` |  

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresa URL. Kontakt [tým podpory Sansan klienta](https://www.sansan.com/form/contact) k získání těchto hodnot. 
     
4. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/sansan-tutorial/tutorial_sansan_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/sansan-tutorial/tutorial_general_400.png)

6. Aplikace Sansan očekává více **identifikátory** a **adresy URL odpovědí** pro podporu více prostředí (PC web, nativní mobilní aplikace, nastavení mobilního prohlížeče), které lze konfigurovat pomocí Powershellu skript. Podrobný postup je popsán níže.

7. Chcete-li nakonfigurovat více **identifikátory** a **adresy URL odpovědí** Sansan aplikací pomocí skriptu prostředí PowerShell, proveďte následující kroky:

    ![Konfigurovat Single Sign-On obj](./media/sansan-tutorial/tutorial_sansan_objid.png)  

    a. Přejděte **vlastnosti** stránce **Sansan** aplikace a zkopírujte **ID objektu** pomocí **kopírování** tlačítko a vložte ho do poznámkového bloku.

    b. **ID objektu**, který jste zkopírovali z portálu Azure portal se použije jako **ServicePrincipalObjectId** Powershellového skriptu se používá v pozdější části kurzu. 

    c. Nyní otevřete příkazový řádek se zvýšenými oprávněními prostředí Windows PowerShell.
    
    >[!NOTE] 
    > Je potřeba nainstalovat modul Azure AD (použijte příkaz `Install-Module -Name AzureAD`). Pokud se zobrazí výzva k instalaci modulu NuGet nebo nový modul Powershellu pro Azure Active Directory V2, zadejte Y a stiskněte klávesu ENTER.

    d. Spustit `Connect-AzureAD` a přihlaste se pomocí uživatelského účtu globálního správce.

    e. Aktualizace více adres URL do aplikace pomocí následujícího skriptu:

    ```poweshell
     Param(
    [Parameter(Mandatory=$true)][guid]$ServicePrincipalObjectId,
    [Parameter(Mandatory=$false)][string[]]$ReplyUrls,
    [Parameter(Mandatory=$false)][string[]]$IdentifierUrls
    )

    $servicePrincipal = Get-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId

    if($ReplyUrls.Length)
    {
    echo "Updating Reply urls"
    Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls $ReplyUrls
    echo "updated"
    }
    if($IdentifierUrls.Length)
    {
    echo "Updating Identifier urls"
    $applications = Get-AzureADApplication -SearchString $servicePrincipal.AppDisplayName 
    echo "Found Applications =" $applications.Length
    $i = 0;
    do
    {  
    $application = $applications[$i];
    if($application.AppId -eq $servicePrincipal.AppId){
    Set-AzureADApplication -ObjectId $application.ObjectId -IdentifierUris $IdentifierUrls
    $servicePrincipal = Get-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId
    echo "Updated"
    return;
    }
    $i++;
    }while($i -lt $applications.Length);
    echo "Not able to find the matched application with this service principal"
    }
    ```

8. Po úspěšném dokončení skriptu prostředí PowerShell výsledek skriptu bude tímto způsobem, jak je znázorněno níže a aktualizovat hodnoty adresy URL, ale nebudete získáte projeví na webu Azure portal. 

    ![Konfigurovat Single Sign-On skriptu](./media/sansan-tutorial/tutorial_sansan_powershell.png)


9. Na **Sansan konfigurace** klikněte na tlačítko **nakonfigurovat Sansan** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/sansan-tutorial/tutorial_sansan_configure.png) 

10. Ke konfiguraci jednotného přihlašování na **Sansan** straně, je nutné odeslat na stažený **certifikát**, **odhlašování URL**, **SAML Entity ID**, a **SAML jednotné přihlašování – adresa URL služby** k [tým podpory Sansan](https://www.sansan.com/form/contact). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

>[!NOTE]
>Nastavení prohlížeče počítače fungovat i pro mobilní aplikace a mobilní prohlížeče spolu s PC web. 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/sansan-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/sansan-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/sansan-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/sansan-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-sansan-test-user"></a>Vytvoření zkušebního uživatele Sansan

V této části vytvoříte uživatele v Sansan jako Britta Simon. Sansan aplikace potřebuje uživatel zřídit aplikace před provedením jednotného přihlašování. 

>[!NOTE]
>Pokud je potřeba ručně vytvořit uživatele nebo dávkové uživatelů, budete muset kontaktovat [tým podpory Sansan](https://www.sansan.com/form/contact). 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Sansan použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Sansan Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Sansan**.

    ![Konfigurace jednotného přihlašování](./media/sansan-tutorial/tutorial_sansan_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Sansan na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Sansan.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sansan-tutorial/tutorial_general_01.png
[2]: ./media/sansan-tutorial/tutorial_general_02.png
[3]: ./media/sansan-tutorial/tutorial_general_03.png
[4]: ./media/sansan-tutorial/tutorial_general_04.png

[100]: ./media/sansan-tutorial/tutorial_general_100.png

[200]: ./media/sansan-tutorial/tutorial_general_200.png
[201]: ./media/sansan-tutorial/tutorial_general_201.png
[202]: ./media/sansan-tutorial/tutorial_general_202.png
[203]: ./media/sansan-tutorial/tutorial_general_203.png

