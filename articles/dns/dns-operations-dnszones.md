---
title: Správa zón DNS v Azure DNS – PowerShell | Microsoft Docs
description: Zóny DNS můžete spravovat pomocí Azure PowerShellu. Tento článek popisuje, jak aktualizovat, odstranit a vytvořit zóny DNS na Azure DNS
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.assetid: a67992ab-8166-4052-9b28-554c5a39e60c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: victorh
ms.openlocfilehash: dd238be6dbfcd14480b2c0cf03236902ef39e722
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959409"
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>Správa Zóny DNS pomocí prostředí PowerShell

> [!div class="op_single_selector"]
> * [Bran](dns-operations-dnszones-portal.md)
> * [Prostředí](dns-operations-dnszones.md)
> * [Rozhraní příkazového řádku Azure Classic](dns-operations-dnszones-cli-nodejs.md)
> * [Rozhraní příkazového řádku Azure](dns-operations-dnszones-cli.md)

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

Azure DNS podporuje i privátní zóny DNS.  Další informace o privátních zónách DNS najdete v tématu [použití Azure DNS u privátních domén](private-dns-overview.md). Příklad vytvoření privátní zóny DNS najdete v tématu Začínáme [s Azure DNS privátní zóny pomocí prostředí PowerShell](./private-dns-getstarted-powershell.md).

## <a name="get-a-dns-zone"></a>Získání zóny DNS

K načtení zóny DNS použijte rutinu `Get-AzureRmDnsZone`. Tato operace vrátí objekt zóny DNS odpovídající existující zóně v Azure DNS. Objekt obsahuje data o zóně (například počet sad záznamů), ale neobsahuje samotné sady záznamů (viz `Get-AzureRmDnsRecordSet`).

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

Vynecháte-li název zóny z `Get-AzureRmDnsZone`, můžete vytvořit výčet všech zón ve skupině prostředků. Tato operace vrátí pole objektů zóny.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Vynecháním názvu zóny a názvu skupiny prostředků z `Get-AzureRmDnsZone` můžete vytvořit výčet všech zón v rámci předplatného Azure.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-zone"></a>Aktualizace zóny DNS

Změny prostředku zóny DNS je možné provádět pomocí `Set-AzureRmDnsZone`. Tato rutina neaktualizuje žádné sady záznamů DNS v rámci zóny (viz [Správa záznamů DNS](dns-operations-recordsets.md)). Slouží pouze k aktualizaci vlastností samotného prostředku zóny. Vlastnosti zóny s možností zápisu jsou v současné době omezeny na [Azure Resource Manager značky pro prostředek zóny](dns-zones-records.md#tags).

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

Při použití `Set-AzureRmDnsZone` s objektem $zone se pro zajištění, že se nepřepisují souběžné změny, použijí [kontroly ETag](dns-zones-records.md#etags) . Pomocí volitelného přepínače `-Overwrite` můžete tyto kontroly potlačit.

## <a name="delete-a-dns-zone"></a>Odstranění zóny DNS

Zóny DNS je možné odstranit pomocí rutiny `Remove-AzureRmDnsZone`.

> [!NOTE]
> Odstraněním zóny DNS dojde také k odstranění všech záznamů DNS v rámci zóny. Tuto operaci nelze vrátit zpět. Pokud se zóna DNS používá, služby, které používají zónu, se při odstranění zóny nezdaří.
>
>Informace o ochraně před náhodným odstraněním zóny najdete v tématu [jak chránit zóny a záznamy DNS](dns-protect-zones-recordsets.md).


K odstranění zóny DNS použijte jeden z následujících dvou způsobů:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Zadejte zónu pomocí názvu zóny a názvu skupiny prostředků.

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Určení zóny pomocí objektu $zone

Můžete určit zónu, která se má odstranit, pomocí objektu `$zone` vráceného `Get-AzureRmDnsZone`.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

Místo předávání jako parametru je možné objekt zóny také přesměrovat:

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone

```

Stejně jako u `Set-AzureRmDnsZone`, určení zóny pomocí objektu `$zone` umožňuje kontrolám značek ETag zajistit, aby se neodstranily souběžné změny. Pro potlačení těchto kontrol použijte přepínač `-Overwrite`.

## <a name="confirmation-prompts"></a>Výzvy k potvrzení

Rutiny `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` a `Remove-AzureRmDnsZone` všechny podporují výzvy k potvrzení.

@No__t-0 a `Set-AzureRmDnsZone` se zobrazí výzva k potvrzení, pokud proměnná předvolby PowerShellu `$ConfirmPreference` má hodnotu `Medium` nebo nižší. Kvůli potenciálně vysokému dopadu odstranění zóny DNS se rutina `Remove-AzureRmDnsZone` vyzve k potvrzení, jestli má proměnná prostředí PowerShell `$ConfirmPreference` jinou hodnotu než `None`.

Vzhledem k tomu, že výchozí hodnota `$ConfirmPreference` je `High`, ve výchozím nastavení se zobrazí pouze @no__t – 2 pro potvrzení.

Aktuální nastavení `$ConfirmPreference` můžete přepsat pomocí parametru `-Confirm`. Pokud zadáte `-Confirm` nebo `-Confirm:$True`, rutina vás před spuštěním vyzve k potvrzení. Pokud zadáte `-Confirm:$False`, rutina vás nevyzve k potvrzení.

Další informace o `-Confirm` a `$ConfirmPreference` naleznete v tématu [o proměnných předvoleb](/powershell/module/microsoft.powershell.core/about/about_preference_variables).

## <a name="next-steps"></a>Další kroky

Naučte se [Spravovat sady záznamů a záznamy](dns-operations-recordsets.md) v zóně DNS.
<br>
Přečtěte si, jak [delegovat doménu na Azure DNS](dns-domain-delegation.md).
<br>
Přečtěte si [referenční dokumentaci k Azure DNS PowerShellu](/powershell/module/azurerm.dns).

