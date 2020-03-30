---
title: Vlastní domovská stránka pro publikované aplikace – proxy aplikací Azure AD
description: Popisuje základy konektorů proxy aplikací Azure AD
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275602"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Nastavení vlastní domovské stránky pro publikované aplikace pomocí proxy aplikace Azure AD

Tento článek popisuje, jak nakonfigurovat aplikaci tak, aby uživatele nasměrovala na vlastní domovskou stránku. Když publikujete aplikaci s proxy aplikací, nastavíte interní adresu URL, ale někdy to není stránka, kterou by měl uživatel vidět jako první. Nastavte vlastní domovskou stránku tak, aby uživatel získal správnou stránku při přístupu k aplikaci. Uživateli se zobrazí vlastní domovská stránka, kterou nastavíte, bez ohledu na to, jestli má přístup k aplikaci z panelu Azure Active Directory Access Panel nebo spouštěče aplikací Office 365.

Když uživatel spustí aplikaci, ve výchozím nastavení se přesměrovává na adresu URL kořenové domény publikované aplikace. Vstupní stránka je obvykle nastavena jako adresa URL domovské stránky. Modul Azure AD PowerShell slouží k definování vlastní adresy URL domovské stránky, když chcete, aby uživatel aplikace přistál na konkrétní stránce v rámci aplikace.

Zde je jeden scénář, který vysvětluje, proč by vaše společnost nastavit vlastní domovskou stránku:

- Uvnitř podnikové sítě se uživatel `https://ExpenseApp/login/login.aspx` přihlásí a přihlášuje k vaší aplikaci.
- Vzhledem k tomu, že máte další prostředky (například obrázky), které proxy aplikace `https://ExpenseApp` potřebuje přístup na nejvyšší úrovni struktury složek, publikujete aplikaci s jako interní adresu URL.
- Výchozí externí adresa `https://ExpenseApp-contoso.msappproxy.net`URL je , která nepřenese externího uživatele na přihlašovací stránku.
- Chcete místo `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` toho nastavit jako adresu URL domovské stránky, aby se nejprve zobrazí přihlašovací stránka externímu uživateli.

> [!NOTE]
> Když uživatelům uděláte přístup k publikovaným aplikacím, zobrazí se aplikace na [přístupovém panelu Azure AD](../user-help/my-apps-portal-end-user-access.md) a ve [spouštěči aplikací Office 365](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Než začnete

Před nastavením adresy URL domovské stránky mějte na paměti následující požadavky:

- Cesta, kterou zadáte, musí být subdoménovou cestou adresy URL kořenové domény.

  Pokud je `https://apps.contoso.com/app1/`například adresa URL kořenové domény , musí adresa `https://apps.contoso.com/app1/`URL domovské stránky, kterou nakonfigurujete, začínat aplikací .

- Pokud v publikované aplikaci provedete změnu, může změna obnovit hodnotu adresy URL domovské stránky. Když v budoucnu aktualizujete aplikaci, měli byste znovu zkontrolovat a v případě potřeby aktualizovat adresu URL domovské stránky.

Adresu URL domovské stránky můžete nastavit buď prostřednictvím portálu Azure nebo pomocí PowerShellu.

## <a name="change-the-home-page-in-the-azure-portal"></a>Změna domovské stránky na webu Azure Portal

Pokud chcete změnit adresu URL domovské stránky aplikace na portálu Azure AD, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) jako správce.
1. Vyberte **Službu Azure Active Directory**a potom **registrace aplikací**. Zobrazí se seznam registrovaných aplikací.
1. Vyberte si aplikaci ze seznamu. Zobrazí se stránka s podrobnostmi o registrované aplikaci.
1. V části **Správa**vyberte **Branding**.
1. Aktualizujte **adresu URL domovské stránky** novou cestou.

   ![Stránka značky pro registrovanou aplikaci s polem URL domovské stránky](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. Vyberte **Uložit**.

## <a name="change-the-home-page-with-powershell"></a>Změna domovské stránky pomocí PowerShellu

Chcete-li nakonfigurovat domovskou stránku aplikace pomocí PowerShellu, musíte:

1. Nainstalujte modul Azure AD PowerShell.
1. Najděte hodnotu ObjectId aplikace.
1. Aktualizujte adresu URL domovské stránky aplikace pomocí příkazů PowerShellu.

### <a name="install-the-azure-ad-powershell-module"></a>Instalace modulu Azure AD PowerShell

Než definujete vlastní adresu URL domovské stránky pomocí PowerShellu, nainstalujte modul Azure AD PowerShell.Balíček si můžete stáhnout z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), která používá koncový bod rozhraní API grafu.

Chcete-li balíček nainstalovat, postupujte takto:

1. Otevřete standardní okno PowerShellu a spusťte následující příkaz:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Pokud příkaz používáte jako nesprávce, použijte `-scope currentuser` tuto možnost.

1. Během instalace vyberte **y,** chcete-li nainstalovat dva balíčky z Nuget.org. Oba balíčky jsou povinné.

### <a name="find-the-objectid-of-the-app"></a>Najít Objektid aplikace

Získáte ObjectId aplikace hledáním aplikace podle jejího zobrazovaného názvu nebo domovské stránky.

1. Ve stejném okně Prostředí PowerShell importujte modul Azure AD.

   ```powershell
   Import-Module AzureAD
   ```

1. Přihlaste se k modulu Azure AD jako správce klienta.

   ```powershell
   Connect-AzureAD
   ```

1. Najděte aplikaci. Tento příklad používá PowerShell k vyhledání Objektu objectid `SharePoint`vyhledáním aplikace se zobrazovaným názvem .

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Měli byste získat výsledek, který je podobný tomu, který je zde zobrazen. Zkopírujte identifikátor GUID ObjektU pro uvažování, který chcete použít v další části.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Případně můžete jen vytáhnout seznam všech aplikací, hledat v seznamu aplikace s konkrétním zobrazovaným jménem nebo domovskou stránkou a zkopírovat objekt ObjectId aplikace, jakmile je aplikace nalezena.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Aktualizace adresy URL domovské stránky

Vytvořte adresu URL domovské stránky a aktualizujte aplikaci s tou hodnotou. Pokračujte v používání stejného okna PowerShellu, nebo pokud používáte nové okno PowerShellu, přihlaste se znovu k modulu Azure AD pomocí `Connect-AzureAD`. Potom postupujte podle těchto kroků:

1. Vytvořte proměnnou pro uložení hodnoty ObjectId, kterou jste zkopírovali v předchozím oddíle. (Nahraďte hodnotu ObjectId použitou v tomto příkladu SharePointu hodnotou ObjectId aplikace.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. Spuštěním následujícího příkazu potvrďte, že máte správnou aplikaci. Výstup by měl být shodný s výstupem, který jste viděli v předchozí části ([Najděte objektid aplikace](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. Vytvořte prázdný objekt aplikace, který uchová změny, které chcete provést.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. Nastavte adresu URL domovské stránky na požadovanou hodnotu. Hodnota musí být subdoménovou cestou publikované aplikace. Pokud například změníte adresu URL `https://sharepoint-iddemo.msappproxy.net/` `https://sharepoint-iddemo.msappproxy.net/hybrid/`domovské stránky z na , uživatelé aplikace přejdou přímo na vlastní domovskou stránku.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. Proveďte aktualizaci domovské stránky.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. Chcete-li ověřit, zda byla změna úspěšná, spusťte znovu následující příkaz z kroku 2.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   Pro náš příklad by měl výstup nyní vypadat takto:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Restartujte aplikaci a potvrďte, že se domovská stránka zobrazí jako první obrazovka podle očekávání.

> [!NOTE]
> Všechny změny provedené v aplikaci mohou obnovit adresu URL domovské stránky. Pokud se adresa URL domovské stránky resetuje, opakujte kroky v této části a nastavte ji zpět.

## <a name="next-steps"></a>Další kroky

- [Povolení vzdáleného přístupu k SharePointu s využitím Proxy aplikací služby Azure AD](application-proxy-integrate-with-sharepoint-server.md)
- [Kurz: Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace ve službě Azure Active Directory](application-proxy-add-on-premises-application.md)