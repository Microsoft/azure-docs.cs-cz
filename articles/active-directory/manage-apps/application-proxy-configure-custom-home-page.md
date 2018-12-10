---
title: Nastavit vlastní domovskou stránku pro aplikace publikované pomocí Proxy aplikací Azure AD | Dokumentace Microsoftu
description: Popisuje základní informace o konektory Proxy aplikací Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 9ee7f0b975dd2990281833726b4013c9726a2935
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53134313"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Nastavit vlastní domovskou stránku pro aplikace publikované pomocí Proxy aplikací Azure AD

Tento článek popisuje, jak nakonfigurovat aplikace k přesměrování uživatelů na vlastní domovskou stránku. Při publikování aplikace pomocí Proxy aplikací je nastavit interní adresa URL ale někdy, který není na stránku, kterou vaši uživatelé měli vidět první. Nastavte vlastní domovskou stránku tak, aby vaši uživatelé přejít na správnou stránku při přístupu k aplikacím. Uživatelům se zobrazí vlastní domovskou stránku, kterou jste nastavili, zda jejich přístup k aplikaci z Azure Active Directory přístupového panelu nebo Spouštěče aplikací Office 365.

Při spuštění aplikace, že jste přesměruje ve výchozím nastavení domény kořenovou adresu URL pro publikovanou aplikaci. Cílová stránka je obvykle nastavena jako adresa URL domovské stránky. Pomocí modulu Azure AD Powershellu k definování adresy URL vlastní domovskou stránku, pokud chcete uživatelům aplikace přejít na určitou stránku v rámci aplikace. 

Tady je jeden příklad, proč společnost nastaví vlastní domovskou stránku:
- Uvnitř firemní sítě, uživatelé přejít na *https://ExpenseApp/login/login.aspx* přihlásit a získat přístup k vaší aplikaci.
- Protože máte jiné prostředky, jako jsou obrázky, které Proxy aplikace potřebuje přístup k na nejvyšší úrovni struktury složek, můžete publikovat aplikaci s *https://ExpenseApp* jako interní adresa URL.
- Výchozí externí adresa URL je *https://ExpenseApp-contoso.msappproxy.net*, který nepřijímá uživatelům přihlašovací stránku.  
- Nastavte *https://ExpenseApp-contoso.msappproxy.net/login/login.aspx* jako adresa URL domovské stránky. 

>[!NOTE]
>Když poskytnete uživatelům přístup k publikovaným aplikacím, aplikace se zobrazují v [přístupový Panel Azure AD](../user-help/active-directory-saas-access-panel-introduction.md) a [Spouštěči aplikací Office 365](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher).

## <a name="before-you-start"></a>Než začnete

Než nastavíte adresa URL domovské stránky, mějte na paměti následující požadavky:

* Zajistěte, aby byl cestu, kterou zadáte cestu subdoménu kořenové domény adresy URL.

  Pokud je adresa URL kořenové domény, například https://apps.contoso.com/app1/, adresa URL domovské stránky, který nakonfigurujete musí začínat znakem https://apps.contoso.com/app1/.

* Pokud provedete změny k publikované aplikaci, může změna resetovat hodnotu adresy URL domovské stránky. Při aktualizaci aplikace v budoucnu, by měl znovu zkontrolujte a v případě potřeby aktualizujte adresu URL domovské stránky.

## <a name="change-the-home-page-in-the-azure-portal"></a>Změnit domovskou stránku na webu Azure Portal

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejděte do **Azure Active Directory** > **registrace aplikací** a vyberte aplikaci ze seznamu. 
3. Vyberte **vlastnosti** z nastavení.
4. Aktualizace **adresa URL domovské stránky** pole s novou cestu. 

   ![Zadejte novou adresu URL domovské stránky](./media/application-proxy-configure-custom-home-page/homepage.png)

5. Vyberte **uložit**

## <a name="change-the-home-page-with-powershell"></a>Změnit domovskou stránku pomocí Powershellu

### <a name="install-the-azure-ad-powershell-module"></a>Instalace modulu Azure AD Powershellu

Před definujete adresu URL vlastní domovskou stránku pomocí prostředí PowerShell, nainstalujte modul Azure AD PowerShell. Si můžete stáhnout balíček z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.0.131), který používá koncový bod rozhraní Graph API. 

Chcete-li nainstalovat balíček, postupujte takto:

1. Otevřete standardní okno Powershellu a spusťte následující příkaz:

    ```
     Install-Module -Name AzureAD
    ```
    Pokud příkaz spouštíte jako bez oprávnění správce, použijte `-scope currentuser` možnost.
2. Během instalace, vybrat **Y** instalace dva balíčky z Nuget.org. Oba balíčky jsou povinné. 

### <a name="find-the-objectid-of-the-app"></a>Najít ID objektu aplikace

Získání ID objektu aplikace a pak vyhledejte aplikaci podle jeho domovské stránky.

1. Ve stejném okně prostředí PowerShell naimportujte modul Azure AD.

    ```
    Import-Module AzureAD
    ```

2. Přihlaste se k modulu Azure AD jako správce tenanta.

    ```
    Connect-AzureAD
    ```
3. Najděte aplikaci, na základě jeho adresy URL domovské stránky. Adresa URL na portálu můžete najít tak, že přejdete do **Azure Active Directory** > **podnikové aplikace** > **všechny aplikace**. Tento příklad používá *sharepoint iddemo*.

    ```
    Get-AzureADApplication | where { $_.Homepage -like "sharepoint-iddemo" } | fl DisplayName, Homepage, ObjectID
    ```
4. Měli byste získat výsledek, který je podobný znázorněno zde. Zkopírujte identifikátor GUID ObjectID pro použití v další části.

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

### <a name="update-the-home-page-url"></a>Aktualizovat adresu URL domovské stránky

Vytvořit adresu URL domovské stránky a aktualizovat vaše aplikace s touto hodnotou. Dál používat stejné okno prostředí PowerShell pro spuštění těchto příkazů. Nebo, pokud používáte nové okno prostředí PowerShell, přihlaste se k modulu Azure AD s použitím znovu `Connect-AzureAD`. 

1. Zkontrolujte, jestli máte správné aplikaci a nahraďte *8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4* s ID objektu, který jste zkopírovali v předchozí části.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

 Teď, když je ověření, že aplikace, budete připraveni k následujícím způsobem aktualizujte domovskou stránku.

2. Vytvořte objekt prázdná aplikace pro uložení změn, které chcete provést. Tato proměnná obsahuje hodnoty, které chcete aktualizovat. Nic je vytvořen v tomto kroku.

    ```
    $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
    ```

3. Adresa URL domovské stránky nastavena na hodnotu, která chcete. Hodnota musí být cesta subdoménu publikované aplikace. Například, pokud se změní adresa URL domovské stránky z `https://sharepoint-iddemo.msappproxy.net/` k `https://sharepoint-iddemo.msappproxy.net/hybrid/`, uživatelům aplikace přejít přímo na domovské stránce vlastní.

    ```
    $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
    ```
4. Provádění aktualizací pomocí identifikátoru GUID (ObjectID), který jste zkopírovali v "krok 1: vyhledání ID objektu aplikace."

    ```
    Set-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 -Homepage $homepage
    ```
5. Pokud chcete potvrdit, že tato změna byla úspěšná, restartujte aplikaci.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

>[!NOTE]
>Všechny změny provedené v aplikaci může resetovat adresa URL domovské stránky. Pokud vaše adresa URL domovské stránky resetuje, opakujte kroky v této části nastavte ji zpět.

## <a name="next-steps"></a>Další postup

- [Povolit vzdálený přístup k Sharepointu pomocí Azure AD Application Proxy](application-proxy-integrate-with-sharepoint-server.md)
- [Povolení Proxy aplikace na webu Azure Portal](application-proxy-add-on-premises-application.md)
