---
title: 'Kurz: Azure Active Directory integrace s Sansan | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Sansan.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f653a0f2-c44a-4670-b936-68c136b578ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: b355a27fd63fa4c7bd4ca01bb677bf10f2ef80b7
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35901657"
---
# <a name="tutorial-azure-active-directory-integration-with-sansan"></a>Kurz: Azure Active Directory integrace s Sansan

V tomto kurzu zjistěte, jak integrovat Sansan s Azure Active Directory (Azure AD).

Integrace Sansan s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Sansan
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Sansan (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Sansan, potřebujete následující položky:

- Předplatné služby Azure AD
- Sansan jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Sansan z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-sansan-from-the-gallery"></a>Přidání Sansan z Galerie
Při konfiguraci integrace Sansan do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Sansan z galerie.

**Pokud chcete přidat Sansan z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Sansan**.

    ![Vytváření testovacího uživatele Azure AD](./media/sansan-tutorial/tutorial_sansan_search.png)

5. Na panelu výsledků vyberte **Sansan**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/sansan-tutorial/tutorial_sansan_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Sansan podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Sansan je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Sansan musí navázat.

V Sansan, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Sansan, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Sansan](#creating-a-sansan-test-user)**  – Pokud chcete mít protějšek Britta Simon v Sansan propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Sansan.

**Ke konfiguraci Azure AD jednotné přihlašování s Sansan, proveďte následující kroky:**

1. Na portálu Azure na **Sansan** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/sansan-tutorial/tutorial_sansan_samlbase.png)

3. Na **Sansan domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/sansan-tutorial/tutorial_sansan_url.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: 
    
    | Prostředí | zprostředkovatele identity |
    |:--- |:--- |
    | Webové PC |`https://ap.sansan.com/v/saml2/<company name>/acs` |
    | Nativní mobilní aplikace |`https://internal.api.sansan.com/saml2/<company name>/acs` |
    | Nastavení mobilní prohlížeče |`https://ap.sansan.com/s/saml2/<company name>/acs` |  

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte s skutečná adresa URL přihlašování. Obraťte se na [tým podpory Sansan klienta](https://www.sansan.com/form/contact) k získání těchto hodnot. 
     
4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/sansan-tutorial/tutorial_sansan_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/sansan-tutorial/tutorial_general_400.png)

6. Aplikace Sansan očekává více **identifikátory** a **adresy URL odpovědí** pro podporu prostředí s více (počítač webové, nativní mobilní aplikace, nastavení prohlížeč pro mobilní zařízení), které lze konfigurovat pomocí prostředí PowerShell skript. Podrobné kroky jsou vysvětleny níže.

7. Konfigurace více **identifikátory** a **adresy URL odpovědí** Sansan aplikace pomocí skriptu prostředí PowerShell, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování obj.](./media/sansan-tutorial/tutorial_sansan_objid.png)  

    a. Přejděte na **vlastnosti** stránka **Sansan** aplikace a zkopírujte **ID objektu** pomocí **kopie** tlačítko a vložte do poznámkového bloku.

    b. **ID objektu**, který jste zkopírovali z portálu Azure se používá jako **ServicePrincipalObjectId** ve skriptu PowerShell později v tomto kurzu použili. 

    c. Nyní otevřete příkazový řádek se zvýšenými oprávněními prostředí Windows PowerShell.
    
    >[!NOTE] 
    > Potřebujete nainstalovat modul AzureAD (použijte příkaz `Install-Module -Name AzureAD`). Pokud se zobrazí výzva k instalaci modulu NuGet nebo nového modulu Azure Active Directory V2 PowerShell, zadejte Y a stiskněte klávesu ENTER.

    d. Spustit `Connect-AzureAD` a přihlaste se pomocí uživatelského účtu globálního správce.

    e. Použijte následující skript k aktualizaci více adres URL do aplikace:

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

8. Po úspěšném dokončení skriptu prostředí PowerShell výsledkem skriptu bude jako to, jak je uvedeno níže a aktualizovat adresu URL hodnoty ale nebude získat projeví na portálu Azure. 

    ![Konfigurovat jednotné přihlašování skriptu](./media/sansan-tutorial/tutorial_sansan_powershell.png)


9. Na **Sansan konfigurace** klikněte na tlačítko **konfigurace Sansan** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/sansan-tutorial/tutorial_sansan_configure.png) 

10. Konfigurace jednotného přihlašování na **Sansan** straně, budete muset odeslat stažené **certifikát**, **Sign-Out URL**, **SAML Entity ID**, a **SAML jeden přihlašování adresa URL služby** k [tým podpory Sansan](https://www.sansan.com/form/contact). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

>[!NOTE]
>Nastavení prohlížeče počítače pracovních také pro mobilní aplikace a společně s počítači webový prohlížeč pro mobilní zařízení. 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/sansan-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/sansan-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/sansan-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/sansan-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-sansan-test-user"></a>Vytvoření zkušebního uživatele Sansan

V této části vytvoříte volal Britta Simon v Sansan uživatele. Sansan aplikace, musí uživatel zřídit v aplikaci před provedením jednotné přihlašování. 

>[!NOTE]
>Pokud potřebujete ručně vytvořte uživatele nebo dávky uživatelů, budete muset kontaktovat [tým podpory Sansan](https://www.sansan.com/form/contact). 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Sansan.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Sansan, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Sansan**.

    ![Konfigurovat jednotné přihlašování](./media/sansan-tutorial/tutorial_sansan_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Sansan na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Sansan.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

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

