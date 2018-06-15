---
title: Správa zón DNS v Azure DNS - prostředí PowerShell | Microsoft Docs
description: Můžete spravovat zóny DNS pomocí Azure Powershell. Tento článek popisuje, jak k aktualizaci, odstranění a vytvoření zóny DNS na Azure DNS
services: dns
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: a67992ab-8166-4052-9b28-554c5a39e60c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: kumud
ms.openlocfilehash: e7b0bc32d3fa8fbcf73298b6988655fca7cfa793
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
ms.locfileid: "32772649"
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>Správa zón DNS pomocí prostředí PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-dnszones-cli.md)

Tento článek ukazuje, jak spravovat zóny DNS pomocí prostředí Azure PowerShell. Můžete také spravovat zóny DNS pomocí napříč platformami [rozhraní příkazového řádku Azure](dns-operations-dnszones-cli.md) nebo portálu Azure.

Tato příručka konkrétně zabývá veřejném DNS zóny. Informace o použití prostředí Azure PowerShell můžete spravovat soukromé zóny v Azure DNS najdete v tématu [začít pracovat s zón DNS privátní Azure pomocí Azure PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>Vytvoření zóny DNS

Zóna DNS se vytvoří pomocí rutiny `New-AzureRmDnsZone`.

Následující příklad vytvoří zónu DNS s názvem *contoso.com* ve skupině prostředků s názvem *MyResourceGroup*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

Následující příklad ukazuje, jak vytvořit zónu DNS se dvěma [Azure Resource Manager značky](dns-zones-records.md#tags), *project = demo* a *env = test*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

Azure DNS teď podporuje také privátní zóny DNS (aktuálně ve verzi Public Preview).  Další informace o privátních zónách DNS najdete v tématu [Použití DNS Azure pro privátní domény](private-dns-overview.md). Příklad vytvoření privátní zóny DNS najdete v tématu [Začínáme s privátními zónami Azure DNS pomocí PowerShellu](./private-dns-getstarted-powershell.md).

## <a name="get-a-dns-zone"></a>Získat zóny DNS

Chcete-li načíst zónu DNS, použijte `Get-AzureRmDnsZone` rutiny. Tato operace vrátí objekt zóny DNS odpovídající stávající zónu v Azure DNS. Objekt obsahuje data o zóny (například počet sad záznamů), ale neobsahuje sady záznamů, sami (viz `Get-AzureRmDnsRecordSet`).

```powershell
Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-8ec2-f4879750d201
Tags                  : {project, env}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org.,
                        ns4-01.azure-dns.info.}
NumberOfRecordSets    : 2
MaxNumberOfRecordSets : 5000
```

## <a name="list-dns-zones"></a>Výpis zón DNS

Vynecháním názvu zóny v rutině `Get-AzureRmDnsZone` můžete zobrazit výčet všech zón ve skupině prostředků. Tato operace vrátí pole objektů zón.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Vynecháním názvu zóny i názvu skupiny prostředků v rutině `Get-AzureRmDnsZone` můžete zobrazit výčet všech zón v předplatném Azure.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-zone"></a>Aktualizace zóny DNS

Změny prostředku zóny DNS je možné provádět pomocí rutiny `Set-AzureRmDnsZone`. Tato rutina neaktualizuje žádné sady záznamů DNS v rámci zóny (viz [Správa záznamů DNS](dns-operations-recordsets.md)). Slouží pouze k aktualizaci vlastností samotného prostředku zóny. Vlastnosti zapisovatelné zóny jsou aktuálně omezená na [Azure Resource Manager, značky' pro daný prostředek zóny](dns-zones-records.md#tags).

Použijte jednu z následujících dvou způsobů k aktualizaci zóny DNS:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Zadejte zóny pomocí skupině název a prostředku zóny

Tento přístup nahradí hodnoty zadané existující zóna značky.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>Určení zóny pomocí objektu $zone

Tento postup načte existující objekt zóny, upraví značek a pak potvrdí změny. Tímto způsobem je možné zachovat stávající značky.

```powershell
# Get the zone object
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

# Remove an existing tag
$zone.Tags.Remove("project")

# Add a new tag
$zone.Tags.Add("status","approved")

# Commit changes
Set-AzureRmDnsZone -Zone $zone
```

Při použití `Set-AzureRmDnsZone` s objektem $zone [kontroluje Etag](dns-zones-records.md#etags) zajišťují souběžných změny se nepřepíšou. Můžete použít nepovinný `-Overwrite` přepínač tak, aby potlačit těchto kontrol.

## <a name="delete-a-dns-zone"></a>Odstranit zónu DNS

Zóny DNS lze odstranit pomocí `Remove-AzureRmDnsZone` rutiny.

> [!NOTE]
> Odstraněním zóny DNS dojde také k odstranění všech záznamů DNS v rámci dané zóny. Tato operace se nedá vrátit zpět. Pokud se zóna DNS používá, služby využívající tuto zónu při jejím odstranění selžou.
>
>Informace o ochraně před náhodným odstraněním zóny najdete v tématu [Ochrana záznamů a zón DNS](dns-protect-zones-recordsets.md).


Zónu DNS můžete odstranit některým z následujících dvou způsobů:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Určení zóny pomocí názvu zóny a názvu skupiny prostředků

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Určení zóny pomocí objektu $zone

Zónu k odstranění můžete určit pomocí objektu `$zone` vráceného rutinou `Get-AzureRmDnsZone`.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

Místo předání jako parametru je možné objekt zóny předat také rourou:

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone

```

Stejně jako u `Set-AzureRmDnsZone`, zadání pomocí zóny `$zone` objektu umožňuje Značka Etag kontroly, aby se zajistilo, souběžných změny nebudou odstraněny. Použití `-Overwrite` přepínač tak, aby potlačit těchto kontrol.

## <a name="confirmation-prompts"></a>Výzvy k potvrzení

Všechny rutiny `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` a `Remove-AzureRmDnsZone` podporují výzvy k potvrzení.

Rutiny `New-AzureRmDnsZone` a `Set-AzureRmDnsZone` zobrazí výzvu k potvrzení v případě, že má proměnná předvolby PowerShellu `$ConfirmPreference` hodnotu `Medium` nebo nižší. Kvůli potenciálně velkému dopadu odstranění zóny DNS zobrazí rutina `Remove-AzureRmDnsZone` výzvu k potvrzení v případě, že má proměnná PowerShellu `$ConfirmPreference` jinou hodnotu než `None`.

Vzhledem k tomu, že výchozí hodnota proměnné `$ConfirmPreference` je `High`, ve výchozím nastavení zobrazí výzvu k potvrzení pouze rutina `Remove-AzureRmDnsZone`.

Aktuální nastavení `$ConfirmPreference` můžete přepsat pomocí parametru `-Confirm`. Pokud zadáte `-Confirm` nebo `-Confirm:$True`, rutina před spuštěním zobrazí výzvu k potvrzení. Pokud zadáte `-Confirm:$False`, rutina výzvu k potvrzení nezobrazí.

Další informace o `-Confirm` a `$ConfirmPreference` najdete v tématu [Informace o proměnných předvoleb](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables).

## <a name="next-steps"></a>Další postup

Zjistěte, jak [spravovat sady záznamů a záznamy](dns-operations-recordsets.md) ve vaší zóně DNS.
<br>
Zjistěte, jak [delegování domény do Azure DNS](dns-domain-delegation.md).
<br>
Zkontrolujte [Azure DNS PowerShell referenční dokumentaci k nástroji](/powershell/module/azurerm.dns).

