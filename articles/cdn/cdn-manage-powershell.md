---
title: Správa Azure CDN pomocí Powershellu | Dokumentace Microsoftu
description: Další informace o použití rutin prostředí Azure PowerShell ke správě Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: fb6f57a5-6e26-4847-8fd9-b51fb05a79eb
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: magattus
ms.openlocfilehash: 5dc05b08e84decf958481d19b9cfb820947fae78
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237040"
---
# <a name="manage-azure-cdn-with-powershell"></a>Správa Azure CDN pomocí Powershellu
PowerShell poskytuje jednu z metod nejflexibilnější ke správě koncových bodů a profilů CDN.  Interaktivně nebo psaní skriptů můžete použít PowerShell k automatizaci úloh správy.  Tento kurz ukazuje některé z nejčastějších úloh můžete provést pomocí Powershellu ke správě koncových bodů a profilů CDN.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Použití Powershellu ke správě koncových bodů a profilů CDN, musíte mít instalace modulu Azure Powershellu.  Další informace o instalaci prostředí Azure PowerShell a připojit se k Azure s využitím `Connect-AzAccount` rutiny, najdete v článku [instalace a konfigurace Azure Powershellu](/powershell/azure/overview).

> [!IMPORTANT]
> Musíte se přihlásit pomocí `Connect-AzAccount` před spuštěním rutin prostředí Azure PowerShell.
> 
> 

## <a name="listing-the-azure-cdn-cmdlets"></a>Seznam rutin Azure CDN
Seznam všech rutin Azure CDN pomocí `Get-Command` rutiny.

```text
PS C:\> Get-Command -Module Az.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzCdnCustomDomain                         2.0.0      Az.Cdn
Cmdlet          Get-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          Get-AzCdnEndpointNameAvailability             2.0.0      Az.Cdn
Cmdlet          Get-AzCdnOrigin                               2.0.0      Az.Cdn
Cmdlet          Get-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Get-AzCdnProfileSsoUrl                        2.0.0      Az.Cdn
Cmdlet          New-AzCdnCustomDomain                         2.0.0      Az.Cdn
Cmdlet          New-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          New-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Publish-AzCdnEndpointContent                  2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnCustomDomain                      2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnEndpoint                          2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnProfile                           2.0.0      Az.Cdn
Cmdlet          Set-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          Set-AzCdnOrigin                               2.0.0      Az.Cdn
Cmdlet          Set-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Start-AzCdnEndpoint                           2.0.0      Az.Cdn
Cmdlet          Stop-AzCdnEndpoint                            2.0.0      Az.Cdn
Cmdlet          Test-AzCdnCustomDomain                        2.0.0      Az.Cdn
Cmdlet          Unpublish-AzCdnEndpointContent                2.0.0      Az.Cdn
```

## <a name="getting-help"></a>Získání nápovědy
Můžete získat pomoc s některou z těchto rutin pomocí `Get-Help` rutiny.  `Get-Help` poskytuje syntaxi a použití a volitelně zobrazuje příklady.

```text
PS C:\> Get-Help Get-AzCdnProfile

NAME
    Get-AzCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzCdnProfile -examples".
    For more information, type: "get-help Get-AzCdnProfile -detailed".
    For technical information, type: "get-help Get-AzCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>Výpis existujících profilů Azure CDN
`Get-AzCdnProfile` Rutiny bez parametrů obnoví všechny vaše stávající profily CDN.

```powershell
Get-AzCdnProfile
```

Tento výstup lze přesměrovat na rutiny pro výčet.

```powershell
# Output the name of all profiles on this subscription.
Get-AzCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzCdnProfile | Where-Object { $_.Sku.Name -eq "Standard_Verizon" }
```

Můžete se taky vrátit jeden profil tak, že zadáte profil a skupinou prostředků.

```powershell
Get-AzCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

> [!TIP]
> Je možné mít víc profilů CDN se stejným názvem, tak dlouho, dokud jsou v různých skupinách prostředků.  Vynechání `ResourceGroupName` parametr vrátí všechny profily s odpovídajícím názvem.
> 
> 

## <a name="listing-existing-cdn-endpoints"></a>Výpis stávající koncové body CDN
`Get-AzCdnEndpoint` můžete načíst koncový bod jednotlivých nebo všechny koncové body v profilu.  

```powershell
# Get a single endpoint.
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzCdnProfile | Get-AzCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzCdnProfile | Get-AzCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>Vytváření koncových bodů a profilů CDN
`New-AzCdnProfile` a `New-AzCdnEndpoint` se používají k vytváření koncových bodů a profilů CDN. Podporují se následující SKU:
- Standard_Verizon
- Premium_Verizon
- Custom_Verizon
- Standard_Akamai
- Standard_Microsoft
- Standard_ChinaCdn

```powershell
# Create a new profile
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US"

# Create a new endpoint
New-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US" | New-AzCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>Kontrola dostupnosti názvu koncového bodu
`Get-AzCdnEndpointNameAvailability` Vrátí objekt označující, zda je k dispozici název koncového bodu.

```powershell
# Retrieve availability
$availability = Get-AzCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>Přidání vlastní domény
`New-AzCdnCustomDomain` Přidá název vlastní domény do existující koncový bod.

> [!IMPORTANT]
> Musíte vytvořit záznam CNAME u svého poskytovatele DNS jak je popsáno v [jak namapovat vlastní doménu na koncový bod Content Delivery Network (CDN)](cdn-map-content-to-custom-domain.md).  Můžete testovat mapování před změnou vašeho koncového bodu pomocí `Test-AzCdnCustomDomain`.
> 
> 

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>Změna koncového bodu
`Set-AzCdnEndpoint` upravuje existující koncový bod.

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>Vymazání/Předprodukčním-loading CDN prostředky
`Unpublish-AzCdnEndpointContent` Vymazat do mezipaměti prostředky, zatímco `Publish-AzCdnEndpointContent` předběžné načtení prostředků v podporovaných koncových bodů.

```powershell
# Purge some assets.
Unpublish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzCdnProfile | Get-AzCdnEndpoint | Unpublish-AzCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>Koncové body CDN spuštění/zastavení
`Start-AzCdnEndpoint` a `Stop-AzCdnEndpoint` umožňuje spouštět a zastavovat jednotlivé koncové body nebo skupiny koncových bodů.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Stop-AzCdnEndpoint

# Start all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Start-AzCdnEndpoint
```

## <a name="deleting-cdn-resources"></a>Odstranění prostředků CDN
`Remove-AzCdnProfile` a `Remove-AzCdnEndpoint` slouží k odstranění koncových bodů a profilů.

```powershell
# Remove a single endpoint
Remove-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzCdnEndpoint | Remove-AzCdnEndpoint -Force

# Remove a single profile
Remove-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>Další kroky
Přečtěte si informace o automatizaci Azure CDN s [.NET](cdn-app-dev-net.md) nebo [Node.js](cdn-app-dev-node.md).

Další informace o funkcích CDN, najdete v článku [přehled sítě CDN](cdn-overview.md).

