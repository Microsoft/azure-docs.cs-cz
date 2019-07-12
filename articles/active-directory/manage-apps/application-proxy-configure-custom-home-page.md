---
title: Nastavit vlastní domovskou stránku pro aplikace publikované pomocí Proxy aplikací Azure AD | Dokumentace Microsoftu
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
ms.openlocfilehash: 51596e4db8999de5089748e40f9b24bd46c84e56
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807833"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Nastavit vlastní domovskou stránku pro aplikace publikované pomocí Proxy aplikací Azure AD

Tento článek popisuje, jak nakonfigurovat aplikaci nasměrovat uživatele na vlastní domovskou stránku. Při publikování aplikací pomocí Proxy aplikace nastavit interní adresa URL, ale v některých případech to není stránku, kterou uživatel měli vidět první. Nastavte vlastní domovskou stránku tak, že uživatel získá správnou stránku při přístupu k aplikaci. Uživateli se zobrazí vlastní domovskou stránku, že jste nastavili, bez ohledu na to, jestli se přístup k aplikaci z Azure Active Directory přístupového panelu nebo Spouštěče aplikací Office 365.

Když uživatel spustí aplikaci, se už přesměruje ve výchozím nastavení domény kořenovou adresu URL pro publikovanou aplikaci. Cílová stránka je obvykle nastavena jako adresa URL domovské stránky. Pomocí modulu Azure AD Powershellu k definování adresy URL vlastní domovskou stránku, pokud chcete, aby uživatel aplikace objevil na určitou stránku v aplikaci.

Tady je jeden scénář, který vysvětluje, proč vaše společnost nastaví vlastní domovskou stránku:

- Uvnitř firemní sítě, uživatel přejde za účelem `https://ExpenseApp/login/login.aspx` přihlásit a získat přístup k vaší aplikaci.
- Protože máte jiné prostředky (jako jsou obrázky), které Proxy aplikace potřebuje pro přístup k na nejvyšší úrovni struktury složek, můžete publikovat aplikaci s `https://ExpenseApp` jako interní adresa URL.
- Výchozí externí adresa URL je `https://ExpenseApp-contoso.msappproxy.net`, který nepřijímá externího uživatele na přihlašovací stránku.
- Chcete nastavit `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` jako adresa URL domovské stránky místo toho tak externího uživatele se zobrazí přihlašovací stránka nejprve.

> [!NOTE]
> Když poskytnete uživatelům přístup k publikovaným aplikacím, aplikace se zobrazují v [přístupový Panel Azure AD](../user-help/my-apps-portal-end-user-access.md) a [Spouštěči aplikací Office 365](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Než začnete

Než nastavíte adresa URL domovské stránky, mějte na paměti následující požadavky:

- Cesta, která zadáte musí být cesta subdoménu kořenové domény adresy URL.

  Například, pokud je adresa URL kořenovou doménu `https://apps.contoso.com/app1/`, adresa URL domovské stránky, který nakonfigurujete musí začínat znakem `https://apps.contoso.com/app1/`.

- Pokud provedete změny k publikované aplikaci, může změna resetovat hodnotu adresy URL domovské stránky. Při aktualizaci aplikace v budoucnu, by měl znovu zkontrolujte a v případě potřeby aktualizujte adresu URL domovské stránky.

Adresa URL domovské stránky na webu Azure portal nebo pomocí prostředí PowerShell můžete nastavit.

## <a name="change-the-home-page-in-the-azure-portal"></a>Změnit domovskou stránku na webu Azure Portal

Chcete-li změnit adresu URL domovské stránky vaší aplikace prostřednictvím portálu Azure AD, postupujte podle těchto kroků:

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com/) jako správce.
1. Vyberte **Azure Active Directory**a potom **registrace aplikací**. Zobrazí se seznam registrovaných aplikací.
1. Vyberte aplikaci ze seznamu. Zobrazí se stránka s podrobnostmi o registrovaná aplikace.
1. V části **spravovat**vyberte **značky**.
1. Aktualizace **adresa URL domovské stránky** novou cestu.

   ![Odkaz na stránku pro registrovaná aplikace zobrazuje pole Adresa URL domovské stránky](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. Vyberte **Uložit**.

## <a name="change-the-home-page-with-powershell"></a>Změnit domovskou stránku pomocí Powershellu

Pokud chcete nakonfigurovat domovskou stránku aplikace pomocí Powershellu, budete muset:

1. Instalace modulu Azure AD PowerShell.
1. Vrátí hodnotu ID objektu aplikace.
1. Aktualizujte adresu URL domovské stránky aplikace pomocí příkazů Powershellu.

### <a name="install-the-azure-ad-powershell-module"></a>Instalace modulu Azure AD Powershellu

Před definujete adresu URL vlastní domovskou stránku pomocí prostředí PowerShell, nainstalujte modul Azure AD PowerShell. Si můžete stáhnout balíček z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), který používá koncový bod rozhraní Graph API.

Chcete-li nainstalovat balíček, postupujte takto:

1. Otevřete standardní okno Powershellu a spusťte následující příkaz:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Pokud příkaz spouštíte jako bez oprávnění správce, použijte `-scope currentuser` možnost.

1. Během instalace, vybrat **Y** instalace dva balíčky z Nuget.org. Oba balíčky jsou povinné.

### <a name="find-the-objectid-of-the-app"></a>Najít ID objektu aplikace

Získání ID objektu aplikace tak, že aplikace podle zobrazovaného názvu nebo Domovská stránka.

1. Ve stejném okně prostředí PowerShell naimportujte modul Azure AD.

   ```powershell
   Import-Module AzureAD
   ```

1. Přihlaste se k modulu Azure AD jako správce tenanta.

   ```powershell
   Connect-AzureAD
   ```

1. Najděte aplikaci. Tento příklad používá k nalezení ObjectId tak, že pro aplikaci se zobrazovaným názvem z prostředí PowerShell `SharePoint`.

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Měli byste získat výsledek, který je podobný znázorněno zde. Zkopírujte identifikátor GUID ObjectId pro použití v další části.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Alternativně můžete jenom o přijetí změn seznam všech aplikací, v seznamu pro aplikace s konkrétní zobrazovaný název nebo webové stránky, vyhledejte a zkopírujte ID objektu aplikace po nalezení aplikace.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Aktualizovat adresu URL domovské stránky

Vytvořit adresu URL domovské stránky a aktualizujete aplikaci s touto hodnotou. Dál používat stejné okno prostředí PowerShell, nebo pokud používáte nové okno prostředí PowerShell, přihlaste se k modulu Azure AD s použitím znovu `Connect-AzureAD`. Pak postupujte podle těchto kroků:

1. Vytvořte proměnnou pro uchování hodnoty ID objektu, který jste zkopírovali v předchozí části. (Nahradit ObjectId hodnota použitá v tomto příkladu Sharepointu s hodnotou ID objektu vaší aplikace.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. Potvrďte, že máte správné aplikace spuštěním následujícího příkazu. Výstup by měl být stejný jako výstup jste viděli v předchozí části ([zjistit ObjectId aplikace](#find-the-objectid-of-the-app)).

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

1. Pokud chcete potvrdit, že tato změna byla úspěšná, spusťte následující příkaz znovu z kroku 2.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   V našem příkladu výstup by měl nyní vypadat takto:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Restartujte aplikaci a potvrdit, na domovské stránce zobrazí jako první obrazovky podle očekávání.

> [!NOTE]
> Všechny změny provedené v aplikaci může resetovat adresa URL domovské stránky. Pokud vaše adresa URL domovské stránky resetuje, opakujte kroky v této části nastavte ji zpět.

## <a name="next-steps"></a>Další postup

- [Povolit vzdálený přístup k Sharepointu pomocí Azure AD Application Proxy](application-proxy-integrate-with-sharepoint-server.md)
- [Kurz: Přidat místní aplikace pro vzdálený přístup prostřednictvím Proxy aplikací v Azure Active Directory](application-proxy-add-on-premises-application.md)