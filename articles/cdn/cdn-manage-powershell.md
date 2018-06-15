---
title: Správa Azure CDN pomocí prostředí PowerShell | Microsoft Docs
description: Další informace o použití rutin prostředí Azure PowerShell ke správě Azure CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: fb6f57a5-6e26-4847-8fd9-b51fb05a79eb
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 5634ecdec04f023d9eb901c4ad0fb21b13bcfdc1
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31592419"
---
# <a name="manage-azure-cdn-with-powershell"></a>Správa Azure CDN pomocí prostředí PowerShell
PowerShell poskytuje jednu z metod nejpružnější ke správě profilů Azure CDN a koncové body.  Interaktivní nebo psaní skriptů můžete použít PowerShell k automatizaci úloh správy.  Tento kurz představuje některé z nejčastějších úloh, můžete provést pomocí prostředí PowerShell ke správě profilů Azure CDN a koncové body.

## <a name="prerequisites"></a>Požadavky
Ke správě profilů Azure CDN a koncové body pomocí prostředí PowerShell, musí mít instalace modulu Azure PowerShell.  Další informace o instalaci prostředí Azure PowerShell a připojte se k Azure pomocí `Connect-AzureRmAccount` rutiny, najdete v části [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> Musíte se přihlásit s `Connect-AzureRmAccount` před spuštěním rutin prostředí Azure PowerShell.
> 
> 

## <a name="listing-the-azure-cdn-cmdlets"></a>Seznam rutin Azure CDN
Seznam všech rutin Azure CDN pomocí `Get-Command` rutiny.

```text
PS C:\> Get-Command -Module AzureRM.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpointNameAvailability             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfileSsoUrl                        2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Publish-AzureRmCdnEndpointContent                  2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnCustomDomain                      2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnEndpoint                          2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnProfile                           2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Start-AzureRmCdnEndpoint                           2.0.0      AzureRm.Cdn
Cmdlet          Stop-AzureRmCdnEndpoint                            2.0.0      AzureRm.Cdn
Cmdlet          Test-AzureRmCdnCustomDomain                        2.0.0      AzureRm.Cdn
Cmdlet          Unpublish-AzureRmCdnEndpointContent                2.0.0      AzureRm.Cdn
```

## <a name="getting-help"></a>Získání nápovědy
Můžete získat pomoc s žádným z těchto rutin pomocí `Get-Help` rutiny.  `Get-Help` poskytuje syntaxi a použití a volitelně zobrazuje příklady.

```text
PS C:\> Get-Help Get-AzureRmCdnProfile

NAME
    Get-AzureRmCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzureRmCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzureRmCdnProfile -examples".
    For more information, type: "get-help Get-AzureRmCdnProfile -detailed".
    For technical information, type: "get-help Get-AzureRmCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>Výpis existující profilů Azure CDN
`Get-AzureRmCdnProfile` Rutina bez parametrů načte všechny vaše stávající profily CDN.

```powershell
Get-AzureRmCdnProfile
```

Tento výstup lze přesměrovat do rutin pro výčet.

```powershell
# Output the name of all profiles on this subscription.
Get-AzureRmCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzureRmCdnProfile | Where-Object { $_.Sku.Name -eq "StandardVerizon" }
```

Můžete se taky vrátit jediného profilu tak, že zadáte název a prostředek skupiny profilu.

```powershell
Get-AzureRmCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

> [!TIP]
> Je možné mít víc profilů CDN se stejným názvem, tak dlouho, dokud jsou v různých skupinách prostředků.  Vynechání `ResourceGroupName` parametr vrátí všechny profily s odpovídajícím názvem.
> 
> 

## <a name="listing-existing-cdn-endpoints"></a>Výpis stávající koncové body CDN
`Get-AzureRmCdnEndpoint` může načíst koncový bod jednotlivé nebo všechny koncové body v profilu.  

```powershell
# Get a single endpoint.
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>Vytváření profilů CDN a koncové body
`New-AzureRmCdnProfile` a `New-AzureRmCdnEndpoint` slouží k vytváření profilů CDN a koncové body.

```powershell
# Create a new profile
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US"

# Create a new endpoint
New-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US" | New-AzureRmCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>Kontrola dostupnosti název koncového bodu
`Get-AzureRmCdnEndpointNameAvailability` Vrátí objekt, která udává, zda je název koncového bodu k dispozici.

```powershell
# Retrieve availability
$availability = Get-AzureRmCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>Přidání vlastní domény
`New-AzureRmCdnCustomDomain` Přidá vlastního názvu domény do existující koncový bod.

> [!IMPORTANT]
> Musíte vytvořit CNAME u svého poskytovatele DNS jak je popsáno v [jak namapovat vlastní doménu na koncový bod Content Delivery Network (CDN)](cdn-map-content-to-custom-domain.md).  Můžete otestovat mapování před změnou váš koncový bod pomocí `Test-AzureRmCdnCustomDomain`.
> 
> 

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzureRmCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzureRmCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>Úprava koncový bod
`Set-AzureRmCdnEndpoint` upravuje existující koncový bod.

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzureRmCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>Vymazání/vedlejší-loading CDN prostředky
`Unpublish-AzureRmCdnEndpointContent` vyprazdňovat mezipaměti prostředky, zatímco `Publish-AzureRmCdnEndpointContent` předem načte prostředky na podporovaných koncových bodů.

```powershell
# Purge some assets.
Unpublish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Unpublish-AzureRmCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>Koncové body CDN spuštění nebo zastavení
`Start-AzureRmCdnEndpoint` a `Stop-AzureRmCdnEndpoint` lze spustit a zastavit jednotlivé koncové body nebo skupiny koncových bodů.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Stop-AzureRmCdnEndpoint

# Start all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Start-AzureRmCdnEndpoint
```

## <a name="deleting-cdn-resources"></a>Odstranění prostředků CDN
`Remove-AzureRmCdnProfile` a `Remove-AzureRmCdnEndpoint` slouží k odebrání profily a koncové body.

```powershell
# Remove a single endpoint
Remove-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzureRmCdnEndpoint | Remove-AzureRmCdnEndpoint -Force

# Remove a single profile
Remove-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>Další kroky
Přečtěte si informace o automatizaci Azure CDN s [.NET](cdn-app-dev-net.md) nebo [Node.js](cdn-app-dev-node.md).

Další informace o funkcích CDN najdete v tématu [přehled CDN](cdn-overview.md).

