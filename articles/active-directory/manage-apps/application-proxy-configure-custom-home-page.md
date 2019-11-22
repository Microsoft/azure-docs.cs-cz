---
title: Vlastní Domovská stránka publikovaných aplikací – Azure Proxy aplikací služby AD
description: Popisuje základní informace o konektory Proxy aplikací Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1621b273f617955a374ed46d9c215ba99e5b2913
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275602"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Nastavit vlastní domovskou stránku pro aplikace publikované pomocí Proxy aplikací Azure AD

Tento článek popisuje, jak nakonfigurovat aplikaci, která uživatele nasměruje na vlastní domovskou stránku. Při publikování aplikace pomocí proxy aplikace nastavíte interní adresu URL, ale někdy to není stránka, kterou by měl uživatel vidět jako první. Nastavte vlastní domovskou stránku tak, aby uživatel při přístupu k aplikaci získá pravou stránku. Uživatel uvidí vlastní domovskou stránku, kterou jste nastavili, bez ohledu na to, jestli k aplikaci přistupuje z přístupového panelu Azure Active Directory nebo ze Spouštěče aplikací pro Office 365.

Když uživatel aplikaci spustí, bude se ve výchozím nastavení směrovat na adresu URL kořenové domény publikované aplikace. Cílová stránka je obvykle nastavena jako adresa URL domovské stránky. Pomocí modulu Azure AD PowerShell definujte vlastní adresu URL domovské stránky, pokud chcete, aby uživatel aplikace na konkrétní stránce v rámci aplikace nakládal.

Tady je jeden scénář, který vysvětluje, proč vaše společnost nastavila vlastní domovskou stránku:

- V rámci podnikové sítě uživatel přejde na `https://ExpenseApp/login/login.aspx`, aby se přihlásil a měl přístup k vaší aplikaci.
- Vzhledem k tomu, že máte jiné prostředky (jako jsou obrázky), které proxy aplikace potřebuje k přístupu na nejvyšší úrovni struktury složek, publikujte aplikaci pomocí `https://ExpenseApp` jako interní adresu URL.
- Výchozí externí adresa URL je `https://ExpenseApp-contoso.msappproxy.net`, která neumožňuje externímu uživateli na přihlašovací stránce.
- Místo toho chcete nastavit `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` jako adresu URL domovské stránky, takže externí uživatel uvidí přihlašovací stránku jako první.

> [!NOTE]
> Když poskytnete uživatelům přístup k publikovaným aplikacím, aplikace se zobrazují v [přístupový Panel Azure AD](../user-help/my-apps-portal-end-user-access.md) a [Spouštěči aplikací Office 365](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Než začnete

Než nastavíte adresa URL domovské stránky, mějte na paměti následující požadavky:

- Cesta, kterou zadáte, musí být cestou subdomény adresy URL kořenové domény.

  Pokud je například adresa URL kořenové domény `https://apps.contoso.com/app1/`, adresa URL domovské stránky, kterou nakonfigurujete, musí začínat na `https://apps.contoso.com/app1/`.

- Pokud provedete změny k publikované aplikaci, může změna resetovat hodnotu adresy URL domovské stránky. Při aktualizaci aplikace v budoucnu, by měl znovu zkontrolujte a v případě potřeby aktualizujte adresu URL domovské stránky.

Adresu URL domovské stránky můžete nastavit buď pomocí Azure Portal, nebo pomocí prostředí PowerShell.

## <a name="change-the-home-page-in-the-azure-portal"></a>Změnit domovskou stránku na webu Azure Portal

Pokud chcete změnit adresu URL domovské stránky vaší aplikace prostřednictvím portálu Azure AD, postupujte takto:

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com/) jako správce.
1. Vyberte **Azure Active Directory**a potom **Registrace aplikací**. Zobrazí se seznam registrovaných aplikací.
1. Vyberte aplikaci ze seznamu. Zobrazí se stránka se zobrazenými podrobnostmi o registrované aplikaci.
1. V části **Spravovat**vyberte **branding**.
1. Aktualizujte **adresu URL domovské stránky** novou cestou.

   ![Stránka branding pro registrovanou aplikaci, která zobrazuje pole adresy URL domovské stránky](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. Vyberte **Uložit**.

## <a name="change-the-home-page-with-powershell"></a>Změnit domovskou stránku pomocí Powershellu

Pokud chcete nakonfigurovat domovskou stránku aplikace pomocí PowerShellu, musíte provést tyto kroky:

1. Nainstalujte modul Azure AD PowerShell.
1. Najděte hodnotu ObjectId aplikace.
1. Aktualizujte adresu URL domovské stránky aplikace pomocí příkazů PowerShellu.

### <a name="install-the-azure-ad-powershell-module"></a>Instalace modulu Azure AD Powershellu

Před definujete adresu URL vlastní domovskou stránku pomocí prostředí PowerShell, nainstalujte modul Azure AD PowerShell. Balíček si můžete stáhnout z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), který používá koncový bod Graph API.

Chcete-li nainstalovat balíček, postupujte takto:

1. Otevřete standardní okno Powershellu a spusťte následující příkaz:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Pokud příkaz spouštíte jako bez oprávnění správce, použijte `-scope currentuser` možnost.

1. Během instalace vyberte **Y** pro instalaci dvou balíčků z NuGet.org. Oba balíčky jsou povinné.

### <a name="find-the-objectid-of-the-app"></a>Najít ObjectId aplikace

Identifikátor ObjectId aplikace získáte tak, že ji vyhledáte jeho zobrazovaným jménem nebo domovskou stránkou.

1. Ve stejném okně prostředí PowerShell naimportujte modul Azure AD.

   ```powershell
   Import-Module AzureAD
   ```

1. Přihlaste se k modulu Azure AD jako správce tenanta.

   ```powershell
   Connect-AzureAD
   ```

1. Najděte aplikaci. Tento příklad používá PowerShell k vyhledání identifikátoru ObjectId hledáním aplikace se zobrazovaným názvem `SharePoint`.

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Měli byste získat výsledek, který je podobný znázorněno zde. Zkopírujte identifikátor GUID ObjectId pro použití v další části.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Alternativně můžete načíst seznam všech aplikací, vyhledat seznam pro aplikaci pomocí konkrétního zobrazovaného jména nebo domovské stránky a zkopírovat ObjectId aplikace, jakmile se aplikace najde.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Aktualizovat adresu URL domovské stránky

Vytvořte adresu URL domovské stránky a aktualizujte aplikaci s touto hodnotou. Pokračujte v používání stejného okna prostředí PowerShell, nebo pokud používáte nové okno PowerShellu, přihlaste se znovu k modulu Azure AD pomocí `Connect-AzureAD`. Pak postupujte podle těchto kroků:

1. Vytvořte proměnnou pro uchování hodnoty ObjectId, kterou jste zkopírovali v předchozí části. (Nahraďte hodnotu ObjectId použitou v tomto příkladu SharePointu s hodnotou ObjectId vaší aplikace.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. Ověřte, že máte správnou aplikaci, a to spuštěním následujícího příkazu. Výstup by měl být stejný jako výstup, který jste viděli v předchozí části ([Najít objectID aplikace](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. Vytvořte objekt prázdná aplikace pro uložení změn, které chcete provést.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. Adresa URL domovské stránky nastavena na hodnotu, která chcete. Hodnota musí být cesta subdoménu publikované aplikace. Například, pokud se změní adresa URL domovské stránky z `https://sharepoint-iddemo.msappproxy.net/` k `https://sharepoint-iddemo.msappproxy.net/hybrid/`, uživatelům aplikace přejít přímo na domovské stránce vlastní.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. Proveďte aktualizaci domovské stránky.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. Chcete-li ověřit, zda byla změna úspěšná, spusťte následující příkaz z kroku 2 znovu.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   Výstup by měl nyní vypadat takto:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Restartujte aplikaci a potvrďte tak, že se Domovská stránka zobrazí jako první obrazovka, podle očekávání.

> [!NOTE]
> Všechny změny provedené v aplikaci může resetovat adresa URL domovské stránky. Pokud vaše adresa URL domovské stránky resetuje, opakujte kroky v této části nastavte ji zpět.

## <a name="next-steps"></a>Další kroky

- [Povolit vzdálený přístup k Sharepointu pomocí Azure AD Application Proxy](application-proxy-integrate-with-sharepoint-server.md)
- [Kurz: Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace v Azure Active Directory](application-proxy-add-on-premises-application.md)