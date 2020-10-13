---
title: Správa zón DNS v Azure DNS – PowerShell | Microsoft Docs
description: Zóny DNS můžete spravovat pomocí Azure PowerShellu. Tento článek popisuje, jak aktualizovat, odstranit a vytvořit zóny DNS na Azure DNS
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: rohink
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 347fde86b2a37aa0f82c09c94d6aa2f9e405da9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89075592"
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>Správa Zóny DNS pomocí prostředí PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Rozhraní příkazového řádku Azure Classic](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

V tomto článku se dozvíte, jak spravovat zóny DNS pomocí Azure PowerShell. Zóny DNS můžete spravovat i pomocí [Azure CLI](dns-operations-dnszones-cli.md) pro různé platformy nebo Azure Portal.

V této příručce se konkrétně zabývá veřejné zóny DNS. Informace o použití Azure PowerShell ke správě privátních zón v Azure DNS najdete v tématu Začínáme [s Azure DNS Private Zones pomocí Azure PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>Vytvoření zóny DNS

Zóna DNS se vytvoří pomocí rutiny `New-AzureRmDnsZone`.

Následující příklad vytvoří zónu DNS s názvem *contoso.com* ve skupině prostředků s názvem *MyResourceGroup*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

Následující příklad ukazuje, jak vytvořit zónu DNS se dvěma [Azure Resource Manager značkami](dns-zones-records.md#tags) *Project = demo* a *ENV = test*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

Azure DNS podporuje i privátní zóny DNS.  Další informace o privátních zónách DNS najdete v tématu [Použití DNS Azure pro privátní domény](private-dns-overview.md). Příklad vytvoření privátní zóny DNS najdete v tématu [Začínáme s privátními zónami Azure DNS pomocí PowerShellu](./private-dns-getstarted-powershell.md).

## <a name="get-a-dns-zone"></a>Získání zóny DNS

K načtení zóny DNS použijte `Get-AzureRmDnsZone` rutinu. Tato operace vrátí objekt zóny DNS odpovídající existující zóně v Azure DNS. Objekt obsahuje data o zóně (například počet sad záznamů), ale neobsahuje samotné sady záznamů (viz `Get-AzureRmDnsRecordSet` ).

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

Změny prostředku zóny DNS je možné provádět pomocí rutiny `Set-AzureRmDnsZone`. Tato rutina neaktualizuje žádné sady záznamů DNS v rámci zóny (viz [Správa záznamů DNS](dns-operations-recordsets.md)). Slouží pouze k aktualizaci vlastností samotného prostředku zóny. Vlastnosti zóny s možností zápisu jsou v současné době omezeny na [Azure Resource Manager značky pro prostředek zóny](dns-zones-records.md#tags).

K aktualizaci zóny DNS použijte jeden z následujících dvou způsobů:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Zadejte zónu pomocí názvu zóny a skupiny prostředků.

Tento přístup nahradí existující značky zóny zadanými hodnotami.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>Určení zóny pomocí objektu $zone

Tento přístup načte existující objekt zóny, upraví značky a pak změny potvrdí. Tímto způsobem lze zachovat existující značky.

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

Při použití `Set-AzureRmDnsZone` s objektem $Zone se pro zajištění, že nejsou přepsány souběžné změny, používají [kontroly ETag](dns-zones-records.md#etags) . `-Overwrite`Tyto kontroly můžete potlačit pomocí volitelného přepínače.

## <a name="delete-a-dns-zone"></a>Odstranění zóny DNS

Zóny DNS je možné odstranit pomocí `Remove-AzureRmDnsZone` rutiny.

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

Stejně jako u `Set-AzureRmDnsZone` , určení zóny pomocí `$zone` objektu umožňuje kontrolám značek ETag, aby se zajistilo, že se neodstraní souběžné změny. `-Overwrite`K potlačení těchto kontrol použijte přepínač.

## <a name="confirmation-prompts"></a>Výzvy k potvrzení

Všechny rutiny `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` a `Remove-AzureRmDnsZone` podporují výzvy k potvrzení.

Rutiny `New-AzureRmDnsZone` a `Set-AzureRmDnsZone` zobrazí výzvu k potvrzení v případě, že má proměnná předvolby PowerShellu `$ConfirmPreference` hodnotu `Medium` nebo nižší. Kvůli potenciálně velkému dopadu odstranění zóny DNS zobrazí rutina `Remove-AzureRmDnsZone` výzvu k potvrzení v případě, že má proměnná PowerShellu `$ConfirmPreference` jinou hodnotu než `None`.

Vzhledem k tomu, že výchozí hodnota proměnné `$ConfirmPreference` je `High`, ve výchozím nastavení zobrazí výzvu k potvrzení pouze rutina `Remove-AzureRmDnsZone`.

Aktuální nastavení `$ConfirmPreference` můžete přepsat pomocí parametru `-Confirm`. Pokud zadáte `-Confirm` nebo `-Confirm:$True`, rutina před spuštěním zobrazí výzvu k potvrzení. Pokud zadáte `-Confirm:$False`, rutina výzvu k potvrzení nezobrazí.

Další informace o `-Confirm` a `$ConfirmPreference` najdete v tématu [Informace o proměnných předvoleb](/powershell/module/microsoft.powershell.core/about/about_preference_variables).

## <a name="next-steps"></a>Další kroky

Naučte se [Spravovat sady záznamů a záznamy](dns-operations-recordsets.md) v zóně DNS.
<br>
Přečtěte si, jak [delegovat doménu na Azure DNS](dns-domain-delegation.md).
<br>
Přečtěte si [referenční dokumentaci k Azure DNS PowerShellu](/powershell/module/azurerm.dns).

