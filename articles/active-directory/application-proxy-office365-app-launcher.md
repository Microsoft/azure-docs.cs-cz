---
title: Nastavit vlastní domovskou stránku pro publikovaných aplikací pomocí proxy aplikace služby Azure AD | Microsoft Docs
description: Popisuje základní informace o Azure AD Application Proxy konektory
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 1627f3590bd92f2460368cffab48d43c5a82d3c4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Nastavit vlastní domovskou stránku pro publikovaných aplikací pomocí proxy aplikace služby Azure AD

Tento článek popisuje postup konfigurace aplikace, které uživatele nasměrují na vlastní domovskou stránku. Při publikování aplikace pomocí Proxy aplikací, můžete nastavit interní adresu URL ale někdy, není na stránku, kterou uživatelé měli vidět nejdřív. Nastavte vlastní domovskou stránku tak, aby vaši uživatelé přejít na stránku správné při přístupu aplikace. Vaši uživatelé uvidí vlastní domovskou stránku, který jste nastavili, jestli budou přistupovat k aplikaci z Azure Active Directory přístupového panelu nebo Spouštěč aplikace Office 365.

Když uživatelé spustí aplikaci, budou se přesměruje ve výchozím nastavení adresa URL kořenové domény pro publikované aplikace. Cílová stránka se obvykle nastavuje jako adresu URL pro domovskou stránku. Modul Azure AD PowerShell slouží k definování adresy URL vlastní domovskou stránku, pokud chcete aplikace uživatelům zobrazovat na konkrétní stránky v aplikaci. 

Tady je příkladem, proč společnosti byste měli nastavit vlastní domovské stránky:
- Uvnitř podnikové sítě, uživatelé přejít na *https://ExpenseApp/login/login.aspx* přihlásit a přístup k vaší aplikaci.
- Protože máte další prostředky, jako jsou bitové kopie, které Proxy aplikace potřebuje přístup k na nejvyšší úrovni struktura složek, můžete publikovat aplikaci s *https://ExpenseApp* jako interní adresa URL.
- Externí adresa URL výchozí *https://ExpenseApp-contoso.msappproxy.net*, který neberou uživatelům přihlašovací stránku.  
- Nastavit *https://ExpenseApp-contoso.msappproxy.net/login/login.aspx* jako adresu URL pro domovskou stránku. 

>[!NOTE]
>Pokud jste uživatelům přístup k publikovaným aplikacím, aplikace se zobrazují v [přístupový Panel Azure AD](active-directory-saas-access-panel-introduction.md) a [Spouštěč aplikace Office 365](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher).

## <a name="before-you-start"></a>Než začnete

Než budete nastavit adresu URL domovskou stránku, mějte na paměti následující požadavky:

* Zajistěte, aby cestu, kterou zadáte cestu subdomény kořenové domény adresy URL.

  Pokud je adresa URL kořenové domény, například https://apps.contoso.com/app1/, adresa URL domovskou stránku, který nakonfigurujete musí začínat https://apps.contoso.com/app1/.

* Pokud změníte publikované aplikace, může změna resetovat hodnotu adresy URL domovské stránky. Při aktualizaci aplikace v budoucnu, měli znovu zkontrolovat a v případě potřeby aktualizujte adresu URL domovské stránky.

## <a name="change-the-home-page-in-the-azure-portal"></a>Změnit domovské stránce portálu Azure

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejděte na **Azure Active Directory** > **registrace aplikace** a vyberte aplikaci ze seznamu. 
3. Vyberte **vlastnosti** z nastavení.
4. Aktualizace **adresa URL domovské stránky** pole s novou cestu. 

   ![Zadejte novou adresu URL domovské stránky](./media/application-proxy-office365-app-launcher/homepage.png)

5. Vyberte **uložit**

## <a name="change-the-home-page-with-powershell"></a>Změnit domovskou stránku pomocí prostředí PowerShell

### <a name="install-the-azure-ad-powershell-module"></a>Instalace modulu Azure AD PowerShell

Před definujete adresu URL vlastní domovskou stránku pomocí prostředí PowerShell, nainstalujte modul Azure AD PowerShell. Si můžete stáhnout balíček z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.0.131), který používá koncový bod rozhraní Graph API. 

K instalaci balíčku, postupujte takto:

1. Otevřete standardní okno prostředí PowerShell a spusťte následující příkaz:

    ```
     Install-Module -Name AzureAD
    ```
    Pokud příkaz spouštíte jako bez oprávnění správce, použijte `-scope currentuser` možnost.
2. Během instalace, vyberte **Y** instalace dva balíčky z Nuget.org. Oba balíčky jsou povinné. 

### <a name="find-the-objectid-of-the-app"></a>Najít ObjectID aplikace

Získejte ObjectID aplikace a pak vyhledejte aplikaci podle jeho domovské stránky.

1. Ve stejném okně prostředí PowerShell naimportujte modul Azure AD.

    ```
    Import-Module AzureAD
    ```

2. Přihlaste se k modulu Azure AD jako správce klienta.

    ```
    Connect-AzureAD
    ```
3. Najděte aplikaci, podle jeho adresa URL domovské stránky. Adresu URL můžete najít na portálu přejděte na **Azure Active Directory** > **podnikové aplikace, které** > **všechny aplikace**. Tento příklad používá *sharepoint iddemo*.

    ```
    Get-AzureADApplication | where { $_.Homepage -like “sharepoint-iddemo” } | fl DisplayName, Homepage, ObjectID
    ```
4. Měli byste obdržet výsledek, který je podobný znázorněno zde. Zkopírujte identifikátor GUID ObjectID pro použití v další části.

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

### <a name="update-the-home-page-url"></a>Aktualizovat adresu URL domovské stránky

Vytvořit adresu URL domovské stránky a aktualizovat vaše aplikace s danou hodnotou. Pokračujte v použití stejné okno PowerShell ke spuštění těchto příkazů. Nebo, pokud používáte nové okno prostředí PowerShell, přihlaste se znovu s použitím modulu Azure AD `Connect-AzureAD`. 

1. Zkontrolujte, jestli máte správné aplikace a nahradit *8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4* s ObjectID, kterou jste zkopírovali v předchozím oddílu.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

 Teď, když jste potvrdí aplikace, budete připraveni k aktualizaci domovské stránce následujícím způsobem.

2. Vytvořte objekt prázdné aplikace pro uložení změny, které chcete nastavit. Tato proměnná obsahuje hodnoty, které chcete aktualizovat. Nic se vytvoří v tomto kroku.

    ```
    $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
    ```

3. Na hodnotu, která chcete nastavte adresu URL domovské stránky. Hodnota musí být cesta subdomény publikované aplikace. Například, pokud změníte adresu URL domovské stránky z *https://sharepoint-iddemo.msappproxy.net/* k *https://sharepoint-iddemo.msappproxy.net/hybrid/*, uživatelům aplikace přejít přímo na domovské stránce vlastní.

    ```
    $homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
    ```
4. Proveďte aktualizace pomocí identifikátoru GUID (ObjectID), kterou jste zkopírovali v "krok 1: najít ObjectID aplikace."

    ```
    Set-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 -Homepage $homepage
    ```
5. Potvrďte, že tato změna byla úspěšná, restartujte aplikaci.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

>[!NOTE]
>Veškeré změny, které provedete aplikace může resetovat adresu URL domovské stránky. Pokud adresa URL domovské stránky obnoví, opakujte kroky v této části nastavte ji zpět.

## <a name="next-steps"></a>Další postup

- [Povolte vzdálený přístup na SharePoint s proxy aplikace služby Azure AD](application-proxy-enable-remote-access-sharepoint.md)
- [Povolení Proxy aplikace na portálu Azure](active-directory-application-proxy-enable.md)
